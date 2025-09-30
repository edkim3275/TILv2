# Python-for-AI Practice

## Open AI와 연결해서 벡터값 넣기

- 벡터 임베딩

  데이터를 기계가 이해할 수 있도록 숫자 벡터로 변환한 것(임베딩)

  testdb=# SELECT id, name, embedding <=> '[0.2, 0.3, 0.4]' AS similarity FROM product_embedding ORDER BY similarity;

  `embedding` : DB에 저장되어있는 임베딩 된 데이터

  `[0.2, 0.3, 0.4]` : 비교하고자 하는 대상(예 : 사용자 질문을 임베딩한 결과)

  비교군과 DB 데이터의 유사도 계산(의미적으로 유사한 값 찾기)

- venv 가상환경에 패키지 설치

  (myvenv) $ pip install openai psycopg2

- 테스트용 벡터 테이블 생성

  testdb=# CREATE TABLE test_vector (id SERIAL PRIMARY KEY, student_no varchar, name TEXT, embedding VECTOR(1536)); -- 여기선 openai를 사용하므로 벡터 크기를 1536으로 설정(:star2: **벡터 크기는 사용하고자 하는 LLM에 맞춰야한다.** )

- openAI_vector_insert.py

  ```python
  import openai
  import psycopg2
  import os
  
  STUDENT_NO="4"
  OPENAI_API_KEY="..."
  
  openai.api_key = OPENAI_API_KEY
  
  # DB 접속 정보
  DB_CONFIG = {
    'host': '자신의 DB서버 IP',
    'port': 5432, # postgres 포트는 주로 5432 사용
    'database': 'testdb',
    'user': 'test01',
    'password': 'test1234'
  }
  
  # 임베딩함수
  # text-embedding-3-small 사용하고자 하는 LLM 모델(이건 openai의 임베딩용 모델)
  def get_embedding(text: str, model: str = "text-embedding-3-small") -> list:
    response = openai.embeddings.create(
    	input=text,
      model=model
    )
    return response.data[0].embedding
  
  # DB에 저장
  def save_to_postgres(text: str, vector: list):
    conn = psycopg2.connect(**DB_CONFIG)
    cur = conn.cursor()
    try:
      sql="""
      INSERT INTO test_vector (student_no, text_str, embedding) VALUES (%s, %s, %s)
      """
      cur.execute(sql, (STUDENT_NO, text, vector))
      conn.commit()
      print("데이터 저장 완료.")
    except Exception as e:
      print("DB오류 :", e)
      conn.rollback()
    finally:
      cur.close()
      conn.close()
  
  # 메인 실행
  if __name__ == "__main__": # 예약어. 가장먼저 실행되는 부분
    input_text = input("임베딩할 문자를 입력하세요: ")
    embedding_vector = get_embedding(input_text)
    save_to_postgres(input_text, embedding_vector)
  ```

  <img width="1134" height="290" alt="image-20250930110953794" src="https://github.com/user-attachments/assets/c3031a75-659d-4a32-988c-b82e43ea11fa" />

## 벡터 검색(유사도 비교)

- SQL에서 SELECT로 검색한 방식은 스칼라 검색. 이제 벡터 검색을 해보자.

- openAI_vector_select.py

  ```python
  import openai
  import psycopy2
  import os
  
  OPENAI_API_KEY = " ... "
  openai.api_key = OPENAI_API_KEY
  
  DB_CONFIG = {
    ...
  }
  
  def get_embedding(text: str, model: str = "text-embedding-3-small") -> list:
    response = openai.embeddings.create(
    	input=text,
      model=model
    )
    return response.data[0].embedding
  
  def find_most_similar(text: str):
    # 임베딩 벡터 생성
    vector = get_embedding(text)
    # DB 연결
    conn = psycopy2.connect(**DB_CONFIG)
    cur = conn.cursor()
    try:
      # 비교 구문 작성
      sql = """
      SELECT text_str, embedding <-> %s AS distance FROM test_vector ORDER BY distance ASC LIMIT 1;
      """
      cur.execute(sql, (str(vector), ))
      result = cur.fetchone()
      
      if result:
        matched_text, similarity = result
        print(f"가장 유사한 문장: {matched_text}")
        print(f"거리 (낮을수록 유사): {similarity:.6f}")
      else:
        print("X 유사한 문장을 찾을 수 없습니다.")
    except Exception as e:
      print("DB 오류: ", e)
    finally:
      cur.close()
      conn.close()
  
  if __name__ == "__main__":
    user_input = input("유사한 문장을 찾을 문장을 입력하세요: ")
    find_most_similar(user_input)
  ```

  <img width="524" height="87" alt="image-20250930113750580" src="https://github.com/user-attachments/assets/baef4553-4080-4ccc-93a4-9037e56d325e" />

  :bulb: 만약 INSERT를 openAI를 사용했다면, 인터페이스를 통일해줘야하기 때문에 SELECT 또한 openAI를 사용해야한다.

  :bulb: 임베딩 대상이 꼭 문장이라는 법은 없고, 사운드, 이미지 등 모든 종류의 데이터를 숫자벡터로 변환하는 것을 임베딩이라고 표현한다.

## LLM 연동해서 질문하기(Gemini)

- 사전에 Gemini API_KEY 발급받기(https://app.apidog.com/main/APIHub)

- gemini-LLM.py

  ```python
  from openai import OpenAI
  import sys
  
  GEMINI_API_URL = "..."
  GEMINI_API_KEY = "..."
  LLM_ID = "gemini-2.0-flash-lite"
  
  client = OpenAI(
  	base_url = GEMINI_API_URL,
    api_key = GEMINI_API_KEY
  )
  
  def ai_chat(messages):
    print(f"GEMINI API 호출", MODEL={LLM_ID})
    response = client.chat.completions.create(
    	model=LLM_ID,
      messages=messages
    )
    return response
    
  if __name__ == "__main__":
    messages = []
    if len(sys.argv) < 3:
      print("Usage: ", sys.argv[0], "서울시의 면적과 인구를 알려줘")
      sys.exit()
    question = ''.join(sys.argv[1:])
    messages.append({"role":"user", "content":question})
    
    response = ai_chat(messages=messages)
    print(response.choices[0].message.content)
  ```

  :bulb: 환경변수 사용하기

  $ export OPENAI_API_KEY = '...' => 해당 세션에서 OPENAI_API_KEY를 환경변수로 사용가능 => 이를 영구 적용하기 위해서는 ~/.bashrc 또는 ~/.zshrc 파일에 추가해야하는데

  $ echo 'export OPENAI_API_KEY="..."' >> ~/.bashrc => ~/.bashrc 파일 마지막 줄에 `export OPENAI_API_KEY="..."`가 자동으로 추가하고

  $ source ~/.bashrc 를 통해 추가한 설정을 즉시 반영

  아래와 같은 방식으로 적용

  ```python
  import openai
  import os
  
  openai.api_key = os.getenv("GEMINI_API_KEY")
  ```

## Flask로 웹 서버 만들기

- 작업내용을 화면으로 보여주기위한 작업

- Flask는 python 웹 프레임워크 중 하나로써 사용전 가상환경에 설치해주자.

  (myvenv) $ pip install flask

- runserver.py

  ```python
  # 방식 1
  from flask import Flask
  
  app = Flask(__name__) # app 이라는 이름의 Flask 오브젝트 생성
  
  @app.route('/')
  def hello():
    html_out="""
    <html>
      <head>
        <title>Flask Example</title>
      </head>
      <body>
        <h1>Hello, Flask world!</h1>
        <p>this is a simple HTML page served by Flask.</p>
      </body>
  	</html>
    """
    return html_out
  # 0.0.0.0 : 모든 IP에서 접근 가능
  if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5004, debug=True)
  ```

  html은 template경로에 작성하고, python파일에는 해당 html을 불러오는 방식도 있음.

  ```python
  # 방식2
  from flask import Flask, render_template
  
  app = Flask(__name__)
  
  @app.route('/')
  def hello():
    return render_template('hello.html')
  
  if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5004, debug=True)
  ```

- app.py

  ```python
  import os
  import psycopg2
  from flask import Flask, render_template, request, g
  
  app = Flask(__name__)
  
  # DB 연결 정보
  DB_HOST = "localhost"
  DB_PORT = "5432" # 포트번호
  DB_NAME = "testdb" # DB이름
  DB_USER = "test01" # 사용자 이름
  DB_PASS = "test1234" # 비밀번호
  
  def get_db():
    # DB 연결 객체 생성 및 반환
    if 'db' not in g:
      g.db = psycopg2.connect(
      	host=DB_HOST,
        dbname=DB_NAME,
        user=DB_USER,
        password=DB_PASS,
        port=DB_PORT
      )
    return g.db
  
  @app.teardown.appcontext
  def close_db(error):
    # 요청 종료 시 DB 연결 닫기
    db = g.pop('db', None)
    if db is not None:
      db.close()
  
  @app.route("/")
  def index():
    # 데이터를 페이지네이션하여 표시
    page = request.args.get('page', 1, type=int)
    per_page = 10
    
    try:
      conn = get_db()
      cursor = conn.cursor()
      
      # 전체 항목 수 계산
      cursor.execute("SELECT COUNT(*) FROM test_vector;")
      total_items = cursor.fetchone()[0]
      total_pages = (total_items + per_page -1) // per_page
      
      # 현재 페이지에 해당하는 데이터 조회
      offset = (page - 1) * per_page
      query = """
      SELECT id, title, doc_body
      FROM test_vector
      ORDER BY id DESC
      LIMIT %s OFFSET %s;
      """
      cursor.execute(query, (per_page, offset))
      documents = cursor.fetchall()
    except psycopg2.Error as e:
      # 데이터베이스 연결 혹은 쿼리 오류 발생 시
      print(f"Database Error: {e}")
      documents = []
      total_pages = 0
    finally:
      if 'cursor' in locals():
        cursor.close()
    return render_template(
    	'index.html',
      documents=documents,
      page=page,
      total_pages=total_pages
    )
  
  if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5004, debug=True)
  ```

  