# Tokenization

## 토큰화

- 자연어처리에서 크롤링 등으로 얻어낸 코퍼스(문장) 데이터가 필요에 맞게 전처리 되지 않은 상태라면, 해당 데이터를 사용하고자 하는 용도에 맞게 **토큰화(tokenization) & 정제(cleaning) & 정규화(normalization)** 해야한다.

- 토큰화(tokenization) : 주어진 코퍼스(corpus, 문장)에서 토큰(token)이라 불리는 단위로 분할하는 작업

  토큰(token) : 의미를 가진 최소단위

  :bulb: 토큰의 기준이 영어와 한글이 다를 수 있다.

- 정제(cleaning) : 토큰화 전, 후에 text data 노이즈를 제거하는 작업

- 정규화(normalization) : 표현방법이 다른 단어들을 통합시켜 동일한 단어로 변환하기. 문자에 fooled, jone's 와 같은 것이 있다면 fool, jone과 같이 본래 형태로 처리하는 것

- 토큰화에서 고려할 사항

  - 구두점, 특수문자를 단순 제외해서는 안 된다.(마침표 등으로 문장을 구분함)

  - 줄임말과 단어 내에 띄어씌기가 있는 경우

    New York ➡︎ 두단어지만 하나의 토큰, we’re ➡︎ 한단어 이나 We 와 are의 두개 token

## 토큰화 방식

### KSS

- 사전에 kss 가상환경에 설치하고 진행

  $ pip install kss

- KSS(Korean Sentences Splitter)

- 한국어

  한국어의 경우 띄어쓰기 단위가 되는 단위를 '어절'이라고 하는데 어절 토큰화는 한국어 NLP에서 사용하지 않는다. 한국어는 영어와 다른 교착어로서 어절과 단어가 다르다.

  1. 교착어 특징 : 영어의 he / him ➡︎ 한국에서는 '그'라는 단어 하나에도 '그가', '그에게', '그를', '그와', '그는' 등과 같이 다양한 조사가 '그'라는 글자 뒤에 띄어씍 없이 바로 붙게되며, 이를 위해 한국어는 단어분리 전 조사를 먼저 처리해주어야 한다.

  2. 형태소 : 한국어는 형태소 단위로 분리해야 하며, 형태소(morpheme)란 뜻을 가진 가장 작은 말의 단위를 의미

     자립형태소 : 접사, 어미, 조사와 상관없이 자립하여 사용할 수 있는 형태소. 그 자체로 단어가 된다. 체언(명사, 대명사, 수사), 수식언(관형사, 부사), 감탄사 등

     의존형태소 : 다른 형태소와 결합하여 사용되는 형태소. 접사, 어미, 조사, 어간 등

  3. 띄어쓰기 : 한국어 corpus에서는 띄어쓰기가 잘 지켜지지 않는다.(띄어쓰기를 하지 않아도 이해가 가능)

  4. 품사태깅 : 한국어에서는 단어 표기는 같지만 품사에 따라 단어의 의미가 달라지는 경우가 많다.

     예시) 못 먹는다. 못을 박는다.

- kss는 문장을 의미로 구분

  ```python
  import kss
  text="서울은 대한민국의 수도입니다. 오늘 점심은 어디서 먹을까요. 갑자기 배가 고픕니다."
  kss.split_sentences(text) # ['서울은 대한민국의 수도입니다.', '오늘 점심은 어디서 먹을까요.', '갑자기 배가 고픕니다.']
  
  # 온점 삭제 한 경우 확인
  text_without_fullstop = "서울은 대한민국의 수도입니다 오늘 점심은 어디서 먹을까요 갑자기 배가 고픕니다"
  kss.split_sentences(text_without_fullstop) # ['서울은 대한민국의 수도입니다', '오늘 점심은 어디서 먹을까요', '갑자기 배가 고픕니다']
  ```

### nltk

- 사전에 nltk 가상환경에 설치하고 진행

  $ pip install nltk

- nltk :영어 corpus tokenization용 도구

  ```python
  # nltk_test.py
  import nltk # 자연어 토크나이저
  nltk.download('punkt')
  nltk.download('punkt_tab')
  nltk.downlaod('averaged_perception_tageer')
  # 단어 토큰화 방식이 세 가지 존재 word_tokenize, WordPunchTokenizer, text_to_word_sequence
  # text_to_word_sequence가 가장 잘 맞아 보임
  from nltk.tokenize import word_tokenize
  from nltk.tokenize import WordPunchTokenized
  from tensorflow.keras.preprocessing.teext import text_to_word_sequence
  
  print('단어토큰화1 :',word_tokenize("Don't be fooled by the dark sounding name, Mr.Jone's Orphanage is as cheery ascheery goes for a pastry shop."))
  print('단어 토큰화2 :',WordPunctTokenizer().tokenize("Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop."))
  print('단어 토큰화3 :',text_to_word_sequence("Don't be fooled by the dark sounding name, Mr. Jone's Orphanage is as cheery as cheery goes for a pastry shop."))
  ```

  <img width="1747" height="52" alt="Image" src="https://github.com/user-attachments/assets/2e8911f7-a6fd-4801-b355-5d48ebe319fb" />

- 표준 토큰화 예시

  ```python
  from nltk.tokenize import TreebankWordTokenizer
  tokenizer = TreebankWordTokenizer()
  text = "Starting a home-based restaurant may be an ideal. it doesn't have a food chain or restaurant of their own."
  print('트리뱅크 워드토크나이저 :',tokenizer.tokenize(text))
  ```

- 문장 토큰화 예시

  문장단위 tokenization

  corpus를 문장단위로 구분하는 작업(sentence segmentation). 보통 마침표나, !, ? 등으로 구분하나, 해당 기호가 문장 중간에 등장하는 경우도 많음

  ```python
  from nltk.tokenize import sent_tokenize
  text = "His barber kept his word. But keeping such a huge secret to himself was driving him crazy. Finally, the barber went up a mountain and almost to the edge of a cliff. He dug a hole in the midst of some reeds. He looked about, to make sure no one was near."
  print("문장 토큰화1:", sent_tokenize(text))
  
  text = "I am actively looking for Ph.D. students. and you are a Ph.D student."
  print('문장 토큰화2 :',sent_tokenize(text))
  
  ```

- 단어 토큰화, 품사 태깅 예시

  ```python
  import nltk
  from nltk.tokenize from word_tokenize
  from nltk.tag import pos_tag
  nltk.download('punkt')
  nltk.download('punkt_tab')
  nltk.download('averaged_perceptron_tagger')
  nltk.downlaod('averaged_perceptron_tagger_eng')
  
  text = "I am actively looking for Ph.D students. and you are a Ph.D student."
  tokenized_sentence = word_tokenize(text)
  print("단어 토큰화 : ", tokenized_sentence)
  # 단어 토큰화 :  ['I', 'am', 'actively', 'looking', 'for', 'Ph.D', 'students', '.', 'and', 'you', 'are', 'a', 'Ph.D', 'student', '.']
  
  print("품사 태깅 : ", pos_tag(tokenized_sentence))
  # 품사 태깅 :  [('I', 'PRP'), ('am', 'VBP'), ('actively', 'RB'), ('looking', 'VBG'), ('for', 'IN'), ('Ph.D', 'NNP'), ('students', 'NNS'), ('.', '.'), ('and', 'CC'), ('you', 'PRP'), ('are', 'VBP'), ('a', 'DT'), ('Ph.D', 'NNP'), ('student', 'NN'), ('.', '.')]
  ```

  토큰 품사 태킹 실 사용 예시 : 댓글 수집 => 수집한 댓글에서 명사 태깅 => 심한욕에 감성 점수 부여

  위 사용은 AI활용 전에는 문장의 맥락을 파악하면서 점수가 부여되지 않아서 애로사항이 있었지만 AI는 글의 맥락을 이해하기 때문에 해당 댓글이 어떤 의도를 갖고 작성되었는지 파악가능

### konlpy

- 사전에 konlpy 가상환경에 설치하고 진행

  $ pip install konlpy

- 한국 형태소 분석 패키지 konlpy(여러가지 형태소 분석 패키지를 하나로 합친 패키지). Okt(OpenKoreaText), 메캅(Mecab), 코모란(Komoran), 한나눔(Hannanum), 꼬꼬마(Kkma) 등이 존재

  이전 부터 개발되어 온거라 자바로 작성된게 있다보니 자바 설치도 필요함

  $ sudo apt install openjdk-17-jdk

- 형태소 분석 예시

  ```python
  from konlpy.tag import Okt
  from konlpy.tag import Kkma
  from konlpy.tag import Mecab
  
  okt = Okt()
  kkma = Kkma()
  mecab = Mecab()
  
  text01 = "열심히 코딩한 당신, 연휴에는 여행을 가봐요."
  
  # okt
  okt.morphs(text01) # 형태소 분석
  okt.pos(text01) # 품사 태깅
  okt.nouns(text01) # 명사 추출
  
  # kkma => okt보다 좀 더 자세하게 분류 됨
  kkma.morphs(text01) # 형태소 분석
  kkma.pos(text01) # 품사 태깅
  kkma.nouns(text01) # 명사 추출
  
  # mecab
  mecab.morphs(text01)
  ```

  :bulb: 명사 추출은 키워드 추출에서 사용될 수 있음

### spacy

- 영문 토큰화의 경우 spacy, nltk를 주로 사용

- 토큰화 예시

  ```python
  import spacy
  
  en_text = 'A Dog Run back corner near spare bedrooms'
  en_text2 = 'I am actively looking for Ph.D students. and you are a Ph.D student.'
  spacy_en = spacy.load('en_core_web_sm')
  
  def tokenize(en_text):
    return [tok.text for tok in spacy_en.tokenizer(en_text)]
  
  tokenize(en_text)
  tokenize(en_text2)
  ```

## 자연어 처리

- 자연어 처리 순서 : 토큰화 ➡︎ 단어집합생성 ➡︎ 정수 인코딩 ➡︎ 패딩 ➡︎ 벡터화

### 불용어

- 불용어 stopwords

  불용어는 문장에 자주 등장하지만 의미 분석에서는 도움이 되지 않는 단어를 말함

  참고 : https://gist.github.com/spikeekips/40eea22ef4a89f629abd87eed535ac6a#file-stopwords-ko-txt

- 불용어 제거 예시

  ```python
  import nltk
  nltk.downlaod('sotpwords')
  nltk.downlaod('punkt')
  
  from nltk.corpus import stopwords
  from nltk.tokenize import word_tokenize
  from konlpy.tag import Okt
  
  stop_words_list = stopwords.words('english')
  print('불용어 개수 : ', len(stop_words_list)) # 198
  print('불용어 10개 출력 : ', stop_words_list[:10])
  example_text = "Family is not an important thing. It's everything."
  stop_words = set(stopwords.words('english'))
  
  word_tokens = word_tokenize(example_text)
  
  result = []
  for word in word_tokens:
    in word not in stop_words:
      result.append(word)
      
  print('불용어 제거 전 :', word_tokens)
  print('불용어 제거 후 :', result)
  
  okt = Okt()
  example = "고기를 아무렇게나 구우려고 하면 안 돼. 고기라고 다 같은 게 아니거든. 예컨대 삼겹살을 구울 때는 중요한 게 있지."
  stop_words = "를 아무렇게나 구 우려 고 안 돼 같은 게 구울 때 는"
  
  stop_words = set(stop_words.split(''))
  word_tokens = okt.morphs(example)
  result = [word for word in word_tokens if not word in stop_words]
  
  print('불용어 제거 전 :', word_tokens)
  print('불용어 제거 후 :', result)
  ```

### 자연어 처리 실습

> 네이버 영화 리뷰에서 단어 빈도수 파악하기

- 사전 작업

  ```
  # 컴파일 시 필요한 툴
  $ sudo apt install build-essential
  $ pip install spacy
  $ python -m spacy download en
  $ sudo apt install openjdk-17-jdk
  $ pip install konlpy
  $ pip install mecab-python
  $ bash <(curl -s https://raw.githubusercontent.com/konlpy/konlpy/master/scripts/mecab.sh)
  ```

- 자연어 처리 실습

  리뷰 => 토큰 => 불용어 제거 => 자주 쓰이는 단어에 대한 빈도수 확인 => 단어 숫자로 변환

  ```python
  import urllib.request
  import pandas as pd
  from konlpy.tag import Mecab
  from nltk import FreqDist
  import numpy as np
  import matplotilb.pyplot as plt
  
  # 해당 url에 접근해서 filename으로 정한 이름으로 저장
  urllib.request.urlretrieve("https://raw.githubusercontent.com/e9t/nsmc/master/ratings.txt", filename="ratings.txt")
  df = pd.read_table('ratings.txt') # 데이터 프레임에 저장
  print("전체 샘플 수 {}".format(len(df)))
  sample_data = df[:100] # 임의로 100개만 저장
  # 정규표현식을 이용하여 한글/공백만 남기고 제거
  sample_data['document'] = sample_data['document'].str.replace("[^ㄱ-ㅎㅏ-ㅣ가-힣]","",regex=True)
  sample_data[:10] # 잘 제거되었는지 확인
  
  # 토큰화 전에 불용어 제거
  stopwords=['의','가','이','은','들','는','좀','잘','걍','과','도','를','으로','자','에','와','한','하다']
  
  tokenizer = Mecab()
  tokenized = []
  for sentence in sample_data['document']:
    temp = tokenizer.morphs(sentence)
    temp = [word for word in temp if not word in stopwords]
    tokenized.append(temp)
  print(tokenized[:10]) # 불용어 제거 됐는지 확인
  
  # 단어집합 생성, FreqDist(빈도수 계산용 함수)
  # tokenized : 문장 단위로 토큰화 된 리스트들의 리스트
  # np.stack(tokenized) : 리스트들을 옆으로 이어 붙이는 함수. 2차원 배열(리스트의 리스트)을 1차원으로 평탄화(flatten)하는 효과 => 즉, 여러 문장의 단어들을 하나의 긴 리스트로 합치는 과정
  # FreqDist(...) : nltk에서 제공하는 단어 빈도수 계산기. 리스트의 각 단어가 몇 번 등장했는지 딕셔너리 형태로 관리. 즉, 전체 코퍼스에서 단어별 등장 횟수를 센 결과. nltk.probability.FreqDist 객체 반환
  vocab = FreqDistance(np.hstack(tokenized))
  print('단어 집합의 크기 : {}'.format(len(vocab)))
  vocab['재밌']
  
  # 상위 vocab_size개의 단어만 보존
  vocab_size = 500
  vocab = vocab.most_common(vocab_size)
  print('단어 집합의 크기 : {}'.format(len(vocab)))
  
  # 생성된 단어에 고유 값 부여
  # 단어 사전 만들기
  # enumerate(vocab) : (index, (단어, 빈도)) 형태로 꺼내짐
  # [(0, ('오늘', 3)), (1, ('날씨', 2)), (2, ('좋다', 2)), (3, ('비', 1)), (4, ('오다', 1))]
  # 딕셔너리 컴프리핸션
  # word[0] : ('오늘', 3) 중에서 '오늘'
  # index + 2 : 인덱스를 0부터 매기지만 2부터 시작
  word_to_index = {word[0]: index + 2 for index, word in enumerate(vocab)}
  word_to_index['pad'] = 1 # 길이가 다른 문장을 같은 길이로 맞출 때 빈 부분을 채우는 용도
  word_to_index['unk'] = 0 # 단어 사전에 없는 단어가 들어왔을 때 대신 쓰는 토큰
  
  encoded = []
  for line in tokenized:
    temp = []
    for w in line:
      try:
        temp.append(word_to_index[w]) # 글자를 해당되는 정수로 변환
      except KeyError: # 단어 집합에 없는 단어일 경우 unk로 대체
        temp.append(word_to_index['unk'])
    encoded.append(temp)
  print(encoded[:10]) # 문장을 정수 인덱스 리스트로 바꾼 상태 확인(기계는 단어를 알아 들을 수 없기때문에 토큰을 숫자로 1대1로 매핑해준다. 이렇게 해주면 동음이의어 현상도 다른 인덱스로 assign되기 때문에 방지할 수 있다)
  
  # 길이가 다른 문장들을 모두 동일한 길이로 변환
  max_len = max(len(l) for l in encoded) # 가장 긴 문장의 길이 구하기
  min_len = min(len(l) for l in encoded) # 가장 짧은 문장의 길이 구하기
  avg_len = sum(map(len, encoded)) / len(encoded) # 평균 길이 = 전체 토큰의 개수 / 문장 개수
  print('리뷰의 최대 길이 : %d' % max_len)
  print('리뷰의 최소 길이 : %d' % min_len)
  print('리뷰의 평균 길이 : %d' % )
  
  # 길이가 다른 문장들 동일한 길이로 맞추기
  for line in encoded:
    if len(line) < max_len:
      line += [word_to_index['pad']] * (max_len - len(line))
      
  print('리뷰의 최대 길이 : %d' % max(len(l) for l in encoded))
  print('리뷰의 최소 길이 : %d' % min(len(l) for l in encoded))
  print('리뷰의 평균 길이 : %f' % (sum(map(len, encoded))/len(encoded)))
  print(encoded[:3])
  ```

- padding

  실습에서 길이가 다른 문장들을 모두 동일한 길이로 변환하는데 이것이 자연어 처리에서 "패딩(padding)"을 하는 핵심 이유다.

  1. 딥러닝 모델 입력은 고정 크기 행렬이어야 한다.

     RNN, LSTM, GRU, Transformer 같은 모델에 데이터를 넣으려면 동일한 크기의 벡터/행렬이 필요하다. 여기서 문장은 길이가 제각각이라 그냥 넣을 수 없다.

     ```python
     [2, 5, 6]         # 길이 3
     [7, 8]            # 길이 2
     [1, 9, 4, 3, 6]   # 길이 5
     ```

     이러한 리스트들을 바로 행렬로 만들 수 는 없다.(파이썬 리스트는 가능하지만, 넘파이 / 텐서로 변환하려면 shape이 맞아야한다.)

     그래서 가장 긴 문장의 길이(max_len)에 맞춰서 짧은 문장은 pad 토큰으로 채워서 길이를 통일시킨다.

     ```python
     max_len = 5
     encoded = [
       [2, 5, 6, 1, 1],   # pad 추가 (길이 3 → 5)
       [7, 8, 1, 1, 1],   # pad 추가 (길이 2 → 5)
       [1, 9, 4, 3, 6]    # 이미 길이 5
     ]
     ```

     위와 같이 문장들을 동일한 길이로 변환하면 넘파이 배열이나 텐서로 변환이 가능하다.

  2. GPU에서 효율적인 병렬 학습을 하기 위함

     **딥러닝 모델은 GPU에서 행렬 연산으로 학습**한다. 길이가 다르면 문장마다 따로 돌려야 해서 비효율적이고 이는 학습 속도 저하로 이어진다.

     패딩을 통해 모든 샘플을 같은 길이로 맞추면 한 번에 묶어서(batch)처리가 가능해서 훨씬 빨라진다.

  3. 문장 간 비교/처리를 일관되게 하기 위해(일관성 있는 인덱스 매핑)

     패딩을 하면 인덱스 위치가 고정돼서 모델이 순서 정보를 일관성 있게 학습할 수 있다.

     즉, 배치 길이를 동일하게 맞췄다면 같은 배치 내에서 동일 인덱스가 "같은 위치"를 의미하게 된다. 예를들어 배치 길이가 6이라면 인덱스 0은 문장 시작 토큰 자리, 인덱스 5는 끝/패딩자리

     모델은 pad는 의미가 없음을 마스크(attention_mask)로 알려주어 무시하게 한다(tranformer), 혹은 시퀀스 길이를 줄여가며 처리(RNN계열) 한다.

  :bulb: 실제로는 for문을 돌려서 패딩하기보다는, `keras.preprocessing,sequence.pad_sequences()`같은 유틸 함수를 자주 사용한다. keras유틸 사용시 직접 for문 돌리지 않아도 된다.

  ```python
  from tensorflow.keras.preprocessing.sequence import pad_sequence
  
  # 예시: 서로 길이가 다른 시퀀스
  encoded = [
      [2, 5, 6],          # len 3
      [7, 8],             # len 2
      [3, 4, 9, 2, 10]    # len 5
  ]
  
  # 최대 길이를 5로 맞추고, 부족한 부분은 pad 값 1로 뒤쪽에 채움
  pad_sequence(encoded,
    maxlen=5,
    dtype='int32',
    padding='post', # 앞: 'pre' / 뒤: 'post'
    truncating='post' # 너무 긴건 뒤에서 자르기
    value=1 # pad 토큰 ID
  )
  
  print(padded) # 결과 (shape: (3, 5))
  ```

  

## TIP :bulb:

- DB에 데이터를 넣는 도중 일시중단 하고 싶다면?

  `^ + s`(control + s)

  다시 재개하고 싶다면 `^ + q`

- map(func, interable)

  iterable의 각 원소에 func 함수를 적용

  예) map(len, encoded) : encoded 안에 있는 각 문자의 길이를 구해줌

  ```python
  encoded = [[2,3,4], [5,6], [2,7,8,9,10]]
  
  list(map(len, encoded))
  # [3, 2, 5]
  ```

-  RNN / LSTM / GRU / Transformer에 데이터를 넣는다는 의미

  이 모델들은 문장을 입력으로 받아 분류/감성분석/요약/번역 같은 작업을 수행하는 딥러닝 모델들이다.

  입력 형식은 사람이 읽는 "단어"가 아니라 숫자 시퀀스(토큰 ID) 혹은 그걸 더 잘 표현한 임베딩 벡터임.

  흐름은 아래와 같음

  1. 텍스트 ➡︎ 토큰화 ➡︎ 단어표(word_to_index)로 정수 시퀀스로 변환
  2. 길이 통일(패딩)
  3. (선택사항) 임베딩 레이어를 거쳐 밀집벡터 시퀀스로 변환
  4. 모델(RNN/LSTM/GRU/Transformer)로 넣어 학습/추론 수행

- 배치(batch)로 묶어서 학습

  딥러닝은 보통 데이터 전체를 한 번에 학습하는 것이 아니라 일정 크기로 잘라서 학습하는데(데이터가 수십만 문장일 경우 GPU 메모리에 다 들어가질 않고, 배치 단위로 학습하게 되면 속도 최적화 + 일반화 성능 향상) 이 잘린 덩어리를 배치(batch)라고 부른다.

  예를들어 데이터가 10000문장이고 배치 크기가 32라면 한 번 학습할 때 모델 입력은 32문장 묶음이라는 것이 된다.

- 넘파이 배열과 텐서

  - encoded된 리스트가 있다면 이를 넘파이 배열 혹은 텐서로 변환해준다.

  - 넘파이 배열(np.array) : 숫자 행렬/텐서를 표현하는 파이썬 표준 라이브러리

  - 텐서 : 딥러닝 프레임워크가 쓰는 다차원 배열(파이토치 torch.Tensor, 텐서플로 tf.Tensor)

    - GPU 연산, 자동미분 등을 제공하기 때문에 모델에 넣으려면 결국 텐서로 바꾼다.

  - 예시

    ```python
    import numpy as np
    import torch
    import tensorflow as tf
    
    encoded = [
      [2, 5, 6, 1, 1],   # pad=1
      [7, 8, 1, 1, 1],
      [3, 4, 9, 2, 1],
    ]
    
    # 1) 리스트 -> 넘파이
    arr = np.array(encoded, dtype=np.int64) # shape: (3, 5)
    # 2-a) 넘파이 -> 파이토치 텐서
    torch_tensor = torch.from_numpy(arr) # dtype: torch.int64
    # 2-b) 넘파이 -> 텐서플로 텐서
    tf_tensor = tf.convert_to_tensor(arr) # dtype: tf.int64
    ```

- 임베딩 차원 128 = 단어 하나를 128개의 숫자로 표현한다는 뜻

  문장 길이가 5(5개의 단어)라면 결과 shape = (5, 128) 5개의 단어, 128차원 벡터

  딥러닝에서 보통 말하는 텐서는 "n차원 배열"을 통칭하는 용어. 즉, 3차원 이상이면 무조건 텐서

  딥러닝에서 데이터의 구조

  ```
  배치 크기 × 문장 길이 × 임베딩 차원
  ```

  - 단어 하나 → shape=(128,) (128차원 벡터)
  - 문장 (단어 5개) → shape=(5,128) (5개의 단어 × 각 단어 128차원)
  - 문장 여러 개 (배치 크기 32) → shape=(32,5,128) (배치 크기 32, 문장길이 5, 임베딩 128)

  배치(batch) : 여러 샘플(문장)을 한 번에 묶어서 모델에 넣는 단위

  ```python
  import numpy as np
  
  seq_len =5
  embedding_dim = 128
  batch_size = 32
  
  # 넘파이
  np_sentence = np.random.randn(seq_len, embedding_dim) # 문장 1개
  np_batch = np.random.randn(batch_size, seq_len, embedding_dim) # 문장 여러개
  
  np_sentence.shape # (5, 128)
  np_batch.shape # (32, 5, 128)
  ```

  