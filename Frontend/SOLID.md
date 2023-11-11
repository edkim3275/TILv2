# SOLID 원칙

> 카카오 기술 블로그(https://fe-developers.kakaoent.com/2023/230330-frontend-solid/)를 보고 정리해보면서 스스로 SOLID 원칙을 이해해보기

- 원칙(原則)

  어떤 행동이나 이론 따위에서 일관되게 지켜야 하는 기본적인 규칙이나 법칙

- 실무단의 아키텍처 설계부터 코딩과 리뷰까지 두루두루 사용되는 원칙

- SOLID는 다음 5가지 원칙의 앞글자를 따온 단어

  **S**RP : Single Responsibility Principle

  **O**CP : Open/Closed Principle

  **L**SP : Liskov Substitution Priinciple

  **I**SP : Interface Segregation Principle

  **D**IP : Dependendy Inversion Principle

- *Robert C*. *Martin*이 정립한 용어

## 콘웨이 법칙

- SOLID는 원칙. 원칙은 지키면 아주 좋으면서 이정표로 삼기 적당한 것

- 원칙보다 우선하는 것은 법칙. SW공학에서 유명한 법칙 중 하나인 '콘웨이 법칙'은 SOLID 원칙에서도 대전제가 됨

- "소프트웨어 구조가 조직의 커뮤니케이션 구조와 다르다면 어딘가 잘못된 구조다."

- 콘웨이 법칙에 입각해 소프트웨어 구조를 조직 커뮤니케이션 구조와 유사하게 만들 수 있도록 도와주는 원칙이 SRP(Single Responsibility Principle)

  <img src="/Users/edgar/Documents/TILv2/Frontend/SOLID.assets/image-20231111203842847.png" alt="image-20231111203842847" style="zoom:67%;" />

## SRP : Single Responsibility Principle

- SOLID에서 가장 중요한 원칙.

- SOLID는 기법이 아닌 원칙이기에 기법으로 접근하면 그 범위와 목적 설정이 어긋나게 됨

- SRP(Single Responsibility Principle)는 '단일책임원칙'

- 여기서 섣불리 기법으로 접근하면 '책임 = 동작' 공식으로 잘 못 해석할 수 있음

  즉, 단일한 "동작"만 가진 컴포넌트로 쪼개야 한다고 오해할 수 있다는 것. 컴포넌트를 너무 자잘하게 쪼개면 전체 로직을 한눈에 파악하기 어렵게 만들고 코드 네비게이션에 들어가는 공수를 늘어나게 만듬

  단일한 동작을 갖도록 하는 것은 컴포넌트가 아니라 순수한 함수 한정이 되어야 함

  다만 이는 SRP 원칙으로 도달할 아키텍처 영역이라기 보다는 클린 코드의 영역으로 볼 수 있음

- 그렇다면 SRP는 어떤 원칙일까

  "SRP의 최종 버전은 다음과 같다. 하나의 모듈은 하나의, 오직 하나의 액터에 대해서만 책이몆야 한다."

  모듈은 컴포넌트로 볼 수 있고, 액터는 사용자 또는 이해관계자 집단을 의미함. 이 집단은 팀 단위가 될 수도 있고 **책무** 단위가 될 수 있음. 여기서 중요한 점은 '책임'을 의미하는 것을 소프트웨어 내부의 '동작' 이나 '논리'가 아니라 **조직 간 커뮤니케이션 영역**으로 봐야 한다는 점

- 책무 기반 컴포넌트 설계

  - 소프트웨어는 요구사항에 의해 만들어짐

  - 사업, 마케팅, 기획, 디자인부서로 부터 요구사항 수립 => 구현중엔 개발조직, 개발자로부터 요구사항 수립

  - 컴포넌트 설계는 '요구사항을 전달하는 책무 단위'로 설계되어야 함

  - 프론트엔드의 경우 사용자와 맞닿아있는 영역이라 좀 더 다채롭기도함

  - **각 영역의 요구사항을 명확히 파악**하고 **영역을 구분**해 의존성 없는 독립적인 컴포넌트로 만들어 **각 책무의 요구사항 변경에도 사이드이펙트 없이 유연하게 대처할 수 있도록 설계하고 구현**하는 것이 키포인트

  - SOLID, 특히 SRP는 이러한 지향점에 도달하기 쉽게 해주는 패턴이라고 볼 수 있음

    또한 프로젝트 아키텍처 설계 시 명확하게 컴포넌트를 나눌 수 있는 공신력 있는 기준이 될 수 있음

- 기획과 디자인 조직이 분리되어 있다면 각각의 컴포넌트로 분리되어야 함. 만약 디자이너가 기획도 같이 진행하는 조직이라면 UI와 비지니스가 결합된 컴포넌트로 만들어도 무방함. 중요한 것은 컴포넌트와 요구사항의 변경 범위가 최대한 같도록 설계하는 것

- 예시

  ```react
  function CompoundComponent({ prop1, prop2, prop3 }) {
    return (
    	<CompoundComponent>
      	<CompoundComponent.Title title={prop1} />
        <CompoundComponent.Content text={prop2} />
        <CompoundComponent.Detail items={prop3} />
      </CompoundComponent>
    )
  }
  ```

  ```react
  describe('Section.Title', function() {
    it('CompundComponent에 대한 조건', () => {
      const givenTitle = "조건을 충족한 경우에 대해서";
      const { getByTest } = render(<CompoundComponent.Title title={givenTitle} />);
      expect(getByText(givenTitle)).not.beInDocument();
      expoect(getByText(givenTitle.substring(0, 15))).beInDocument();
    })
  })
  ```

## OCP : Open-Closed Principle

- OCP는 **요구사항이 변경될 때** 기존 코드를 변경하는 것이 아니라 **새로운 코드를 추가하는 방향을 추구**하는 원칙

- 프론트엔드 컴포넌트 설계에서 OCP를 적용하는 방법을 다뤄보면

  닫힌(closed)구조

  ```react
  sections.map((section) => {
    if(section.type === 'a') {
      return section.items.map((item) => <A_Section item={item} />)
    } else if (section.type === 'b') {
      ...
    }
  })
  ```

  확장에 개방(open)될 수 있도록 다형성을 이용한 구조

  ```react
  sections.map((section) => {
    <Section section={section} >
    	{section.items.map((item) => 
      	<Item section={section} item={item } />}                  
      )}
    </Section>
  })
  ```

  이렇게 되면 sections에 대한 데이터를 구성하는 기획부분과 해당 데이터를 개발하는 개발부분이 업무는 분리되면서 업데이트된 내용은 효율적으로 반영될 수 있게된다.

- 좀 더 수정될 가능성(Section자체 구성에 대한 변경 등)에 대해서는 애플 개발자 공식 문서에서 제안하고 있는 Compositional Layout 패턴(https://developer.apple.com/documentation/uikit/uicollectionviewcompositionallayout)을 이용해보는 것도 좋은방법이 된다.

## LSP : Liskov Substitution Principle

- LSP는 리스코브 치환 법칙을 말함
- 일반적으로 클래스의 상속을 통해 설명하고 증명하지만 아키텍처 관점에서는 좀 더 넓은 의미로 적용할 수 있음
- 상속 관계를 얘기할 땐 간단하게 `is-a`를 만족하는지 여부로 상속 관계인지 판별할 수 있음
- "상속(is-a)으로 이어진 관계에서 예상 못할 행동을 하지 말라"
- 당연한 얘기같지만 실무 레벨에서 빈번히 일어날 수 있는 문제
  - GET method의 REST API로 정의했는데 실제 동작에선 DB 상태 변경
  - API 응답으로 주기로 약속한 모델을 화면마다 다르게 내려줌
  - Label이라 해놓고 체크 박스 기능 요구
  - 필드명이 같은 것을 기준으로 타입스크립트 인터페이스 정의를 무지성으로 상속 관계로 만듦
- 실제 SOLID 원칙 중 '장애상황', '버그'와 가장 밀접하게 관련된 부분이 LSP 원칙

## ISP, DIP : Interface Segregation Principle, Dependency Inversion Principle

- 컴포넌트를 설계할 때 단일한 책무에 맞게 쪼개져 있다면 불필요한 속성(props)없는 독립된 컴포넌트로 구성될 수 있음
- 하지만 컴포넌트만 있으면 페이지는 완성될 수 없고, 페이지를 만들려면 컴포넌트들을 조합(composition)해야함.
- 인터페이스 분리 원칙(ISP)과 의존성 역전 법칙(DIP)은 컴포넌트를 조합할 때 큰 도움을 주는 원칙



- 아키텍처를 구축할 때 SOLID 원칙에 기반한 사고 체계를 가질 수 있다는 점
- 커뮤니케이션 구조를 기반으로 컴포넌트 네이밍부터 시작해 각각에 맞는 책임, 책무를 정의하고(SRP) 변경하는 요구사항을 최소한의 코드 변경을 통해 유연하게 대응하고(OCP), `is-a`를 의도했다면 그 의도에 맞게 구현되도록 힘쓰고(LSP), 컴포넌트에 꼭 필요한 인터페이스만 의존할 수 있도록 구성하고(ISP), 필요하다면 의존성을 역전시켜 독립된 컴포넌트로 만들 수 있도록(DIP) 끊임없이 생각하고 개선해가는 경험과 태도가 중요하다.

## 마무리

- 우선적으로 명확한 클라이언트 요구사항에 대한 분석이 이루어져야만 함(사실 클라이언트가 누구인지 부터인 듯)
- 해당 요구사항에 대한 사내 업무(부서) 분리도를 좀 살펴보고 어떠한 프로세스로 output이 만들어지는지 이해해야 함
- 코드 구현단계에서 컴포넌트 재사용성을 고려한 설계