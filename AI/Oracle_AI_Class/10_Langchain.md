# Langchain

## Langchain

- LLM(Large Language Model) 기반 애플리케이션 개발을 위한 오픈소스 프레임워크
- LLM이 더 강력하고 유연하게, 그리고 스스로 외부와 소통하며 행동할 수 있도록 돕는 도구
- LLM의 한계
  - 기억력 부재 => 맥락(context)이 유지되질 않음
  - 외부 데이터 접근 불가
  - 복잡한 작업 수행의 어려움
- Langchain의 역할
  - Memory : 대화 기억
  - Retrival : 외부 데이터 활용
  - Agent & Tool : 자율적 행동
- Langchain의 핵심 기능
  - Model I/O (프롬프트, 모델, 파서)
  - Retrieval (데이터 로딩, 분할, 벡터화)
  - Chains (컴포넌트 연결, LCEL)
  - Agents (도구 사용, 자율 행동)
  - Memory (대화 맥락 기억)

## LangChain Expression Language(LCEL)

- LLM과 다른 컴포넌트들을 파이프라인처럼 순차적으로 연결하여 하나의 작업을 완성하는 것

- 가장 기본적인 체인의 구조

  프롬프트 + 모델 + 출력 파서

  ```
  PromptTemplate | ChatModel | OutputParser
  ```

  프롬프트에서 LLM에게 보낼 질문의 양식을 정의하고, 질문을 받아 답변을 생성할 모델은 선택하고, 출력 파서로 모델이 생성한 답변을 사용자 맞춤형으로 정제하여 출력

- LCEL 멀티 체인 구조

  체인은 하나만 존재할 수도있지만 하나의 체인에서 나온 결과를 다음 체인의 입력으로 사용하는, 즉 여러 체인을 순차적으로 연결하여 더 복잡한 작업을 수행하는 것이 가능하다.

## Runnable

- Langchain이 "왜 유연한 구조"인지 알 수 있는 부분

- 요약하면 Runnable은 "입력을 받아서 결과를 내는 표준형 인터페이스". Langchain을 구성하는 모든 컴포넌트는 Runnable 인터페이스를 구현하고 있다. 따라서 모든 컴포넌트가 동일 메서드를 통해 실행될 수 있고 파이프 연산자로 연결되어 하나의 "체인"처럼 동작할 수 있다.

- Runnable; 실행할 수 있는 것. Langchain에서는 "입력(input)"을 받아서, 어떠한 처리를 한 뒤, "결과(output)"를 내는 모든 것은 Runnable이라고 부른다. 간단하게 

- LLM, PromptTemplate, Chain, Tool, OutputParser ... 모두 Runnable

- Runnable의 아이디어는 내부적으로 **모든 요소들을 하나의 공통 인터페이스**로 묶어버린 것. 그래서 각각이 아래의 공통 메서드를 가진다.

  - invoke() : 답변 한번에 출력
  - stream() : 실시간 스트리밍으로 답변 출력
  - batch() : 여러 입력에 대해 한 번에 답변하는 형태

- 모든 종류의 객체들이 Runnable 프로토콜을 따르기 때문에 `|`(파이프) 연산자 하나로 간단하게 연결 가능

  ```
  chain = promp | llm | outputparser
  ```

  Prompt, Model, OutputParser, Chain ... 모든 요소가 Runnable 인터페이스를 가지고 있기 때문에 이들을 조합할 때 특별한 문법이나 함수를 다르게 쓸 필요가 없다.

- 조금 더 알아보기

  - Langchain에서 Runnable은 "입력 -> 출력" 흐름을 가지는 함수형 객체

    ```python
    class Runnable:
      def invoke(self, input: Any) -> Any:
        ...
    ```

    강제하는 구체적인 타입 제약은 없지만, 서로 연결할 수 있으려면 "입출력의 형태가 논리적으로 이어져야"한다.

  - "형식은 자유롭지만, 논리는 맞아야 한다"

    ```
    A | B
    ```

    A는 어떤 입력을 받아서 결과를 낸다. B는 A의 결과를 입력으로 받는다. 즉, A의 출력 형태가 B가 기대하는 입력 형태와 "논리적으로 일치"해야 한다.

## Prompt

- Prompt; 프롬프트
- LLM에게 원하는 작업을 수행하도록 내리는 **구체적인 지시문 또는 질문**
- 업무 지시서와 같아서 지시가 명확하고 구체적일수록 원하는 결과를 얻을 확률이 높아진다.
- 프롬프트의 필요성
  - 문맥(Context) 설정
  - 정보 통합
  - 응답 품질 향상
- 좋은 프롬프트를 만드는 원칙
  1. 역할(Persona) 부여 : 특정 역할을 부여하여 답변의 깊이, 관점을 설정
  2. 작업(Task) 명시 : 수행 작업을 명확하고 구체적으로
  3. 형식(Formal) 지정 : 출력 형식 지정(일관성)
  4. 정보(Context) 제공 : LLM이 작업을 수행하는 데 필요한 정보나 예시 제공

### PromptTemplate, ChatPromptTemplate

- PromptTemplate(구형)

  단일 텍스트만 생성 가능

- ChatPromptTemplate :white_check_mark:

  역할이 있는 메시지 리스트 제공

  ```python
  from langchain.prompts import ChatPromptTemplate
  
  # 튜플(역할, 템플릿) 리스트로 템플릿을 정의
  chat_template = ChatPromptTemplate.from_messages([
    ("system", "system에게 하고싶은말을 여기 적고 {info_to_system} 를 보내서 활용하도록 합니다."),
    ("human", "사용자가 하는 말을 여기 작성하고 {info_from_human}를 보내서 활용하게 합니다.")
  ])
  
  # 변수를 채워 메시지 리스트 생성
  messages = chat_template.format_messages(info_to_system="보내고 싶은 정보", info_from_human="사용자로부터의 정보")
  ```

### Langchain에서의 LLM과 ChatModel

- LLM

  다음에 올 단어 예측(예측만 학습). 초기 버전 모델

- ChatModel

  대화형(지시를 내려서 답변받는 형태). 인간의 피드백(RLHF)을 추가로 학습시켜 미세조정(fine-tuning)이 가능

### FewShotPromptTemplate

- LLM에게 작업 요청 전 몇 개의 모범 답안 예시를 먼저 보여주는 프롬프트 기법

- 필요상황

  - 복잡한 출력 형식 지정
  - 답변 스타일/톤 제어
  - 까다로운 추론/분류

- 구성요소

  - examples : LLM에게 보여줄 모범 답안 목록
  - example_prompt : 모범 답안 목록의 각 항목을 어떤 형식으로 보여줄지를 정하는 템플릿
  - prefix : 모든 예시 앞에 위치할 전체 지시문
  - suffix : 모든 예시 뒤에, 사용자의 실제 질문이 들어갈 부분
  - input_variables : 최종적으로 사용자가 입력할 변수의 이름을 지정. **suffix에 있는 {}안의 변수명과 일치해야 한다.**

- FewShotPromptTemplate

  ```python
  from langchain_core.prompts import PromptTemplate
  
  examples = [
    {"input": "춥다", "output": "덥다" },
    {"input": "큰", "output": "작은"}
  ]
  
  example_prompt = PromptTemplate(
  	input_variables = ["input", "output"], # 사용할 변수 이름들
    template="입력: {input}\n출력: {output}"
  )
  
  few_shot_prompt = fewShotPromptTemplate(
  	examples=examples,
    example_prompt=example_prompt,
    prefix="다음 단어의 반의어를 출력하세요.",
    suffix="입력: {user_input}\n출력:",
    input_variables=["user_input"]
  )
  ```

- 보통 ChatModel에서 설정해도 답변을 잘 받아볼 수 있어서 기본적으로는 잘 사용하진 않음(역할군이 정해져있다는 느낌이긴 해서 목적성에 따라 사용해도 될듯)

- FewShotChatMessagePromptTemplate

## Output Parser

- Output Parser; 출력 파서

- LLM 출력을 받아 더 적합한 형식으로 변환

- 다양한 종류의 출력 파서 존재

  StrOutputParser

  CommaSeperatedListParser

  MarkdownListParser

  JSON

  ...

## Langchain 언어모델

- 클라우드 기반
  - gpt (OpenAI)
  - claude (Anthropic)
  - gemini (Google)
- 로컬 실행
  - Llama (Meta)
  - gemma (Google)
  - qwen (Alibaba)
  - deepseek (DeepSeek)
  - solar (Upstage)
- gemma, qwen이 경량 모델이 많이 나와서 공공 기관들에서 사용 가능성이 높고
- 작년까지는 온프레미스 구축 어려움이 컸으나, 현재는 모델 성능 향상으로 전부 온프레미스로 구축 중.

## LLM 연동

- ChatOpenAI

  ```python
  from langchain_openai import ChatOpenAI
  from langchain_core.prompts import ChatPromptTemplate
  from langchain_core.output_parsers import StrOutputParser
  from dotenv import load_dotenv
  
  # OpenAI API KEY 로드
  load_dotenv()
  # 모델 초기화
  model = ChatOpenAI(model="가져오고자 하는 모델 명")
  # 체인 구성 및 실행
  prompt = ChatPromptTemplate("바나나는 길어 길면 기차 기차는 빨라 빠르면?")
  chain = prmopt | model | StrOutputParser()
  
  print(chian.invoke({}))
  ```

- ChatGoogleGenerativeAI

  ```python
  from langchain_google_genai import ChatGoogleGenerativeAI
  from langchain_core.prompts import ChatPromptTemplate
  from langchain_core.output_parsers import StrOutputParser
  from dotenv import load_dotenv
  
  # Google API KEY 로드
  load_dotenv()
  # 모델 초기화
  model = ChatGoogleGenerativeAI(model="가져오고자 하는 모델 명")
  # 체인 구성 및 실행
  prompt = ChatPromptTemplate("사람의 최적 수면시간은?")
  chain = prmopt | model | StrOutputParser()
  
  print(chian.invoke({}))
  ```

- ChatAntropic

  ```python
  from langchain_anthropic import ChatAnthropic
  
  ...
  model = ChatAnthropic(model="가져오고자 하는 모델 명")
  ```

- ChatOllama

- Huggingface 연동

  ```python
  from langchain_huggingface import HuggingFacePipeline
  
  ...
  llm = HuggingFacePipeline.from_model_id(
  	model_id="모델명",
    task="text-generation",
    device_map="auto",
    pipeline_kwargs={"max_new_tokens": 512}
  )
  ```

  - 연동시 주의사항
    - HugginFace 게정 필요
    - Access Token 필요
    - CPU에서는 성능이 매우 낮아 GPU 연동 필요

## Document Loader

- 검색 증강 생성(RAG; Retrieval-Augmented Generation) 시스템에서의 가장 첫 번째 단계
- PDF, 웹페이지, CSV 등 다양한 소스의 데이터를 LLM이 처리할 수 있도록 표준화된 Document 객체로 가져오는 구성요소
- Document는 Langchain에서 텍스트 데이터를 다루는 가장 기본적인 단위
- Document 객체의 두 가지 주요 속성
  - page_content : 문서의 실제 텍스트 내용이 담기는 부분
  - metadata : 문서의 부가 정보가 담기는 딕셔너리. 파일 출처(source), 몇 페이지인지(page) 등을 기록하여 나중에 답변의 출처를 밝히는 데 매우 유용하게 사용됨

## TextSplitter

- RAG 시스템에서의 두 번째 단계

- 로드된 문서들을 효율적으로 처리하고, 시스템이 정보를 보다 잘 활용할 수 있도록 준비하는 중요한 과정(청킹)

- LLM이 받아들일 수 있는 효율적인 작은 규모의 조각, 토큰으로 나누는 작업

- 문서분할 과정

  - 문서 구조 파악
  - 단위 선정
  - 단위 크기 선정(chunk size)
  - 청크 오버랩(chunk overlap)

- 텍스트 분할 클래스

  - RecursiveCharacterTextSplitter : 지정된 구분자 리스트 기준으로 분할(대부분 이를 사용) :white_check_mark:

    ...

## Embedding

- RAG 시스템의 세 번째 단계
- 기계가 이해할 수 있는 형태로 변환하는 과정
- 얼마나 임베딩을 잘하느냐에따라 받을 수 있는 결과값이 크게 달라짐
- 벡터 검색으로 인해 의미를 가진 검색이 가능해짐

### 유사도 검색

- 유사도 검색 종류

  코사인 유사도, 내적, 유클리드 거리

- 코사인 유사도

  - 벡터가 이루는 각도
  - 두 벡터가 같은 방향을 가리키면 각도는 0도가 되고, 코사인 값은 1이 되어 완전히 유사함을 의미하게 된다. 
  - 기본적으로 가장 많이 사용됨

- 내적

  - 방향과 크기의 곱
  - 내적은 두 벡터의 방향(각도)과 크기(길이)를 모두 반영하여 계산
  - 방향이 비슷하더라도 벡터의 크기가 클수록 내적 값은 더 커짐

- 유클리드 거리

  - 두 벡터 끝 점 사이의 직선 거리(물리적 거리)
  - 거리가 가까울수록(값이 작을수록) 더 유사하다고 판단

### 정규화

- 벡터의 고유한 방향은 유지하면서, 크기를 정확히 1로 만들어주는 처리 과정

- 랭체인에서는 정규화 과정(방향을 유지하면서 크기를 전부 1로 만드는 것. 그러면 방향만 비교하면 됨 이러면 벡터검색을 쉽게 내적으로만 계산할 수 있게 됨)을 거침

  즉, 크기가 계산에 영향을 미치지 않게 해주는 것

- 정규화를 해서 일관되게 데이터를 가져올 수 있도록해줌

- 데이터 규모가 커지면 정규화 적용 유무에 따른 효과 차이가 커짐

## VectorStore

- RAG 시스템의 네 번째 단계
- 임베딩한 데이터를 저장하고 관리하는 단계

## Retriever

- RAG 시스템의 다섯 번째 단계
- 저장된 벡터 데이터베이스에서 사용자의 질문과 관련된 문서를 검색하는 과정
- 동작방식
  - 질문의 벡터화
  - 벡터 유사성 비교
  - 상위 문서 선정
  - 문서 정보 반환

### Sparse Retriever

- 텍스트에 어떤 단어가 얼마나 자주 나타나는지를 계산(키워드 검색)

### Dense Retriever

- 텍스트의 문맥과 의미를 이해하여 숫자 벡터로 변환(임베딩)
- 벡터 검색과 유사하다고 생각하면 됨

### 주요 Retriever

- Vector Store Retriever

- Ensemble(앙상블) Retriever

  '하이브리드' 검색. 즉, 두 개 이상의 리트리버를 결합하여 각 리트리버의 검색 결과를 종합하고 순위를 다시 매기는 검색기능

  최근 가장 많이 사용되는 검색기능

- Multi-Query Retriever

  쿼리(사용자질문)들어오면 LLM이 다시 해당 질문을 여러 질문으로 파생시켜서 파생된 질문도 같이 던져서 결과를 검색.

  temperature=0 창의적이지 않은 정확한 답변, 1에 가까울 수록 창의적으로 생성(정확한 답변을 원하면 0 ~ 0.5, 창의적인 내용을 원한다면 0.5~1로 설정)

- Contextual Compression Retriever

  유사도 검사 랭킹이 지정되어서 가져왔는데, 리랭커들 돌려서 가져온 문서들을 확인 후 질문과 관련된 부부만 압축/필터링

  하이브리드 리트리버와 결합해서 자주 사용됨

  리랭커를 사용하면 답변 정확도가 올라갈 수 있지만 잘못 작동되면 점수가 편향된 부분이 생길 수 있음

- Self-Query Retriever

  LLM이 자연어를 자동 분해하여 구조화된 쿼리를 생성

  메타 데이터 필드에 대한 정의 필요

- 더 다양한 리트리버가 존재.

- Parent Document Retriever

  부모,자식 관계 연관해서 검색

  부모 문서용 큰 청크를 하나 만들어 놓고 자식 문서용 작은 청크를 하나 더 만듬. 검색자체는 작은 청크로 진행해서 DB저장, 검색하고 부모 문서는 가져올 때 참고용

## RAG

- 검색 증강 생성(Retrieval-Augmented Generation; RAG)

- 기본 적으로 학습된 데이터만을 갖고 응답하여 발생하는 할루시네이션.

  또한 LLM은 부족한 최신 정보(RAG가 외부 검색을 통해 최신 뉴스, 특정 분야의 전문 지식에 대한 정보를 가져와서 응답이 가능하고 정확한 정보와 최신성 유지가 가능)

- Langchain RAG의 8단계 프로세스

  - 문서 로드(Document Loader)

  - 텍스트 분할(Text Splitter)

  - 임베딩(Embedding)

    :bulb:TIP) 임베딩 시에 테스트 코드를 하나 넣어놓아서 확인하면 대량의 데이터가 들어가기전에 모델이 정상적으로 작동하는지 확인이 가능해서 좋음

  - 벡터스토어(Vector Store) 저장

  - 검색기(Retriever)

  - 프롬프트(Prompt)

    RAG 프롬프트 템플릿 구성

  - LLM(Large Language Model) 연동

  - 체인 생성 : 이전의 모든 과정을 하나의 파이프라인으로 묶어주는 체인을 생성