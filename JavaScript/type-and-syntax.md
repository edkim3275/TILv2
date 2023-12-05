# 타입(Type)과 문법(Syntax)

## 1. 타입

- 자바스크립트 엔진이나 개발자 등이 어떠한 값과 다른 **값을 분별**하기 위한, **고유한 내재적 특성**의 집합을 말한다.

  값을 사용하고자하는 의도의 차이로 생겨난 것이 '타입'이라고 볼 수 있다.

  타입별로 내재된 고유 특성을 제대로 알고 있어야만 값을 다른 타입으로 변환하는 방법을 정확히 알 수 있다.

- 7가지 타입

  `null`, `undefined`, `number`, `boolean`, `string`,`symbol`(from ES6), `object`

  `object` 타입을 제외한 6가지 타입을 원시타입(primitive type)이라고 한다.

- 변수는 타입이 없다.

  값은 타입이 있지만, 변수엔 타입이 없다.

  자바스크립트는 타입 강제(type enforcement)를 하지 않기에 변수는 어떤 형태의 값이라도 가질 수 있다.

- 타입을 확인하기 위한 연산자 `typeof`

  ```javascript
  typeof 29 === "number"; // true
  typeof 'edgar' === "string"; // true
  typeof true === "boolean"; // true
  typeof Symbol() === "symbol"; // true
  typeof undefined === "undefined"; // true
  typeof { name: 'edgar' } === "object"; // true
  typeof [1, 2, 3] === "object"; // true
  ```

  하지만 `null`의 경우는 좀 다르다. `null`은 falsy한 유일한 원시값이고, 타입은 `object`인 좀 독특한 존재.

  ```javascript
  typeof null === "object"; // true
  
  var a = null;
  (!a && typeof a === "object"); // true
  ```

  `typeof`가 반환하는 문자열은 하나가 더 있다.(`typeof` 연산자의 반환값은 언제나 문자열)

  ```javascript
  typeof function a() { ... } === "function"; // true
  ```

- 값이 없는 `undefined` vs. 선언되지 않은 `undeclared`

  `undefined`는 접근 가능한 스코프에서 변수가 선언은 되었으나 값이 할당되지 않은 상태이고, `undeclared`의 경우 선언조차 되지 않은 상태를 말한다.

  ```javascript
  var a;
  
  a; // undefined
  b; // ReferenceError: b is not defined(이는 b is undefined의 의미가 아니라 b is not declared라는 의미임.)
  ```

  이를 `typeof`연산자로 확인해보면 좀 골때리는데... 선언되지 않은 변수라도 브라우저는 오류 처리를 하지 않는다. 이것이 `typeof`의 안전가드(safety guard)

  ```javascript
  var a;
  
  typeof a === "undefined"; // true
  typeof b === "undefined"; // true
  ```

- `typeof`의 안전가드(safe guard)

  브라우저에서 자바스크립트 코드를 처리할 때 의외로 쓸모가 있다.

  1. 여러 스크립트 파일의 변수들이 전역 네임스페이스를 공유할 때

     ```javascript
     // 에러발생
     if (GLOBAL_VARIABLE) {...}
     // 안전하게 확인 가능
     if (typeof GLOBAL_VARIABLE !== "undefined") {
       ...
     }
     ```

  2. 내장 API 기능을 체크할 때

     ```javascript
     if (typeof atob === "undefined") {
       atob = function() { ... }
     }
     ```

  이외에 안전가드 없이 전역 변수를 체크하는 방법은 전역 변수가 모두 전역 객체(브라우저는 `window`)의 프로퍼티라는 점을 이용하는 것. 하지만 전역 객체는 꼭 `window` 객체로만 호출되지는 않으므로 삼가는 것이 좋다.

  ```javascript
  // 프로퍼티가 존재하지 않아도 ReferenceError가 발생하지 않는다.
  if (window.GLOBAL_VARIABLE) { ... }
  if (!window.atob) { ... }
  ```

## 2. 값

- 배열, 문자열, 숫자에 대한 값 타입과 작동방식을 잘 이해하고 있어야 한다.

### 2.1. 배열

- 배열은 어떠한 타입의 값이라도 담을 수 있다.

  ```javascript
  const arr = [1, '2', [3]];
  ```

- `delete` 연산자를 사용하면 해당 슬롯에 있는 값을 지울 수 있으나 length 프로퍼티는 그대로 유지된다. 비어있다는 것이 값이 할당되지 않았다는 것이지만 `undefined`는 또 아니다.

  ```javascript
  delete arr[0]; // [비어있음, '2', [3]]
  arr.length; // 3
  ```

- 구멍 난(sparse) 배열에 유의하자.

  ```javascript
  const a = [];
  a[0] = 1;
  a[2] = 3;
  a; // [1, 비어있음, 3]
  ```

- 배열도 객체다. 따라서 키/프로퍼티 문자열 추가가 가능하다. 다만 이 때 주의해야할 점은 키로 사용될 문자열에 숫자를 사용할 경우 문자열 키가 아닌 숫자 키를 사용한 것 같은 결과를 초래한다는 점

  ```javascript
  const a = [];
  a[0] = 1;
  a["foo"] = 1;
  
  a.length; // 1
  a["foo"]; // 1
  a.foo; // 1
  
  a["13"] = 42;
  a.length; // 14
  a; // [1, 비어있음 x 13, 42]
  ```

- 유사배열을 진짜 배열로 바꾸고 싶을 때 `slice()` 함수의 기능을 차용하거나 `Array.from()`을 사용하자.

  ```JavaScript
  function foo() {
    var arr = Array.prototype.slice.call(arguments);
    arr.push('baz');
    console.log(arr); // ['foo', 'bar', 'baz']
  }
  
  foo('foo', 'bar')
  
  ...
  var arr = Array.from(arguments);
  ...
  ```

### 2.2. 문자열

- 문자열은 문자의 배열? 비슷하지만 같지 않다. 이를테면 둘 다 length 프로퍼티, `indexOf()` 메서드, `concat()` 메서드를 가진다.

  ```javascript
  var a = 'foo';
  var b = ['f','o','o'];
  
  a.length; // 3
  b.length; // 3
  
  a.indexOf('f'); // 0
  b.indexOf('f'); // 0
  
  var c = a.concat("bar"); // "foobar"
  var d = b.concat(['b','a','r']); // ['f','o','o','b','a','r']
  
  a; // "foo"
  b; // ['f','o','o']
  ```

- 하지만, 둘은 다르다.

  문자열은 불변 값(immutable)이지만 배열은 가변 값(mutable)이다. 또한 문자열은 불변 값이므로 문자열 메서드는 그 내용을 바로 변경하지 않고 항상 새로운 문자열을 생성 후 반환한다.

  ```javascript
  a[1] = '0';
  b[1] = '0';
  
  a; // 'foo'
  b; // ['f','0','o']
  ```

- 배열 메서드 빌려쓰기

  ```javascript
  // 새로운 문자열 만들기
  var a = "foo";
  a.join; // undefined(배열 메서드 사용불가)
  a.map; // undefined
  
  var b = Array.prototype.join.call(a, "-");
  var c = Array.prototype.map.call(a, function(v){
    return v.toUpperCase() + ".";
  }).join("");
  
  b; // "f-o-o"
  c; // "F.O.O."
  
  // 문자열 뒤집기
  var a = 'hello';
  var reverse = a.split("").reverse().join("");
  ```

- 문자열에 대한 작업이 빈번할 경우 문자열을 문자 단위로 저장하는 배열로 취급하는 것이 나을 수도 있다. 그리고 다시 문자열로 나타낼 때 문자 배열에 `join("")`메서드를 호출한다.

### 2.3. 숫자

- 숫자는 number타입이 유일하여 정수, 부동 소수점 숫자 모두를 아우른다.

- IEEE 754 표준. 배 정도(Double Precision) 표준 포맷(64비트 바이너리)

- 10진수가 디폴트. 소수점 이하 0은 뗀다.

- 아주 크거나, 작으면 지수형(Exponent Form)으로 표시

  ```javascript
  var a = 5E10;
  a; // 50000000000
  a.toExponential(); // "5e+10"
  ```

- 숫자 값은 Number 객체래퍼로 박싱할 수 있기 때문에 `Number.prototype` 메서드로 접근할 수 있다.

  `toFixed()`, `toPrecision()`

- `0.1 + 0.2 === 0.3; // false`

  널리 알려진 이진 부동 소수점 숫자의 부작용 문제. 이진 부동 소수점으로 나타낸 0.1과 0.2는 원래 숫자와 일치하지 않는다. 따라서 둘을 더한 결과 역시 0.3이 아니라 실제로는 0.30000000000000004에 가깝다. 가깝다는 것은 같다는 것이 아니다.

  미세한 '반올림 오차'를 허용 공차로 처리하는 방법(머신 입실론). 자바스크립트 숫자의 머신 입실론은 `2.2200446049250313e-16`(매우 작은 수)이고, `Number.EPSILON`으로 정의

  ```javascript
  function numberCloseEnoughToEqual(n1, n2) {
    return Math.abs(n1-n2) < Number.EPSILON;
  }
  ```

  부동 소수점 숫자의 최대값은 대략 `1.798e+308`이고 `Number.MAX_VALUE`로 정의. 최소값은 `5e-324`고 `Number.MIN_VALUE`로 정의.

  `Number.MAX_VALUE`보다 작은 수준에서 안전한 값의 범위가 정해져 있다. 즉, 표현한 값과 실제 값이 정확하게 일치한다고 장담할 수 있는 정수는 최대 2^53-1(9천 조 정도)인데 이는 `Number.MAX_SAFE_INTEGER`로 정의. 최소값은 `Number.MIN_SAFE_INTEGER`로 정의.

  보통 아주 큰 숫자를 맞닥뜨리는 경우는 64비트 ID를 처리할 때인데 이러한 숫자는 숫자 타입으로 정확하게 표시할 수 없으므로 자바스크립트 `string` 타입으로 저장해야 한다.

- undefined, null, NaN
- Infinity, -Infinity
- 0, -0