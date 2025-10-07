# typescript

## 타입스크립트

- 타입스크립트는 자바스크립트에 타입을 부여한 언어

- 자바스크립트의 확장된 언어라고 볼 수 있음

- 자바스크립트와 달리 브라우저에서 실행하려면 파일을 한번 변환해주어야 한다. 이러한 변환 과정을 컴파일(compile)이라고 부른다.

- 자바스크립트는 동적 타입 언어로써 자유도가 높아 진입 장벽이 낮고 생산성이 높은 대신, 형식이 정해져 있지 않기 때문에 오류가 런타임 중에 발생한다거나, 코드를 통한 의사소통에 어려움이 생길 수 있다는 단점이 존재

  cf) C#, Java와 같은 언어들에서 사용되는 강한 타입 시스템은 높은 가독성과 코드 품질을 제공하고 런타임이 아닌 컴파일 환경에서 에러가 발생해 치명적인 오류들을 더욱 쉽게 잡아내어 버그에 대해 미리 방지할 수 있다.

- 타입스크립트는 `.ts` 확장자를 가진 파일로 작성되고, 작성 후 타입스크립트 컴파일러를 통해 자바스크립트 파일로 컴파일하여 사용되게 되는 형태

  ![image-20250313160202914](/Users/edgar/Documents/TILv2/TypeScript/typescript.assets/image-20250313160202914.png)

  따라서 타입스크립트는 프로그래밍 언어이자 컴파일러 역할도 한다고 할 수 있다.

- C나 Java의 경우 컴파일이 엔진에 의해 이루어지지만, 타입스크립트는 컴파일이 라이브러리에 의해 이루어지기 때문에 컴파일 속도가 느리다는 단점이 존재.

## why typescript?

### 에러의 사전 방지

- 타입스크립트는 에러를 사전에 미리 예방할 수 있다.

  ```js
  // math.js
  function sum(a, b) {
    return a+b;
  }
  sum('10', '20'); // 1020
  ```

  ```ts
  // math.ts
  function sum(a: number, b: number) {
    return a+b;
  }
  sum('10', '20') // Error: '10'은 number에 할당될 수 없습니다.
  ```

- 타입스크립트는 변수와 데이터의 종류를 명확히 지정해주기 때문에 위의 단점들이 보완될 수 있다.

- 대부분의 에러를 컴파일 환경에서 코드를 입력하는 동안 체크할 수 있다.

### 코드 가이드 및 자동 완성(개발 생산성 향상)

- 코드를 작성할 때 개발 툴의 기능을 최대로 활용할 수 있다는 것.

  ```ts
  function sum(a: number, b: number): number {
    return a+b;
  }
  let total = sum(10, 20);
  total.toLocaleString();
  ```

- 코드를 작성하는 시점에 타입을 인지할 수 있다.

## 타입스크립트 개발 환경 구성

### 타입스크립트 컴파일러 설치

- 노드 패키지를 설치하는 것처럼 typescript를 글로벌로 설치해둔다. (버전을 명시하고 싶다면 뒤에 @을 붙인다)

  ```shell
  > npm i -g typescript
  > npm install -g typescript@2.7.2 # 버전 명시
  ```

- 타입스크립트 파일이 있는 경로에 가서 컴파일 명령어 `tsc`(type script compile)를 실행해준다.

  그러면 ts 파일이 js 파일로 변환 되는 것을 볼 수 있다.

  ```shell
  > tsc index.ts // ts를 js로 컴파일
  ```

- 타입스크립트 컴파일 주의할 점은 타입스크립트 에러 타입 검사와 컴파일은 별개로 작동한다는 점이다. 그래서 타입스크립트 문법에 에러가 있음에도 tsc 명령어를 실행해보면 문제없이 자바스크립트로 변환되는걸 볼 수 있다.

### ts-node 설치

- `node index.ts` 명령어로 실행해보면 에러가 나고 실행이 안된다. 그래서 ts 파일을 컴파일 한뒤 js 파일을 `node` 명령어로 실행했다.

  이처럼 개발단계에서 타입스크립트 코드를 테스트/실행하기 위해 일일이 컴파일 하기에는 번거롭고 귀찮다.

- ts-node를 설치하면 `ts-node index.ts`와 같은 명령어로 타입스크립트 컴파일 단계없이 바로 코드를 실행할 수 있게 된다.

- 설치 및 사용

  ```shell
  > npm install -g ts-node
  > ts-node index.ts
  ```

### ts-node-dev 사용해보기

- `ts-node`로 곧바로 타입스크립트 파일을 실행하여 테스트해보았지만, 여전히 배포를 하기 위해서는 따로 자바스크립트로 컴파일을 해줘야 해서, 따로 tsc 명령어를 이용해 js로 변환해주어야 하는데, 이 역시 번거롭다.

- 그래서 생겨난게 `ts-node-dev` 패키지. `ts-node-dev`는 `ts-node`와 `node-dev`의 합본팩이다.

  ```shell
  > npm install -g ts-node-dev
  > ts-node-dev --respawn --transpile-only index.ts
  ```

  :bulb: node-dev 는 nodemon 과 같이 watch(실시간 동작) 와 관련된 모듈이다

- `ts-node-dev`로 실행하게 되면, `ts-node`와 같이 곧바로 타입스크립트 파일을 실행해 결과값을 보여주며, 또한 실시간으로 바로 자바스크립트로 컴파일 해준다.

- 이를 응용하여 `package.json`의 scripts 항목에 npm 스크립트로 등록해서 개발환경을 구성할 수 있다.

  ```json
  "scripts": {
     // 전체 ts 파일들을 컴파일
     "build": "tsc", 
  
     // 실시간 ts 컴파일
     "build:watch": "tsc -w", 
  
     // nodemon 실행
     // --watch src/ : src 폴더안에 변화가 있으면 감지해서 재시작함
     // --exec ts-node : ts파일을 nodemon으로 실행시킬 수 있게함
     "dev": "NODE_ENV=dev nodemon --watch src/ --delay 500ms --exec ts-node src/start.ts", 
  
     // ts-node-dev 실행
     // dev 환경에서 ts파일로 서버 실행
     "start:dev": "NODE_ENV=dev ts-node-dev --respawn --transpile-only src/start.ts",
  
     // ts를 빌드한 js file로 node 서버 시작
     "start": "NODE_ENV=production node build/start.js"
  }
  ```

### 타입스크립트 컴파일 설정

- 위에서 `ts-node` 명령어를 실행했을 때 뒤에 옵션을 줬다. 이 옵션들을 마치 package.json 처럼 `tsconfig.json`  파일에서 한번에 관리하여 컴파일 설정을 할 수 있다.

## @type 라이브러리

- 타입스크립트는 자바스크립트를 포함하는 관계. 그렇다면 기존의 자바스크립트 라이브러리(lodash, react 등)를 타입스크립트에서 쓸 수 있을까?

- 가능하다. 다만 각 라이브러리 함수 기능에 대한 타입이 정의 되어 있어야 사용할 수 있다.

  예를들어 아래와 같은 lodash 라이브러리 함수 코드는 타입스크립트에서 제대로 동작하지 않는다.

  ![image-20250313162927684](/Users/edgar/Documents/TILv2/TypeScript/typescript.assets/image-20250313162927684.png)

  그 이유는 lodash 라이브러리 내부 코드에 대한 타입이 정의되어 있지 않아 해당 라이브러리를 들고 와서 사용할 때 타입스크립트 파일에서 타입 추론을 할 수 없기 때문

- 이런 경우 `@types`라는 타입스크립트 추론이 가능한 보조 라이브러리를 설치하면 된다. jQuery나 React와 같이 대중적으로 사용되는 JS 라이브러리는 별도로 `@types/라이브러리명` 를 제공한다.

- `@types` 라이브러리에는 필요한 각 코드에 대한 타입들이 정의된 `.d.ts` 파일들이 들어있다. 기존에는 JS방식으로 돌아가던 라이브러리를 TS 환경에서 사용하게 되는 경우 타입체킹에 필요한 타입들을 불러와야 하기 때문에 이런 추가적인 타입 선언 모듈을 받아서 동작시키는 것이다.

  [Search for typed packages](https://www.typescriptlang.org/dt/search/?search=)를 확인해보니 이제 npm 패키지 레지스트리에 해당 패키지가 타입 정보가 포함되어서 확인할 필요가 없다고 한다.

  ![image-20250313163521064](/Users/edgar/Documents/TILv2/TypeScript/typescript.assets/image-20250313163521064.png)

  ![image-20250313163652004](/Users/edgar/Documents/TILv2/TypeScript/typescript.assets/image-20250313163652004.png)

- 다음과 같이 `@types/라이브러리명`을 npm으로 다운로드 해주면 타입들을 쓸 수 있도록 해준다.

  ```shell
  > npm i @types/lodash
  ```

  ![image-20250313163831896](/Users/edgar/Documents/TILv2/TypeScript/typescript.assets/image-20250313163831896.png)

### .d.ts 파일 이란?

- `@types` 라이브러리를 npm을 통해 설치하면, node_modules 폴더에, 라이브러리 폴더 외에 `@types`라는 폴더가 생기게 된다. 그리고 이 폴더 안에는 수많은 `.d.ts`파일들이 들어가 있다.

- `.d.ts` 파일은 기존 JS 모듈을 타입스크립트에서 사용하기 용이하도록 기존 JS 모듈의 타입정보를 별도의 파일로 따로 선언한 것들이다.

  기본적으로 JS 기반의 라이브러리들은 TS 환경에서 타입이 지정되지 않았기 때문에, 타입체킹이 되지 않아서 라이브러리 코드를 인식하지 못하는 문제가 발생하게 되는데, 이 문제를 `.d.ts`파일을 통해 해결하는 것이다.