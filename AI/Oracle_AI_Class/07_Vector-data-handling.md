# Vector-data-handling

- :dizzy: 진행내용

  - RAG 기본 원리
  - Python 실습(pandas, numpy, matplotlib)
  - 정규표현식(regular expression)
  - 자연어처리, 토큰화(tokenization)

  => 사용자의 질문(텍스트, 음성, 이미지, 영상 등)

## RAG

- 검색 증강 생성. RAG. Retrieval-Augmented Generation

  LLM 답변 정확성, 최신성을 위해 답변 생성 전에 **외부 지식 베이스에서 관련 정보를 먼저 검색하여 추가하는 기술**. (예를들어 Python 실습전에 DB 데이터(벡터 데이터)를 쌓는 것)

### Python 응용 실습(RAG 기본 원리)

- RAG에서 필요한 정보를 DB에 넣고(openAI_vector_insert.py), 사용자가 질문을 하면 해당 질문을 임베딩하여 DB에 있는 데이터와 유사도 비교를 하여 유사한 문장을 검색. 유사한 문장과 사용자 질문을 포함하여 ChatGPT에게 질문하여 답변 받기

- rag_run.py

  ```python
  import openai
  import psycopg2
  import os
  
  STUDENT_NO='4'
  OPENAI_API_KEY="..."
  openai.api_key = OPENAI_API_KEY
  
  DB_CONFIG = {
    ...
  }
  
  # 임베딩 함수
  def get_embedding(text: str, model: str="text-embedding-3-small") -> list:
    response = openai.embeddings.create(
    	input=text,
      model=model
    )
    return response.data[0].embedding
  
  # 유사한 문장 검색
  def find_most_similar(question_vector):
    # DB 연결
    conn = psycopg2.connect(**DB_CONFIG)
    cur = conn.cursor()
    try:
      sql = """
      	SELECT text_str, embedding <-> %s AS distance
      	FROM test_vector
      	ORDER BY distance ASC
      	LIMIT 3;
      """
      cur.execute(sql, (str(question_vector), ))
      result = ""
      rows = cur.fetchone()
      for idx, row in enumerate(rows):
        result += f"{idx}. {row[0]} \n"
      return result
    finally:
      cur.close()
      conn.close()
      
  # ChatGPT 호출
  def ask_chatgpt(context: str, question: str, model="gpt-3.5-turbo"):
    prompt = f"""
    다음 문장을 참고하여 질문에 답해주세요.
    [참고 문장]
    {context}
    [질문]
    {question}
    """
    print("prompt(START)--------------------------------------\n")
  	print(prompt)
  	print("prompt(END)--------------------------------------\n")
    response = openai.chat.completion.create(
    	model=model,
      messages=[
        {"role":"system", "content":"당신은 친절한 지식 도우미입니다."},
        {"role":"user", "content":prompt}
      ],
      temperature:0.7
    )
    return response.choices[0].message.content.strip()
  
  # 전체 흐름 실행
  if __name__ == "__main__":
    user_question = input("질문을 입력하세요: ")
    # 1. 사용자 질문 임베딩
    question_vector = get_embedding(user_question)
    # 2. 유사한 문장 검색
    context_text = find_most_similar(question_vector)
    
    if context_text:
      print(f"\n [검색된 문맥정보]\n{context_text}")
      # 3. ChatGPT에 전달하여 답변 받기
      answer = ask_chatgpt(context_text, user_question)
      print(f"\n ChatGPT의 답변:\n{answer}")
    else:
      print("X 유사한 문장을 찾을 수 없습니다.")
  ```

## 데이터 분석 기초

- 데이터 분석 기초

  데이터 분석을 위한 필수 패키지 3대장 : pandas, numpy, matplotlib

### pandas

- 가상환경에 pandas 설치

  (myvenv) $ pip install pandas

  ```python
  import pandas as pd
  ```

- 시리즈(series) : 1차원 리스트

  시리즈(series) 클래스는 1차원 배열의 값(value)에 각 값에 대응하는 인덱스(index)를 부여할 수 있는 구조

  ```python
  import pandas as pd
  
  sr = pd.Series([10000, 15000, 24000], index=['pizza', 'chicken', 'burger'])
  print(sr['pizza']) # 10000
  print(sr.iloc[0]) # 10000
  ```

- 데이터프레임(dataframe) : 2차원 리스트

  데이터프레임은 2차원 리스트를 매개변수로 전달. 데이터프레임은 열(column), 인덱스(index), 값(values)로 구성. `.SCV` 같은 2차원 데이터를 편리하게 다루기 위한 구조

  ```python
  values = [[1,2,3],[4,5,6],[7,8,9]]
  index = ['one', 'two', 'three']
  columns = ['A', 'B', 'C']
  
  df = pd.DataFrame(values, index=index, columns=columns)
  ```

- 데이터프레임 생성(딕셔너리)

  :bulb: 데이터프레임은 리스트, 시리즈, 딕셔너리, numpy의 ndarrays 또 다른 데이터프레임으로부터 생성할 수 있다.

  ```python
  data = {
    '학번': ['2000', '2001', '2002', '2003', '2004', '2005'], 
    '이름': ['홍길동', '전우치', '이이돌', '이삼돌', '이사돌', '이오돌'], 
    '점수':[90, 91, 92, 93, 94, 95]
  }
  df = pd.DataFrame(data) # key값들이 열에 들어가게 됨
  print(df.loc[0]) # 0번째 행의 정보 : 2000 홍길동 90
  print(df.loc[0]['학번']) # 0번째 행의 '학번' 컬럼에 해당하는 부분의 값
  print(df['학번']) # '학번' 컬럼의 정보
  print(df['학번'][0]) # '학번' 컬럼의 0번째 행에 해당하는 값 2000
  ```

  `df.loc[0]` : 라벨 기반 인덱싱. 

- 파일 불러오기

  ```python
  # excel파일(.xlsx)을 읽어서 DataFrame객체로 변환
  df = pd.read_excel(io='USA_healthcare_dataset.xlsx', sheet_name='Sheet')
  
  df.head(5) # 5개만 가져오기
  
  df.tail(5) # 뒤에서 5개 가져오기
  
  df.loc[1]
  
  len(df) # df 길이
  
  # DB에 넣기
  for i in range(len(df)):
    # row 뽑기(i번 환자의 정보 뽑기)
    row = df.loc[i]
  ```

- pandas로 읽은 데이터를 DB에 넣어보기

  ```python
  import psycopg2
  from datetime import datetime
  import pandas as pd
  
  DB_CONFIG = { ... }
  try:
    conn = psycopg2.connect(**DB_CONNECT)
    cursor = conn.cursor()
    # insert DB
    insert_query = """
    	INSERT INTO (
    		Name ,Age ,Gender ,Blood_Type ,Medical_Condition ,
    		Date_of_Admission ,Doctor ,Hospital ,Insurance_Provider ,Billing_Amount ,
    		Room_Number ,Admission_Type ,Discharge_Date ,Medication ,Test_Results
    	)
      VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
    """
    df = pd.read_excel(io="USA_healthcare_dataset.xlsx", sheet_name="Sheet1")
    
    for i in range(len(df)):
      cursor.execute(insert_query, (
          str(df.iloc[i]['Name']),
          int(df.iloc[i]['Age']),
          str(df.iloc[i]['Gender']),
          str(df.iloc[i]['Blood_Type']),
          str(df.iloc[i]['Medical_Condition']),
          df.iloc[i]['Date_of_Admission'],
          str(df.iloc[i]['Doctor']),
          str(df.iloc[i]['Hospital']),
          str(df.iloc[i]['Insurance_Provider']),
          float(df.iloc[i]['Billing_Amount']),
          str(df.iloc[i]['Room_Number']),
          str(df.iloc[i]['Admission_Type']),
          df.iloc[i]['Discharge_Date'],
          str(df.iloc[i]['Medication']),
          str(df.iloc[i]['Test_Results'])
      ))
    conn.commit()
    
  	cursor.execute("""
  		select id, Name, Age, Gender from usa_health_data order by id desc limit 10
  	""")
    rows = cursor.fetchall()
    for row in rows:
      print(row[0], ',\n', row[1], ',', row[2], ',\n', row[3])
      
    except Exception as e:
      print("Error:", e)
      
    finally:
      if cursor:
        cursor.close()
      if conn:
        conn.close()
  ```

  

### numpy

- numpy

  array다룰때 자주 사용하는 도구. 실제로는 텐서 계산할 때 넘파이 자주 사용함

  사전에 numpy 설치하기 `pip install numpy`

- numpy 기초

  ```python
  import numpy as np
  
  # 1차원 벡터(벡터)
  vec = np.array([0.1, 0.2, 0.3, 0.4])
  
  # 2차원 벡터(매트릭스)
  mat = np.array([[10, 20, 30], [40, 50, 60]])
  mat[0] # 행
  
  type(mat) # numpy.ndarray
  
  vec.ndim # 차원 확인 => 1
  mat.ndim # 차원 확인 => 2
  
  # 초기화
  zero_mat = np.zeros((2, 3)) # 2행 3열짜리 매트릭스를 0으로 초기화
  zero_mat # 2행 3열짜리 매트릭스
  same_val = np.full((2,3), 0.7) # 2행 3열짜리 매트릭스 value가 0.7로 채우기
  eye_mat = np.eye(3) # 대각선으로 1인 행렬
  
  # 행렬에서 바로 벡터 만들기
  aa=np.arange(30) # 행렬
  vec = np.array(aa) # 0~29 행렬로 만든 벡터
  reshape = vec.reshape((3, 10)) # 벡터를 3행 10열짜리 행렬로 reshape
  ```

- numpy 슬라이싱

  ```python
  mat = np.array([[1, 2, 3], [4, 5, 6]]) # 2행 3열짜리 행렬
  mat=np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]) # 4행 3열짜리 행렬
  
  # python 리스트에서 사용하는 슬라이싱 적용가능
  mat[:]
  mat[2]
  mat[:,1] # 행은 전체, 열은 두 번째 열 => array([2, 5, 8, 11])
  mat[1:, 2] # 행은 두 번째 부터, 열은 세 번째 열 => array([ 6,  9, 12])
  mat[2:, 2:] # 행은 두 번째 이후로, 열도 두 번째 이후로 => array([[ 9], [12]])
  ```

- numpy 연산

  ```python
  x = np.array([1, 2, 3])
  y = np.array([4, 5, 6])
  result = x + y # array([5, 7, 9]) 차원이 그대로 유지됨
  result2 = x - y # array([-3, -3, -3])
  result3 = x * 5 # array([ 5, 10, 15])
  
  mat1 = np.array([[1, 2], [3, 4]])
  mat2 = np.array([[5, 6], [7, 8]])
  np.dot(mat1, mat2) # 행렬곱 => array([[19, 22],[43, 50]])
  ```

### matplotlib

- Matplotlib

  시각화해서 표현하기 위한 라이브러리. 못한다고해서 크게 지장이 있는건아니지만 리눅스에서 그래프 그려볼 수 있음

  콘솔에서 작업할 수 없음.

  사전에 matplotlib 설치 `pip install matplotlib`

- test

  ```python
  import matplotlib.pyplot as plt
  
  plt.title('test')
  plt.plot([1, 2, 3, 4], [2, 4, 8, 6])
  plt.xlabel("hours")
  plt.ylabel("score")
  plt.show()
  ```