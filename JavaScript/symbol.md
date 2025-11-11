# Symbol

## Symbol이란?

- 1997년 자바스크립트가 ECMAScript로 처음 표준화된 이래로 자바스크립트는 6개의 타입을 가지고 있다.

- 원시타입(primitive data type)과 객체타입(object type)

- 원시타입
  - Boolean
  - null
  - undefined
  - Number
  - String

- 객체타입
  - Object

- 심볼(Symbol)은 ES6에서 새롭게 추가된 7번째 타입으로 변경 불가능한 원시 타입의 값이다.

- 심볼은 주로 **이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키(property key)를 만들기 위해 사용**한다. 즉, 객체의 고유한 프로퍼티 키를 만들기 위해서 도입되었다.

  기존에는 객체의 프로퍼티 키로 문자열만 사용할 수 있었는데, 이 방식에는 몇 가지 한계가 있었다.(객체 프로퍼티 키의 충돌, 외부 접근 가능성)

- 심볼(Symbol) 도입 이유

  1. 객체 프로퍼티 키의 충돌 방지

     ```js
     const ID = Symbol('id');
     const obj = {
       [ID]: '123';
     }
     console.log(obj[ID]); // 123
     console.log(obj.id); // undefined
     ```

  2. 외부 코드에서 접근을 어렵게 함(은닉화)

     심볼로 정의돈 프로퍼티는 `Object.keys()`나 `for...in`과 같은 일반적인 프로퍼티 열거 방식으로 접근할 수 없다.(일종의 데이터 은닉 기능을 제공)

     ```js
     const SECRET = Symbol('secret');
     
     const user = {
       name: 'Bob',
       [SECRET]: '비밀 데이터'
     };
     
     // 열거되지 않음
     console.log(Object.keys(user)); // ['name']
     console.log(Object.getOwnPropertyNames(user)); // ['name']
     
     // 하지만 Symbol 프로퍼티는 직접 접근 가능
     console.log(user[SECRET]); // '비밀 데이터'
     ```

  3. 내장 심볼과 프로토콜 확장

     심볼은 ES6에서 내장된 심볼을 활용해서 **JS 내부 동작을 커스텀할 수 있도록 도와준다.** 예를들어 `Symbol.iterator`를 사용하면 객체에 이터러블 프로토콜을 추가할 수 있다.

     ```js
     const iterableObj = {
       values: [1, 2, 3],
       [Symbol.iterator]() {
         let index = 0;
         return {
           next: () => {
             return index < this.values.length
             	? { value: this.values[index++], done: false} : { done: true }
           }
         }
       }
     }
     
     for (const value of iterableObj) {
       console.log(value); // 1, 2, 3
     }
     ```

## Symbol의 생성

- 심볼은 `Symbol()` 함수로 생성한다. `Symbol()` 함수는 호출될 때마다 Symbol 값을 생성한다. 이때 생성된 Symbol은 객체가 아니라 변경 불가능한 원시 타입의 값이다.

  ```js
  let myFirstSymbol = Symbol();
  console.log(myFirstSymbol); // Symbol()
  console.log(typeof myFirstSymbol); // symbol
  ```

- `Symbol()`함수는 String, Number, Boolean과 같이 래퍼 객체를 생성하는 생성자 함수와는 달리 `new` 연산자를 사용하지 않는다.

  ```js
  new Symbol(); // TypeError: Symbol is not a constructor
  ```

- `Symbol()` 함수에는 문자열을 인자로 전달할 수 있다. 이 문자열은 Symbol 생성에 어떠한 영향을 주지 않으며 다만 생성된 Symbol에 대한 설명(description)으로 디버깅 용도로만 사용된다.

  ```js
  let mySymbolWithDesc = Symbol('desc1');
  console.log(mySymbolWithDesc); // Symbol(desc1)
  console.log(mySymbolWithDesc === Symbol('desc1')); // false
  ```

## Symbol의 사용

- 객체의 프로퍼티 키는 빈 문자열을 포함하는 모든 문자열로 만들 수 있다.

  ```js
  const obj = {};
  obj.prop = 'myProp';
  obj[123] = 123; // 123은 문자열로 변환된다.
  obj.123 = 123; // SyntaxError: Unexpected number
  obj['prop'+123] = false;
  console.log(obj); // {'123' : 123, prop: 'myProp', prop123: false}
  ```

- Symbol 값도 객체의 프로퍼티 키로 사용할 수 있다. Symbol 값은 유일한 값이므로 **Symbol 값을 키로 갖는 프로퍼티는 다른 어떠한 프로퍼티와도 충돌하지 않는다.**

  ```js
  const obj = {};
  const mySymbol = Symbol('mySymbol');
  obj[mySymbol] = 123;
  console.log(obj); // { [Symbol(mySymbol)]: 123}
  console.log(obj[mySymbol]); // 123
  ```

## Symbol 객체

- Symbol() 함수로 Symbol 값을 생성할 수 있었다. 이것은 Symbol이 함수 객체라는 의미. 브라우저 콘솔에서 Symbol을 참조해보면 Symbol 객체는 프로퍼티와 메소드를 가지고 있다. Symbol 객체의 프로퍼티 중에 length와 prototype을 제외한 프로퍼티를 `Well-Known Symbol`이라고 부른다.

  ![image-20250303181119418](/Users/edgar/Documents/TILv2/JavaScript/symbol.assets/image-20250303181119418.png)

- Well-Known Symbol은 자바스크립트 엔진에 상수로 존재하며, **자바스크립트 엔진은 Well-Known Symbol을 참조하여 일정한 처리를 한다.** 예를들어 어떤 객체가 Symbol.iterator를 프로퍼티 key로 사용한 메소드를 가지고 있으면 자바스크립트 엔진은 이 객체가 **이터레이션 프로토콜**을 따르는 것으로 간주하고 이터레이터로 동작하도록 한다.

- Symbol.iterator를 프로퍼티 key로 사용하여 메소드를 구현하고 있는 빌트인 객체(빌트인 이터러블)는 아래와 같다. 아래의 객체들은 이터레이션 프로토콜을 준수하고 있으며 이터레이터를 반환한다.

  - Array

    Array.prototype[Symbol.iterator]

  - String

    String.prototype[Symbol.iterator]

  - Map

    Map.prototype[Symbol.iterator]

  - Set

    Set.prototype[Symbol.iterator]

  - DOM data structures

    NodeList.prototype[Symbol.iterator]
    HTMLCollection.prototype[Symbol.iterator]

  - arguments

    arguments[Symbol.iterator]

### Symbol.iterator

- Symbol.iterator

  ```js
  // 이터러블(iterable)
  // Symbol.iterator를 프로퍼티 key로 사용한 메소드를 구현하여야 한다.
  // 배열에는 Array.prototype[Symbol.iterator] 메소드가 구현되어 있다.
  const iterable = ['a', 'b', 'c'];
  
  // 이터레이터(iterator)
  // 이터러블의 Symbol.iterator를 프로퍼티 key로 사용한 메소드는 이터레이터를 반환한다.
  const iterator = iterable[Symbol.iterator]();
  
  // 이터레이터는 순회 가능한 자료 구조인 이터러블의 요소를 탐색하기 위한 포인터로서 value, done 프로퍼티를 갖는 객체를 반환하는 next() 함수를 메소드로 갖는 객체이다.
  // 이터레이터의 next() 메소드를 통해 이터러블 객체를 순회할 수 있다.
  console.log(iterator.next()); // { value: 'a', done: false }
  console.log(iterator.next()); // { value: 'b', done: false }
  console.log(iterator.next()); // { value: 'c', done: false }
  console.log(iterator.next()); // { value: undefined, done: true }
  ```

### Symbol.for

- Symbol.for 메소드는 인자로 전달받은 문자열을 키로 사용하여 Symbol 값들이 저장되어 있는 **전역 Symbol 레지스트리**에서 해당 키와 일치하는 저장된 Symbol 값을 검색한다. 
  이때 검색에 성공하면 검색된 Symbol 값을 반환하고, 검색에 실패하면 새로운 Symbol 값을 생성하여 해당 키로 전역 Symbol 레지스트리에 저장한 후, Symbol 값을 반환한다.

  ```js
  // 전역 Symbol 레지스트리에 foo라는 키로 저장된 Symbol이 없으면 새로운 Symbol 생성
  const s1 = Symbol.for('foo');
  // 전역 Symbol 레지스트리에 foo라는 키로 저장된 Symbol이 있으면 해당 Symbol을 반환
  const s2 = Symbol.for('foo');
  console.log(s1 === s2); // true
  ```

- Symbol 함수는 매번 다른 Symbol 값을 생성하는 것에 반해, Symbol.for 메소드는 하나의 Symbol을 생성하여 여러 모듈이 키를 통해 같은 Symbol을 공유할 수 있다.

- Symbol.for 메소드를 통해 생성된 Symbol 값은 반드시 키를 갖는다. 이에 반해 Symbol 함수를 통해 생성된 Symbol 값은 키가 없다.

  ```js
  const shareSymbol = Symbol.for('myKey');
  const key1 = Symbol.keyFor(shareSymbol);
  console.log(key1); // myKey
  
  const unsharedSymbol = Symbol('mykey');
  const key2 = Symbol.keyFor(unsharedSymbol);
  console.log(key2); // undefined