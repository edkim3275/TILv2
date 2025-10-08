## 타입스크립트 기본 타입

- 변수나 함수를 정의할 때 사용하는 기본 타입. 기본 타입은 12가지가 있다.

  string, number, boolean, object, array, tuple, enum, null, undefined, any, void, never

## 문자열(string)

- 자바스크립트 변수의 타입이 문자열인 경우 아래와 같이 선언해서 사용한다.

  ```ts
  let str: string = 'Hello';
  ```

  :bulb: 위 처럼 `:`를 이용하여 자바스크립트 코드에 타입을 정의하는 방식을 타입 표기(Type Annotation)라고 한다.

## 숫자(number)

- 타입이 숫자면 아래와 같이 선언한다.

  ```ts
  let num: number = 10;
  ```

## 진위(boolean)

- 타입이 참/거짓 값인 경우에는 아래와 같이 선언한다.

  ```ts
  let isTrue: boolean = true;
  ```

## 객체(object)

- 객체 타입은 아래와 같이 정의할 수 있다.

  ```ts
  let user: object = {
    name: 'cat',
    age: 10
  };
  ```

- 다만, 객체 타입은 위와같이 정의하는 것보다 **인터페이스**나 **타입 별칭**을 쓰는 것이 더 좋다.

## 배열(array)

- 타입이 배열인 경우 간단하게 아래와 같이 선언한다..

  ```ts
  let arr: nubmer[] = [1, 2, 3];
  ```

- 또는, 아래와 같이 **제네릭**을 사용할 수 있다.

  ```ts
  let arr: Array<number> = [1, 2, 3];
  ```

## 튜플(tuple)

- 튜플은 **배열의 길이가 고정되고 각 요소의 타입이 지정되어 있는 배열 형식**을 의미한다.

  ```ts
  let arr: [string, number] = ['hello', 10];
  ```

  만약 정의하지 않은 타입, 인덱스로 접근할 경우 오류가 난다.

  ```ts
  arr[1].concat('!'); // // Error, 'number' does not have 'concat'
  arr[5] = 'hello'; // // Error, Property '5' does not exist on type '[string, number]'.
  ```

## 이넘(enum)

- 이넘은 C, Java와 같은 언어에서 흔하게 쓰이는 타입으로 특정 값(상수)들의 집합을 의미한다.

  ```ts
  enum Animals {
    Cat,
    Dog,
    Monkey
  }
  let animal: Animlas = Animals.Cat;
  ```

- 이넘은 인덱스 번호로도 접근할 수 있다.

  ```ts
  enum Animals {
    Cat,
    Dog,
    Monkey
  }
  let animal: Animlas = Animals[0];
  ```

- 만약 원한다면 이넘의 인덱스를 사용자 편의로 변경하여 사용할 수도 있다.

  ```ts
  enum Animals {
    Cat = 2,
    Dog,
    Monkey
  }
  let animal: Animals = Animals[2]; // Cat
  let animal: Animals = Animals[4]; // Monkey
  ```

## any

- any 타입은 모든 타입에 사용할 수 있는 치트키 같은 타입.

- 특정 데이터의 타입을 잘 모르거나 자바스크립트 프로젝트에 타입스크립트를 점진적으로 적용할 때 사용하면 좋은 타입.

- 단어 의미 그대로 모든 타입에 대해서 허용한다는 의미를 갖고 있다.

  ```ts
  let str: any = 'hi';
  let num: any = 10;
  let arr: any = ['a', 2, true];
  ```

  :warning:`any` 타입을 많이 사용하면 사용할수록 타입스크립트의 장점이 사라지니 꼭 필요할 때만 주의해서 사용하자 😃

## void

- 반환 값이 없는 함수의 반환 타입. 아래와 같이 return이 없거나, return이 있더라도 반환하는 값이 없으면 함수의 반환 타입을 void로 지정한다.

  ```ts
  function printSomething(): void {
    console.log('sth');
  }
  
  function returnNothing(): void {
  	return;
  }
  ```

## never

- never 타입은 절대 발생하지 않는 값을 의미하는 타입. 예를들어 함수가 반복문이나 에러 핸들링으로 인해 함수의 끝에 절대 도달하지 않는 경우에 never 타입을 사용할 수 있다.

  ```ts
  // 이 함수는 절대 함수의 끝까지 실행되지 않는다는 의미
  function loopForever(): nerver {
    while (true) {
      // ...
    }
  }
  
  function neverEnd(): never {
    throw new Error('unexpected');
  }
  ```

  

## 참고

[타입스크립트 기본 타입](https://joshua1988.github.io/ts/guide/basic-types.html#%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85)