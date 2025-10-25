# LangGraph

- RAG 사용중 마주하게 되는 갈등
  - Hallucination
  - 답변이 문서에 없는 "사전지식"으로 답변한건 아닐까?
  - 문서 검색에서 원하는 내용이 없는 경우 => 부족한 정보를 Web 검색하여 문서에 추가하는 로직을 추가해서 검색을 진행
- LangChain 보완하기위한 LangGraph
- RAG를 잘 사용하더라도 정확도가 90% 정도라는 말이 있음. 10%의 할루시네이션 가능성은 존재. 이를 줄이기 위한 고민이 개발단계에 생기게 됨
- 검색이 제대로 나올때까지 반복해서 검색?(토큰 사용량 폭증 ​=​> :x: )

## 기본적인 RAG의 문제점

- 사전에 정의된 데이터 소싱(PDF, DB, Table 등) 자원
- 사전에 정의된 Fixed Size Chunk
- 사전에 정의된 Query
- 사전에 정의된 검색 방법(문서외에는 답변을 하지 못하는 상황)
- 신뢰하기 어려운 LLM 혹은 Agent
- 고정된 프롬프트 형식
- LLM 답변 결과에 대한 문서와의 관련성 / 신뢰성
- Document Loader > Answer의 RAG 파이프라인이 **단방향 구조**이기 때문
  - 모든 단계를 한 번에 다 잘해야하고, 이전 단계로 되돌아가기 어려운 문제점

## LangGraph의 제안

- 각 세부과정을 노드(Node)라고 정의

  :bulb: 노드는 내부 작업, 도구는 외부에서 가져오는 기능

- 이전 노드 > 다음 노드 : 엣지(Edge)로 연결

- 조건부 엣지를 통해 분기 처리(라우팅)

=> **단방향 구조인 RAG 파이프라인을 보다 유연하게 설계하는 것이 가능해짐(순환구조를 가질 수 있게 됨)**

- 평가자 & Query Transform 추가
  - 질문이 모호하게 들어온 경우 질문 재작성을 하거나
  - 문서검색 결과를 평가 하여 관련성이 떨어지면 재 검색을 하는 것이 가능
- 추가 검색기를 통하여 문맥(context) 보강

## LangGraph

- :star2: Node(노드) : 어떤 작업(task)을 수행할지 정의

- :star2: Edge(엣지) : 다음으로 실행할 동작 정의

- :star2: State(상태) : 현재의 상태 값을 저장 및 전달하는데 활용(중첩으로 다음 노드에 넘어감)

- :star2: ​Conditional Edge(조건부 엣지) : 조건에 따라 분기 처리(조건 분기에 따라 엣지를 연결)

- 알고리즘의 집합을 그래프(Graph), 작업들을 노드(Node), 연결부가 엣지(Edge)

- Node, Edge, State를 통해 LLM을 활용한 워크플로우에 순환 연산 기능을 추가하여 손쉽게 흐름을 제어

- Human-in-the-loop : 필요시 중간 개입하여 다음 단계를 결정

  '사용자에게 해당 데이터가 맞습니까?' 식의 확인과정

- Checkpointer : 과거 실행 과정에 대한 "수정" & "리플레이" 기능

### State(상태)

- LangGraph정의 시 기본적으로 상태를 먼저 정의하게 됨

- TypedDict : 일반 파이썬 딕셔너리에 타입힌팅을 추가한 개념(모든 값을 다 채우지 않아도 됨)

- 새로운 노드에서 값을 덮어쓰게 됨(Overwrite)

  ```python
  from typing import TypedDict
  
  class GraphState(TypedDict):
    example: str
    num_list: list[str]
    question: str
    decision: list[str]
  ```

   각 노드에서 새롭게 업데이트 하는 값은 기존 Key값을 덮어쓴다

- 노드에서 필요한 상태 값을 조회하여 동작에 활용할 수 있음

- state 정보를 변경하는 방법

  ```python
  def update_node(state: GraphState):
    ...
    return {"example": "new_example"}
  ```

  node에서 반환 값으로 State 스키마에 맞는 새로운 State 정보를 딕셔너리 형태로 반환하면 된다. 위에서는 일부 필드만 업데이트 했으므로 업데이트 된 필드의 값만 변경된다.

- Reducer를 이용한 상태 정보 변경

  Reducer로 사용할 함수를 지정하기 위해서 일반적으로 Annotated를 사용. Annotated는 해당 필드에 대한 데이터 형과, 데이터를 업데이트할때 사용할 behavior를 두 번째 인자로 지정한다.

  ```python
  from typing import Annotated
  from typing_extensions import TypedDict
  from operator import add
  
  class GraphState(TypedDict):
    num_list: Annotated[list[str], add]
  ```

  num_list는 str타입의 list로 update가 발생할 시에 해당 필드를 업데이트하지 않고, add라는 operator를 이용해서 리스트에 계속해서 데이터를 append하게 된다.

- :warning: Multi State 사용시 주의점

  Langraph에서는 하나의 Graph에서 여러개의 State를 동시에 사용할 수 있다. 다만 업데이트시에 Langraph는 노드에서 반환시 모든 state값을 병합하여 저장한다. 즉, state에 같은 이름이 있을 경우에는 어느 state의 정보인지와 상관없이 공통적으로 변경이 된다. 따라서 State마다 다른 변수 이름을 사용하는 것이 좋다.

### Node(노드)

- 에이전트가 실질적으로 일(State 정보를 변경하거나 action을 취함)을 할 수 있게하는 요소

- 함수로 정의

  ```python
  def retrieve(state: GraphState):
  	return
  ```

- 상태를 전부 받게 됨, 반환 또한 대부분 상태 객체를 반환

- Agent에서 함수 구현, 노드에서는 가져다가 사용하는 방식

- 그래프 생성 후 Node 추가

  ```
  add_node("노드이름", 함수)
  ```

- config

  두 번째 인자를 통해서 config 정보 전달이 가능. state는 node가 답변을 내는데 필요한 정보라고 한다면, config는 답변 생성에는 사용하지 않지만 node의 행동을 정의하기 위한 추가적인 메타 정보라고 생각하면된다.

  config에는 LLM 이름이나 사용자 정보, 로깅/모닝터링을 위한 request id 등의 메타 정보가 들어가게 된다.

- START와 END 노드

  그래프의 시작점과 끝점을 정의하는 노드

### Edge(엣지)

- 노드에서 노드간의 연결

  ```
    					from > to
  add_edge("노드이름", "노드이름")
  ```

### 조건부 Edge(엣지)

- 노드에 조건부 엣지를 추가하여 분기를 수행

  ```
  add_conditional_edges("노드이름", 조건부 판단 함수, dict로 다음 단계 결정)
  ```

  노드에서 나온 결과를 조건부 판단 함수에 입력으로 전달. 반환된 값은 dict로 나와서 dict로 다음 단계를 결정

  value에 해당하는 값이 END면 Graph 실행 종료

  노드이름이라면 해당 노드로 연결

### 도구호출

- 도구는 노드와 마찬가지로 함수로 정의한다.

  ```python
  @tool
  def web_search(query: str):
    ...
    return response
  ```

- ToolNode

  도구(tool)를 호출하는 노드를 ToolNode라고 하는데, 인자로 호출할 툴 함수를 정의한다.

- 툴 노드를 정의했으면 LLM 모델에게 현재 생성된 툴 함수를 연결해주어야 한다.

  ```python
  llm = chat_model(model="gemini-2.5-flash")
  model_with_tools = llm.bind_tools([web_search])
  ```

  이를 통해 LLM은 툴의 기능을 파악하고, 필요에 따라서 툴을 호출하도록 판단을 내릴 수 있다.

### 시적점 지정

- 지정한 시작점부터 Graph가 시작

  ```
  set_entry_point("노드이름")
  ```

### 체크포인터(memory)

- Checkpointer : 각 노드간 실행결과를 추적하기 위한 메모리(대화에 대한 기록과 유사한 개념)
- 체크포인터를 활용하여 특정 시점(snapshot)으로 되롤리기 기능도 가능
- compile(checkpoint=memory) 지정하여 그래프 생성

### 그래프 시각화

- 생성한 그래프 시각화

  ```
  # 실행 가능한 객체의 그래프를 mermaid 형식의 PNG로 그려서 표시
  get_graph(xray_True).draw_mermaid.png()
  ```

### 그래프 실행

- RunnableConfig

  - recursion_limit : 최대 노드 실행 개수를 지정

  - thread_id : 그래프 실행 아이디를 기록하고, 추후 추적하기 위한 목적으로 활용

    ```python
    from langchain_core.runnables import RunnableConfig
    
    # recursion_limit : 최대 반복 횟수
    # thread_id : 실행 ID
    config = RunnableConfig(recursion_limit=4, configurable={"thread_id": "ACTION-ID"})
    
    question = "..."
    inputs = GraphState(question=question)
    output = app.invoke(inputs, config=config)
    ```

    







































