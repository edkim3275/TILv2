# 인터페이스

- CS에서 인터페이스는 상호 간에 정의한 약속 혹은 규칙을 의미한다.

  타입스크립트에서 인터페이스는 객체 타입을 정의할 때 사용된다.

- 인터페이스는 객체 타입 뿐만 아니라 다음 범주에 대해서도 타입을 정의할 수 있다.

  - 객체의 스펙(속성과 속성의 타입)
  - 함수의 파라미터
  - 함수의 스펙(파라미터, 반환 타입 등)
  - 배열과 객체를 접근하는 방식
  - 클래스

## 인터페이스 맛보기

- 인터페이스에 대해 알아볼 수 있는 간단한 예제

  ```ts
  let person = { name: 'edgar', age: 31};
  
  function logAge(obj: { age: number }) {
    console.lg(obj.age); // 31
  }
  
  logAge(person); // 31
  ```

  인자를 받을 때 단순한 타입뿐만 아니라 객체의 속성 타입까지 정의할 수 있다.

  여기에 인터페이스를 적용해보면

  ```ts
  interface personAge {
    age: number;
  }
  
  function logAge(obj: personAge) {
    console.log(obj.age);
  }
  
  let person = { name: 'edgar', age: 31};
  logAge(person);
  ```

  > :question: 여기서 매개변수 타입에 name 정보를 작성하지 않아도 에러가 발생하지않는다. 왜그럴까? 
  >
  > logAge 함수의 매개변수는 `{age: number}` 타입을 요구하지만 인수로 전달된 persondms `{name: string, age: number}` 타입이므로 age 속성이 포함되어 있어 문제 없이 전달된다. 즉, 타입스크립트는 person을 최소 `{age : number}`를 포함한 더 큰 타입으로 인식하기 때문에 에러가 발생하지 않는다.
  >
  > 하지만 `logAge({ name: 'edgar', age: 31 })`과 같이 바로 객체를 인자로 전달하면 에러가 발생한다. 이유는 타입스크립트는 객체 리터럴에 대해 "강력한 검사(strict checking)"를 수행하기 때문에 이 경우 초과 속성 검사(Excess Property Checking)가 적용되어 에러가 발생한다.

- **인터페이스를 인자로 받아 사용할 때 항상 인터페이스 속성 개수와 인자로 받는 객체의 속성 개수를 일치시키지 않아도 된다.** 다시 말해, 인터페이스에 정의된 속성, 타입의 조건만 만족한다면 객체의 속성 개수가 더 많아도 상관 없다는 의미이다. 또한, 인터페이스에 선언된 속성 순서를 지키지 않아도 된다.

## 옵션 속성

- 인터페이스를 사용할 때 인터페이스에 정의되어 있는 속성을 모두 다 꼭 사용하지 않아도 된다. 이를 옵션 속성이라고 한다.

  ```ts
  interface 인터페이스_이름 {
    속성?: 타입;
  }
  ```

  이처럼 속성의 끝에 `?`를 붙인다.

- 예시를 살펴보면

  ```ts
  interface CraftBeer {
    name: string;
    hop?: nubmer;
  }
  let myBeer = {
    name: 'Saporo'
  }
  function brewBeer(beer: CraftBeer) {
    console.log(beer.name);
  }
  brewBeer(myBeer);
  ```

  `brewBeer`함수에서 `CraftBeer` 인터페이스를 인자의 타입으로 선언했음에도 불구하고, 인자로 넘긴 객체는 `hop` 속성이 없다. 왜냐하면 `hop` 을 옵션 속성으로 선언했기 때문

### 옵션 속성의 장점

- 옵션 속성의 장점은 단순 인터페이스를 사용할 때 속성을 선택적으로 적용할 수 있다는 것 뿐만 아니라 **인터페이스에 정의되어 있지 않은 속성에 대해 인지시켜줄 수 있다**는 점이다.

  ```ts
  interface CraftBeer {
    name: string;
    hop?: number;
  }
  
  let myBeer = {
    name: 'Saporo'
  };
  function brewBeer(beer: CraftBeer) {
    console.log(beer.brewery); // Error: Property 'brewery' does not exist on type 'Beer'
  }
  brewBeer(myBeer);
  ```

  이처럼 인터페이스에 정의되어 있지 않은 속성에 대해 오류를 표시한다. 만약 아래와 같이 오탈자가 난 경우 그것 역시 알려준다.

  ```ts
  interface CraftBeer {
    name: string;
    hop?: number;
  }
  
  function brewBeer(beer: CraftBeer) {
    console.log(beer.nam); // Error: Property 'nam' does not exist on type 'Beer'
  }
  ```

## 읽기 전용 속성

- 읽기 전용 속성은 인터페이스로 객체를 처음 생성할 때만 값을 할당하고, 그 이후에는 변경할 수 없는 속성을 의미한다.

  문법은 다음과 같이 `readonly` 키워드를 속성 앞에 붙인다.

  ```ts
  interface CraftBeer {
    readonly brand: string;
  }
  ```

  위와 같이 인터페이스로 객체를 선언하고 나서 수정하려고 하면 아래와 같이 오류가 발생한다.

  ```ts
  let myBeer: CraftBeer = {
    brand: 'Asahi'
  };
  myBeer.brand = 'Korean Carpenter'; // error
  ```

## 읽기 전용 배열

- 배열을 선언할 때 `ReadonlyArray<T>` 타입을 사용하면 읽기 전용 배열을 생성할 수 있다.

  ```ts
  let arr: ReadonlyArray<number> = [1, 2, 3];
  arr.splice(0, 1); // error
  arr.push(4); // error
  arr[0] = 100; // error
  ```

- 배열을 `ReadonlyArray`로 선언하면 배열의 내용을 변경할 수 없다. 선언하는 시점에만 값을 정의할 수 있으니 주의해서 사용해야한다.

## 객체 선언과 관련된 타입 체킹

- 타입스크립트는 인터페이스를 이용하여 객체를 선언할 때 좀 더 엄밀한 속성 검사를 진행한다.

  ```ts
  interface CraftBeer {
    brand?: string;
  }
  
  function brewBeer(beer: CraftBeer) {
    // ...
  }
  breaBeer({ brandon: 'new Brand' }); // error: Object literal may only specify known properties, but 'brandon' does not exist in type 'CraftBeer'. Did you mean to write 'brand'?
  ```

   `CraftBeer` 인터페이스에는 `brand`라고 선언되어 있지만 `brewBeer()` 함수에 인자로 넘기는 `myBeer` 객체에는 `brandon`이 선언되어 있어 오탈자 점검을 요하는 오류가 발생한다.

- 만약 이런 타입 추론을 무시하고 싶다면 아래와 같이 선언한다.(Type Assertion)

  ```ts
  // 1 변수 저장 후 전달 시 초과 속성 검사 우회하여 에러발생하지 않음
  let myBeer = { brandon: 'new Brand' };
  brewBeer(myBeer as CraftBeer);
  // 2 객체 리터럴 직접 사용시 초과 속성 검사 적용으로 에러발생
  breaBeer({ brandon: 'new Brand' } as CraftBeer);
  ```

- 그럼에도 불구하고 만약 인터페이스 정의하지 않은 속성들을 추가로 사용하고 싶을 때는 아래와 같은 방법을 사용한다.

  ```ts
  interface CraftBeer {
    brand?: string;
    [propName: string]: any;
  }
  ```

## 함수 타입

- 인터페이스는 함수의 타입을 정의할 때에도 사용할 수 있다.

  ```ts
  interface login {
    (username: string, password: string): boolean;
  }
  ```

  함수의 인자의 타입과 반환 값의 타입을 정한다.

  ```ts
  let loginUser: login;
  loginUser = function(id: string, pw: string) {
    return true;
  }
  ```

## 클래스 타입

- 클래스가 일정 조건을 만족하도록 타입 규칙을 정할 수 있다.

  ```ts
  interface CraftBeer {
    beerName: string;
    nameBeer(beer: string): void;
  }
  class myBeer implements CraftBeer {
    beerName: string = 'Sapporo';
    nameBeer(b: string) {
      this.beerName = b;
    }
    constructor() {}
  }
  ```

## 인터페이스 확장

- 클래스와 마찬가지로 인터페이스도 인터페이스 간 확장이 가능하다.

  ```ts
  interface Person {
    name: string;
  }
  interface Developer extends Person {
    skill: string;
  }
  let fe = {} as Developer;
  fe.name = 'josh';
  fe.skill = 'TypeScript';
  ```

- 여러 인터페이스를 상속받아 사용할 수도 있다.

  ```ts
  interface Person {
    name: string;
  }
  interface Drinker {
    drink: string;
  }
  interface Developer extends Person, Drinker {
    skill: string;
  }
  let fe = {} as Developer;
  fe.name = 'josh';
  fe.skill = 'TypeScript';
  fe.drink = 'Beer';
  ```

## 하이브리드 타입

- 자바스크립트의 유연하고 동적인 타입 특성에 따라 인터페이스 또한 여러 가지 타입을 조합하여 만들 수 있다. 예를들어 다음과 같이 함수 타입이면서 객체 타입을 정의할 수 있는 인터페이스가 있다.

  ```ts
  interface CraftBeer {
    (beer: string): string; // 함수 호출 시그니처
    brand: string; // 속성
    brew(): void; // 메서드(반환값 없음)
  }
  
  function myBeer(): CraftBeer {
    let my = (function(beer: string) {}) as CraftBeer;
    my.brand = 'Beer Kitchen';
    my.brew = function() {};
    return my;
  }
  ```

  이러한 인터페이스는 하나의 객체가 함수처럼 호출될 수도 있고, 속성과 메서드도 가질 수 있는 형태가 된다.