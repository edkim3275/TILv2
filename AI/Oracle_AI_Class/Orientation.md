# Orientaion

- 한국소프트웨어산업협회(KSTA) 운영・총괄, 정보통신산업진흥원(NIPA) 기반

  Oracle이 기술 파트너로 참여한 **Oracle Healthcare AI Agent 서비스 개발 과정**을 참여하게 됐다.

- 강좌 진행 기간 : 25.09.22 ~ 25.11.21 (320시간)

- 수강하면서 터득하게된 지식들을 정리하고 도중에 생겨난 궁금증이나 질문들을 정리해보자!

- 교육개요 - 왜 헬스케어임?

  - 헬스케어 산업의 디지털화 가속

    디지털 헬스케어(환자 데이터, 보험 청구, 원격진료, 예방의료, 공중보건 등) 수요 증가 비례하여 증가하는 의료 데이터

    하지만 규제와 제약이 심한 시장(의료 기업간 데이터 공유 제한) => 오라클에겐 "도전이자 기회"

  - 데이터와 AI 중심 기술 수요

    증가하는 의료 데이터에 대한 수집・분석・활용 기술 중요

  - 오라클이 가지고 있는 장점 - DB, Cloud Infra ...

  - 전략적 확장 기회

    2022년 전자 건강 기록(EHR; Electronic Health Records) 회사 Cerner 인수

  - 따라서

    - 헬스케어 데이터를 다룰 수 있고, AI Agent를 설계/개발 할 수 있는 전문 인력 수요가 증가할 것
    - 자사 플랫폼/AI/클라우드/DB 역량을 함양한 전문인력 양성

  - Oracle Cluud - 인프라 설계・구축, AI Agent 설계・생성과정을 통해 실직적으로 데이터를 어떻게 활용하여 어떻게 AI Agent를 설계(+Fine-tuning)하느냐의 중요성을 알고 해당 흐름을 이해하고 실무에 적용해보는 것

- 커리큘럼

  - 1주차 - 시나리오 설계(AI Agent 비즈니스 전반적인 접근)
    - API 호출(Python)
    - AI API 호출(AI별 방법, params 등) 및 실무적용
  - 2주차 - DB벡터 저장, Oracle LLM
  - 3주차 - 설계 및 튜닝, RAG 기반 구조 설계 및 튜닝
  - 4주차 - LangChain, LangGraph, LangFuse, VIBE Coding
  - 5주차 - ChatGPT, Oracle LLM 연동, 실시간 API 기반 배포 구현
  - 6~7주차 - 개인 건강관리 AI Agent 개발 + 선도기업(Oracle) 특강, 취업상담
  - 8~9주차 - 의료/헬스케어 기관용 상담 AI Agent 개발(다른 주제 협의 후 가능)

- 과정에 임하며 개인적인 목표

  - **날마다 학습한 내용 기록하기**
  - AI시대에서 개발산업에서 요구하는 AI역량 갖추기(현 시대에서의 AI에 대한 이해, 거기서 본인이 당장 해낼 수 있는 능력치와 앞으로 해낼 수 있는 능력에 대한 이해)
  - 목표에 집중하고 혜택에 현혹되지 않기

## 1일차

- ICT(Information Technology Communication) history & trend

- Internet World History

- opensource license - apache, MIT ... => Gen AI의 오픈소스화

  - 소스코드를 누구나 볼 수 있고, 수정・배포 가능한 소프트웨어

  - 하지만 "아무렇게나" 쓰라는게 아니라 **정해진 조건(라이선스)**에 따라 사용・수정・배포해야 함

  - 오픈소스 라이선스는 개발자와 사용자가 소프트웨어를 어떻게 사용할 수 있는지 정해놓은 규칙

  - 오픈소스 라이선스는 자유도에따라 분류됨

    - Coptleft(강한 공유 의무) - GPL(GNU General Public License) ...

    - Permissive(허용적, 자유도 높음) - MIT, Apache(특허 관련 보호 조항 존재함), BSD License

      소스코드 공개 필요 없음, 기업/상용 소프트웨어에 쓰기 유리

    - Hybrid(Copyleft도아니고 Permissive도 아닌 중간형태) - MPL(Mozilla Public License)

- 사업에 연계되는 사업들에 대해서 고민해보기

- AI Agent가 한국에서 도입된다면 경쟁력 있는 산업군 => 방산, 조선

- 헬스케어 산업 규제가 상대적은 낮은 나라 => 미국, 일본, 프랑스, 호주

## Q&A

- Oracle

- Chat GPT 생성 원리

- 라마 3.1 400B

- 양자화

- rRaise?

- model Routing - LangChain, LangGraph 관련(문제에 최적인 AI Model을 선택해주는 것)

- Fine-tuning

- AI Agent

  - Agent(에이전트) = "환경을 인식(perceive)하고, 목표(goal)을 달성하기 위해 행동(act)하는 존재"
  - AI Agent
    1. 상황(환경)을 인식 => 사용자 요청, 데이터, 시스템 상태 등을 읽음
    2. 판단/계획 => 어떤 행동이 목표를 달성하는데 필요한지 스스로 결정
    3. 실행 => API 호출, 데이터 검색, 문서 작성, 외부 시스템 제어 등 실제 행동 수행
    4. 피드백 반영 => 결과를 검토하고 다시 행동 조정(루프)
  - AI Agent 구성 요소
    - 모델(LLM) => 두뇌 역할(ChatGPT, Claude, Llama 같은 것)
    - 도구/플러그인(API, 데이터베이스, 웹검색, 캘린더, 이메일 등)
    - 메모리 => 과거 대화/행동 기록 저장
    - 플래너 => 목표 달성까지 **단계별 계획** 수립

- 모델(두뇌)과 에이전트의 차이

  - AI 모델

    정의 : **대규모 데이터**로 학습된 언어모델/기초모델(Foundation Model)

    역할 : 텍스트 이해 & 생성(질문에 답하기, 글쓰기, 번역 등), "두뇌"와 같은 추론과 언어 능력

    예시 : ChatGPT(OpenAI 서비스), Claude(Anthropic의 언어모델), Gemini(Google DeepMind의 언어모델), Llama(Meta의 오픈소스 언어모델)

    :point_right: 답변을 생성할 뿐, 스스로 행동하지는 않음

  - AI Agent

    정의 : **모델(두뇌)을 이용해 외부 도구를 연결**하고, **목표 달성까지 행동을 수행**하는 시스템

- 모델의 종류

  - 언어모델(LLM: Large Language Model) : 텍스트를 이해하고 생성하는 모델 => 대화, 번역, 글쓰기, 추론에 사용

    예 : GPT 시리즈, Claude, Gemini, Llama 등

  - 비전모델(Vision Model) : 이미지・영상 데이터를 이해하는 모델 => 이미지 분류, 객체 탐지, 이미지 생성, 영상 분석

    예 : CLIP, SAM(Segment Anything Model)

  - 음성모델(Speech Model) : 음성을 바꾸거나, 반대로 텍스트를 음성으로 바꿈

  - 멀티모달 모델(Multimodal Model) : 텍스트 + 이미지 + 음성 + 영상 등 여러 입력을 동시에 다룸

    예 : GPT-4o, Gemini 1.5, Claude 3.5

  - 도메인 특화 모델(Domain-specific Model) : 특정 분야에 맞춰 학습된 모델

    예 : BioGPT(생명과학/의료), BloombergGPT(금융)

  - 강화학습 모델(RL; Reinforcement Learning) : 환경과 상호작용하면서 "보상(reward)"을 최적화하도록 학습

    예 : AlphaGo, AlphaZero, OpenAI Five

  - 생성모델(Generative Models) : 데이터를 새로 만들어내는 모델(텍스트・이미지・음악・영상 등) => 딥페이크, 음악 생성, 영상 합성

    예 : GAN(Generative Adversarial Network), VAE(Variational Autoencoder), Diffusion Models

- RAG(Retrieval-Augmented Generation) - 검색으로 보강된 생성