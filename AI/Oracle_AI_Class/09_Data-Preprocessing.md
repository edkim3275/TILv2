# Data Preprocessing

> AI Agent를 구성하는 단계는 전처리, 메인로직 처리, 후처리 세 단계로 나뉜다.
>
> 전처리 과정에서는 사용자가 입력하는 값(키보드 입력뿐만 아니라 고객이 입력한 내용, 인터넷에서 긁어온 내용, 다른 시스템에서 들어온 DB, 심지어는 엑셀파일, 종이문서 등)들을 가지고 메인로직이 처리할 수 있도록 사전에 데이터를 처리(토큰 분석, 벡터변환 등)하게 된다.
>
> 메인로직 처리에서는 실제 데이터를 가지고 우리가 제공하고자 하는 서비스에 맞는 편리한 로직이 들어가 있어서 적절한 AI를 선정하고, 프롬프트 구성등을 설계에 넣게 된다.
>
> 후처리 과정에서 처리한 데이터들을 사용자가 사용하기 쉽게 제공하면 된다.

## 스크래핑(데이터 수집)

- 스크래핑; 데이터(인사정보 시스템 DB, 개인 의료 기록, Excel 파일, 웹사이트 데이터 등) 수집
- 스크래핑 시 사용 대표 도구 selenium, beautifulsoup

### selenium

- 웹 브라우저를 제어하여 웹 애플리케이션 테스트를 위한 프레임워크이지만, 파이썬에서 동적 웹사이트 크롤링에 매우 유용하게 사용되며, **클라이언트에서 브라우저를 구동하여 브라우저가 가진 데이터를 가로채는 방식**으로 구동

- 사전작업

  - selenium 파이썬 패키지 설치

    $ pip install selenium

  - firefox 설치

    https://www.firefox.com/ko/browsers/desktop/mac/

  - firefox geckodriver 설치

### beautifulsoup

- HTML과 XML문서를 파싱하고 데이터를 추출하는 파이썬 라이브러리

- 웹 스크래핑에 주로 사용

- 사전작업

  - beautifulsoup 파이썬 패키치와 http 요청을 위한 requests 패키지 설치

    $ pip install beautifulsoup4 requests

    ```python
    import requests
    from bs4 import BeautifulSoup
    ```

### bs4를 이용한 HTML 파싱

- 세 가지 파싱 방법

  ```python
  import requests
  from bs4 import BeautifulSoup
  
  # HTML 문자열에서 파싱
  html_doc = "<html><body><h1>Hello World</h1></body></html>"
  soup = BeautifulSoup(html_doc, "html.parser")
  
  # 파일에서 파싱
  with open("example.html", "r") as f:
    soup = BeautifulSoup(f, "html.parser")
    
  # 웹에서 파싱
  url = "https://example.com"
  response = requests.get(url)
  soup = BeautifulSoup(response.text, "html.parser")
  ```

- 요소 찾기

  ```python
  import requests
  from bs4 import BeautifulSoup
  
  # 태그 이름으로 찾기
  title = soup.find('title')
  print(title.text)
  
  # 모든 태그 찾기
  all_links = soup.find_all('a')
  for link in all_links:
    print(link.get('href'))
          
  # css 선택자로 찾기
  # 클래스명을 가진 요소를 찾아서 리스트로 반환
  elements = soup.select('.class-name')
  
  # ID로 찾기
  # select_one은 첫 번째 요소 하나만 반환
  element = soup.select_one('#id-name')
  
  # 복합 선택자
  # 조건을 만족하는 p태그 찾기(부모 요소가 container클래스인 div태그이며, p태그 자체는 description클래스이며, 부모 div 바로 한 단계 아래만 선택)
  items = soup.select('div.container > p.description')
  # 모든 하위 p.description 찾기
  items = soup.select('div.container p.description')
  
  # 속성으로 찾기
  # 특정 속성이 있는 요소 찾기(img태그 중에서 src속성을 가진 모든 요소)
  images = soup.find_all('img', src=True)
  
  # 특정 속성 값으로 찾기(src='logo.png'인 img태그)
  soup.find('img', {'src': 'logo.png'})
  
  # 정규표현식 사용
  import re
  # img 태그중에서 src속성값이 .png로 끝나는 모든 태그
  images = soup.find_all('img', src=re.compile('\.png$'))
  ```

- 데이터 추출

  ```python
  # 텍스트 추출
  # 요소의 텍스트 추출
  title_text = soup.find('h1').text
  title_text = soup.find('h1').get_text()
  
  # 모든 텍스트 추출
  all_text = soup.get_text()
  
  # 속성값 추출
  # href 속성값 추출
  link = soup.find('a')
  url = link['href']
  url = link.get('href')
  
  # 여러 속성값 추출
  img = soup.find('img')
  src = img['src']
  alt = img.get('alt', '기본 텍스트') # alt가 없다면 '기본 텍스트' 반환
  
  # HTML 내용 추출
  # 내부 HTML(div 태그 안의 HTML 내부 내용을 문자열로 꺼내옴)
  inner_html = soup.find('div').decode_contents()
  
  # 요소 자체의 HTML
  outer_html = str(soup.find('div'))
  ```

### bs4로 스크래핑한 데이터를 DB에 넣어보기

- 우선은 스크래핑 후 엑셀로 저장해보기

  ```python
  import requests
  from bs4 import BeautifulSoup
  from openpyxl import Workbook
  
  url = "https://example.com"
  
  response = requests.get(url)
  response.raise_for_status()
  html = response.text
  soup = BeautifulSoup(html, "html.parser")
  tbody = soup.select_one('#container > div.aside > div.group_aside > div.aside_area.aside_popular > table > tbody')
  trs = tbody.select('tr')
  
  datas = []
  for tr in trs:
    name = tr.select_one('th > a').get_text()
    current_price = tr.select_one('td').get_text()
    change_direction = tr['class'][0]
    change_price = tr.select_one('td > span').get_text()
    datas.append([name, current_price, change_direction, change_price])
  
  write_wb = Workbook() # 새 엑셀 파일 생성
  write_ws = write_wb.create_sheet('결과') # '결과'라는 이름의 새로운 시트 추가
  datas.insert(0, ['이름', '현재가', '등락', '전일비']) # 컬럼명 추가
  
  for data in datas:
    write_ws.append(data)
  
  write_wb.save(r'naver_fin01.xlsx')
  ```

- DB에 넣기 전 테이블 만들기

  ```sql
  CREATE TABLE naver_stock (
  	id serial PRIMARY KEY, -- 행 구별할 고유 id 컬럼
    stock_name varchar null default null,
    stock_price int null default null,
    up_down varchar null default null,
    up_down_price int null default null,
    reg_date timestamptz default now()
  )
  ```

  `serial` : PostgreSQL에서 자동 증가 정수형(레코드가 추가될 때마다 자동으로 1씩 증가)

  `primary key` : 기본 키로 지정(중복 불가, null 불가)

  `not null` : 반드시 있어야 함

  `null` : 값이 없어도 허용

  `default null` : 값을 지정하지 않으면 자동으로 null로 저장

- 데이터 DB에 넣기

  ```python
  import psycopg2
  
  db_config = {
    'host': '...', # IP주소
    'port': 5432, # 포트번호
    'database': '...' # DB명
    'user': '...', # DB권한 유저
    'password': '...' # 유저 패스워드
  }
  
  conn = psycopg2.connect(**db_config)
  cursor = conn.cursor()
  insert_query = """
  	INSERT INTO naver_stock(stock_name, stock_price, up_down, up_down_price)
  	VALUES(%s, %s, %s, %s)
  """
  
  ...
  trs = tbody.select('tr')
  
  for tr in trs:
    name = ...
    current_price = ...
    change_direction = ....
    change_price = ...
    cursor.execute(insert_query, (name, int(current_price.replace(",",'')), change_direction, int(change_price.replace(",",''))))
  conn.commit()
  cursor.close()
  conn.close()
  ```

### selenium을 활용한 스크래핑

- requests를 통해서 웹 사이트의 데이터 스크래핑이 어려운 경우가 존재하는데, 이럴 경우 selenium을 활용하여 브라우저를 통해 직접 스크래핑을 할 수 있다.

- Firefox 사용 시

  ```python
  from selenium import webdriver
  from selenium.webdriver.firefox.options import Options as FirefoxOptions
  from bs4 import BeautifulSoup
  
  # Firefox 옵션 설정
  options = FirefoxOptions()
  # options.add_argument('--headless') # 헤드리스 모드(화면없이 진행)
  options.add_arguments('--no-sandbox')
  options.add_arguments('disable-dev-shm-usage')
  driver = webdriver.Firefox(options=options)
  
  # 웹 페이지 접속
  url = 'https://example.com'
  driver.get(url)
  
  # 페이지 소스 가져오기
  page_source = driver.page_source
  
  # BeautifulSoup으로 파싱
  soup = BeautifulSoup(page_source, "html.parser")
  
  # 요소 찾기 및 조작
  element = soup.find('#some-id')
  element.click() # 클릭
  element.send_keys('text') # 텍스트 입력
  element.clear() # 입력 내용 지우기
  
  # 드라이버 종료
  driver.quit()
  ```

- Chrome 사용시

  ```python
  from selenium import webdriver
  from webdriver_manager.chrome import ChromeDriverManager
  
  # Chrome 드라이버 설정
  options = webdriver.ChromeOptions()
  
  # 크롬 드라이버 초기화
  driver = webdriver.Chrome(ChromeDriverManager().install(), options=options)
  
  # 페이지 소스 가져오기 Firefox와 동일
  ```

### Firefox+selenium+bs4를 활용한 스크래핑

- 네이버 뉴스 페이지에서 데이터를 수집하여 DB에 넣어보기

  ```python
  from selenium from webdriver
  from selenium.webdriver.firefox.options import Options as FirefoxOptions
  from selenium.webdriver.common.by import By
  from selenium.webdriver.support.ui import WebDriverWait
  from selenium.webdriver.support import expected_conditions as EC
  from bs4 import BeautifulSoup
  import psycopg2
  
  def scrape_naver_news():
    options = FirefoxOptions()
    options.add_arguments('--no-sandbox')
    options.add_arguments('--disable-dev-shm-usage')
  
    try:
      driver = webdriver.Firefox(options=options)
      url = 'https://example.com'
      driver.get(url)
      
      # 페이지가 완전히 로드될 때 까지 대기
      WebDriverWait(driver, 10).until(EC.presence_of_element_located(By.CLASS_NAME, 'newsList'))
      
      # 추가로 약간의 대기 시간(동적 콘텐츠 로딩 위해)
      time.sleep(2)
      
      # 페이지 소스 가져오기
      page_source = driver.page_source
      
      # BeautifulSoup으로 파싱
      soup = BeautifulSoup(page_source, 'html.parser')
      
      # 주요 뉴스 목록 가져오기
      news_list = soup.find('ul', class_='newsList')
      
      if news_list:
        db_config = { ... }
    		conn = psycopg.connect(**db_config)
    		cursor = conn.cursor()
        insert_query = """
        	INSERT INTO naver_stock_news(news_title, url, content, press, published_at)
        	VALUES (%s, %s, %s, %s, %s)
        """
        news_items = news_list.find_all('li')
        for idx, item in enumerate(news_items, 1): # 1부터 시작
          # 제목 추출
          title_element = item.find('dd', class_='articleSubject')
          title = title_element.get_text(strip=True) if title_element else '제목 없음'
          # 링크 추출
          link_element = item.find('a')
          link = "https://example.com" + link_element['href'] if link_element and link_element.has_attr('href') else '링크 없음'
          # 요약 내용 추출
          content_element = item.find('dd', class_='articleSummary')
          content = content_element.get_text(strip=True) if content_element else '내용 없음'
          # 언론사 추출
          press_element = item.find('span', class_='press')
          press = press_element.get_text(strip=True) if press_element else '언론사 없음'
          # 작성 시간 추출
          time_element = item.find('span', class_='wdate')
          write_time = time_element.get_text(strip=True) if time_element else '시간 없음'
          cursor.execute(insert_query, (title, link, content, press, write_time))
          conn.commit()
           # db연결 종료
      		cursor.close()
      		conn.close()
      else:
        print("뉴스 목록을 찾을 수 없습니다.")
    except Exception as e:
      print(f"오류 발생: {e}")
    finally:
      # 드라이버 종료
      driver.quit()
      
  if __name__ == "__main__":
    scrape_naver_news()
  ```

- 위 실습도 마찬가지로 실행 전에 DB에 테이블을 생성해야한다.

  ```sql
  CREATE TABLE naver_stock_news (
  	id serial primary key,
    news_title text not null, 
    url text not null,
    content text, 
    press varchar(128), 
    published_at timestamptz,
    reg_date timestamptz default now()
  )
  ```

## 임베딩

### 임베딩

- 데이터 임베딩 혹은 벡터화는 컴퓨터가 이해하고 처리할 수 있도록 텍스트, 이미지, 소리 등 다양한 형태의 데이터를 고차원 벡터 공간의 숫자 벡터로 변환하는 과정

- 서로 다른 데이터 간의 관계와 유사성을 숫자를 통해 표현하기 때문에, 기계 학습 모델이 데이터를 더 효율적으로 학습하고 연산할 수 있도록 한다.

- 컴퓨터는 언어, 이미지와 같은 비정형 데이터를 직접 이해할 수 없다. 예를 들어 컴퓨터는 '강아지'라는 단어를 단지 문자로 인식할 뿐, '강아지'의 의미나 '고양이'와의 유사성을 알지 못한다. => 데이터 임베딩은 이러한 **비정형 데이터를 숫자로 변환하여 컴퓨터가 데이터의 의미와 관계를 파악할 수 있도록 만들어준다.**

- 임베딩의 종류

  - 텍스트 임베딩 : 텍스트 데이터를 벡터로 변환
    - One-Hot Encoding
    - Word2Vec
    - BERT(Bidirectional Encoder Representations from Transformers)
  - 이미지 임베딩 : 이미지 데이터를 벡터로 변환
    - CNN(Convolutional Neural Network) 기반 임베딩
    - CLIP(Contrastive Language-Image Pre-training)

- 임베딩 모델

  - 임베딩 모델이 따로 존재한다. 

  - Transformer 기반 모델(BERT, GPT 등)을 사용하며, 가장 진보된 모델임

  - 문장 전체의 맥락을 고려하여 단어를 벡터화 한다.

  - BERT : 문장의 앞뒤 맥락을 모두 고려하여 단어를 이해

    google-bert / koBERT 계열 / RoBERT 계열

  - GPT(Generative Pre-tained Transformer) : 주로 문장의 앞 부분을 통해 뒷부분을 예측하는 방식으로 학습

    GPT-3 / GPT-4 / GPT-4o / ChatGPT

  - Sentence Transformer : age-m3 / arctic-embed 등

### huggingface

- 임베딩 모델을 다운로드 하기 위해서 허깅페이스를 활용한다. 

- https://huggingface.co/

- public 모델의 경우 바로 사용이 가능하고 access 토큰을 발급받고 사용해야하는 모델도 존재함.

- 사전 작업

  - huggingface access token 발급

  - python 가상환경 생성

  - 가상환경 activate 후 huggingface_hub 설치

    (.venv) $ pip install huggingface_hub

  - 설치 후, huggingface에서 발급받은 token을 자신의 서버의 huggingface 환경에 설정한다.

    (.venv) $ huggingface-cli login --token hf_token_key

  - sentence_transformers 패키지 추가

    $ pip install -U sentence_transformers

  - 웹 서버 실행을 위한 unicorn 패키지 추가

    $ pip install unicorn

### huggingface를 통해 모델 다운받아서 임베딩하기

- 모델 다운로드 및 테스트 하기

  ```python
  from sentence_transformers import SentenceTransformer
  senetences = ["안녕하세요?", "한국어 문장 임베딩을 위한 버트 모델입니다."]
  
  model = SentenceTransformer('jhgan/ko-sroberta-multitask')
  embeddings = model.encode(sentences)
  print(embeddings) # 각각의 문장이 768 차원(dimension; dim)의 벡터로 변환된다.
  print("벡터 차원: ", len(embeddings))
  ```

  ```
  # 각각의 문장이 768차원의 벡터로 변환된다.
  # 유사한 의미의 문장은 유사한 값의 벡터로 변환된다.
  "안녕하세요" => [-0.3751049 -0.7733841 0.592771 ... 0.5792352 0.32683495 -0.6508965 ]
  "한국어 ... 모델입니다." => [-0.09361727 -0.18191542 -0.19230759 ... -0.03165785 0.30412525 -0.2679364 ]
  ```

### 사용자가 보낸 문장 임베딩해주는 웹 서비스 만들기

- 임베딩 서비스(Server)

  ```python
  # -*- coding: utf-8 -*-
  from fastapi import FastAPI, Request
  from sentence_transformers import SentenceTransformer
  from pydantic import BaseModel
  
  # FastAPI 애플리케이션 생성
  app = FastAPI()
  
  # 임베딩 모델 로드(앱 시작 시 한 번만 로드하여 효율성을 높인다)
  model = SentenceTransformer('jhgan/ko-sroberta-multitask')
  
  # 요청 본문을 위한 Pydantic 모델 정의
  class TextRequst(BaseModel):
    sentences: List[str]
    
  # 응답 본문을 위한 Pydantic 모델 정의
  class EmbeddingsResponse(BaseModel):
    embeddings: List[List[float]]
    
  # 임베딩을 수행하는 API 엔드포인드
  @app.post("/embed/", response_model=EmbeddingsResponse)
  def get_embeddings(request: TextRequst):
    # 모델이 이미 지정된 GPU에 로드되어 있으므로 별도의 설정 불필요
    embeddings = model.encode(request.sentences, convert_to_numpy=True)
    # numpy 배열을 python 리스트로 변환
    embeddings_list = embeddings.tolist()
    # pydantic 모델을 사용하여 응답 반환
    return {"embeddings": embeddings_list}
  ```

  ```bash
  # embedding_server.py로 저장 후 unicorn으로 실행
  unicorn embedding_server:app --reload --host=0.0.0.0 --port=5050
  ```

- 임베딩 서비스(Client)

  ```python
  # -*- coding: utf-8 -*-
  import requests
  
  # API 엔드포인트
  url = "http://localhost:5050/embed/"
  
  # POST 요청으로 전송할 데이터
  data = {
    "sentences": ["안녕하세요?", "한국어 문장 임베딩을 위한 버트 모델입니다."]
  }
  
  # request.post() 함수를 사용하여 POST 요청 보내기
  # json = data를 사용하면 자동으로 Content-Type: application/json 헤더가 설정됨
  try:
    response = requests.post(url, json=data)
    # 응답 상태 코드 확인
    response.raise_for_status() # 200번대 응답 아니면 예외
    # JSON 응답을 파이썬 딕셔너리로 변환
    embeddings_result = response.json()
    # 결과 출력
    print("API 응답 상태 코드:", response.status_code)
  	print("임베딩 결과:")
    for idx, embedding in enumerate(embeddings_result['embeddings'], 1):
      print(f"문장 {idx} 임베딩 벡터 길이: {len(embedding)}")
      print(f"임베딩 예시 (앞 5개): {embedding[:5]}")
    except requests.exception.RequestException as e:
      print(f"오류가 발생했습니다: {e}")
  ```

## TODO

- 데이터를 임베딩하여 벡터를 DB에 저장하기
- 증권 사이트 데이터 수집해서 DB에 넣고 검색해서 찾는 것
- Flask를 이용한 데이터 portal 만들기