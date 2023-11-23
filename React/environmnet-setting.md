# ENV

## babel

- JSX를 변환하기 위한 Babel. JSX를 변환하기 위해서는 `@babel/preset-react`와 같은 React 프리셋을 사용

- Babel은 JS 컴파일러로, 최신 JavaScript 문법이나 JSX와 같은 확장된 문법을 이전 버전의 JavaScript로 변환해주는 역할.

- `babel.config.js`?

  이는 Babel 설정 파일로, 프로젝트 전체에 대한 Babel 설정을 지정. 해당 파일이 프로젝트 루트에 존재할 때 Babel은 이 파일의 설정을 우선시하게 됨.

  cf-1) `webpack.config.js` 파일은 Webpack 설정 파일로, 웹팩 빌드에 대한 설정을 담고 있음

  cf-2) `.babelrc`파일과 `babel.config.js`는 기본적으로 비슷한 역할을 함. 두 파일 모두 Babel 설정을 담고 있고, 사용하는 프로젝트나 환경에 따라 선택할 수 있음. `babelrc` 파일은 주로 프로젝트 내의 특정 디렉토리에 해당하는 설정을 지정할 때 사용하고, `babel.config.js` 파일은 프로젝트 전체에 대한 설정을 지정할 때 사용

- 하나의 config.js 파일에 babel, webpack config를 정의할 수는 없는걸까?

  일반적으로 하나의 파일에 두 가지 설정을 동시에 정의하지는 않는다. 하지만 만약 하나의 파일에 여러 설정을 정의하고 싶다면 해당 파일에서 각 설정을 내보내는 방식은 사용할 수 있을 듯

  ```js
  // config.js
  const webpackConfig = {
      // webpack configuration
  };
  
  const babelConfig = {
      // babel configuration
  };
  
  module.exports = {
      webpackConfig,
      babelConfig
  }
  ```


## webpack

- Webpack은 모듈 번들러(module bundler)로써 FE 웹 개발에서 사용되는 자바스크립트 및 기타 리소스들을 번들링하고 패킹하는 도구

- 모듈 번들러의 주 역할은 **여러 개의 파일로 나누어져 있는 소스 코드 및 리소스들을 하나의 파일로 묶어서 성능을 최적화**하고 개발 과정을 편리하게 만드는 것

- `webpack` 빌드 명령어 이를 실행하면 Webpack은 설정 파일인 `webpack.config.js`에 정의된 설정에 따라 프로젝트의 소스 코드와 리소스를 번들링하여 결과물을 생성

- 웹팩 빌드를 실행하면 아래와 같은 과정을 거치게 됨

  1. Entry Point로부터의 의존성 해석

     `webpack.config.js` 파일에서 정의한 entry point를 기반으로 프로젝트의 모든 의존성을 해석함. 모든 의존성은 JavaScript 파일뿐만 아니라 CSS 파일, 이미지 등 여러 종류의 리소스도 포함될 수 있음

  2. 로더(loader)를 사용한 변환

     정의된 로더들은 프로젝트에서 사용하는 각각의 파일에 대해 실행되어, 필요한 변환을 수행. 예를들어 babel-loader는 ES6/JSX를 일반 JavaScript로 변환하고, CSS 로더는 CSS를 번들에 포함시키거나 스타일 시트를 처리

  3. 번들링과 모듈 결합

     각 파일을 로딩하고 변환한 후, Webpack은 이들을 하나의 번들로 결합. 번들에는 프로젝트의 모든 모듈과 의존성이 포함됨

  4. 결과물 생성

     번들링이 완료되면 설정에 따라서 결과물을 생성함. 주로 `dist` 디렉토리에 `bundle.js`와 같은 파일이 생성되고, 이것이 브라우저에서 로드될 수 있는 최종 결과물이 된다.

- `webpack webpack.config.js --open`

  이렇게 설정을하면 개발 서버를 사용하지 않고 번들링만 하는 것이므로 실시간으로 변경 사항을 감지하거나 자동으로 리로드 되지 않는다.

  따라서 `webpack-dev-server`를 사용하여 변경 사항을 실시간으로 감지하고 브라우저를 자동으로 리로드하는 기능을 제공하도록 해야한다.

  - `webpack-dev-server --config config/webpack.dev.js --open --hot`
    - `--config` : 웹팩 설정 파일을 지정하는 옵션. 일반적으로는 `webpack-dev-server`에게 사용할 웹팩 설정 파일의 경로를 알려준다. 위의 스크립트에서는 `config/webpack.dev.js`파일을 사용
    - `--hot` : HMR(Hot Module Replacement)을 활성화하는 옵션. 이 옵션을 사용하면 코드 수정 시 브라우저를 새로고침하지 않고 모듈이 실시간으로 교체되어 개발 편의성을 높이게 됨.
    - 즉 위 스크립트를 해석해보자면 `config/webpack.dev.js` 파일을 웹팩 설정파일로 사용하고, 개발 서버를 실행할 때 브라우저를 자동으로 열고, HMR을 활성화하라는 것으로 볼 수 있다.

- `webpack.config.js`

  일반적으로 복잡한 프로젝트에서는 `webpack.common.js` 파일에 공통적인 웹팩 설정을 정의하고, 이후 `webpack.dev.js`와 `webpack.prod.js`등의 환경별 설정 파일에서 `webpack.common.js`를 확장하거나 필요한 설정을 추가함.

  - `webpack.common.js`: 공통 설정
  - `webpack.dev.js`: 개발 환경에 필요한 추가 설정
  - `webpack.prod.js`: 프로덕션 환경에 필요한 추가 설정

## 환경변수

.env에는 여러가지 종류가 존재(`.env.local`, `.env.development`, `.env.test`, `.env.production`)

기본적으로 node.js는 production(배포)와 development(개발), test(테스트)로 구분하여 사용. 리액트의 경우 crate-react-app의 실행 명령에 따라 자동으로 NODE_ENV값이 정해짐

```bash
# production 배포 env실행
$ npm run build

# development 개발 env실행
$ npm start

# test 개발 env실행
$ npm run test
```

실행 OS에 따라 환경변수를 설정하는 방법이 다르기에, 환경 변수 시 undefined가 나오는 경우가 있음. 따라서 cross-env와 같은 모듈을 사용하여 순차적으로 사용할 명령어를 package.json에 정의하곤 하는데 이러한 방식은 환경변수가 노출된다는 치명적인 단점이 존재.

이에 `.env.local`, `.env.development`, `.env.test`, `.env.production` 이라는 파일을 만들어서 변수를 넣어 관리를 하게 됨. 리액트에서는 .env 환경 설정 시 변수명에 REACT_APP_ 이라고 앞에 붙여야 인식을 함. 

npm start 시 .env 파일의 실행순서는 아래와 같음

`.env.delopment.local` > `.env.development` > `.env.local` > `.env`

우측부터 순차적으로 실행됨 만약 `.env` 파일만 있다면 `.env` 파일만 실행 됨. `.env.development.local` 파일이 있다면 다른 우선 순위는 무시 됨

npm run build 시 .env 파일도 실행 우선순위가 있는데 아래와 같음

`.env.production.local` > `.env.production` > `.env.local` > `.env`

npm run test 시 .env 파일의 실행 우선순위는 아래와 같음

`.env.test.local` > `.env.test` > `.env.local` > `.env`

- npm run dev와 npm run start의 동작이 다름

  ```json
  {
      "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "dev": "webpack-dev-server --config config/webpack.dev.js --open --hot",
      "build": "webpack --config config/webpack.prod.js",
      "start": "webpack --config config/webpack.dev.js"
    },
  }
  ```

  그 이유는 "start" 스크립트가 webpack-dev-server를 사용하지 않고 직접 webpack을 실행하기 때문. **webpack-dev-server는 개발 서버를 제공하여 변경된 내용을 실시간으로 반영**하고 핫 모듈 교체를 지원하는 반면 **npm run start에서는 단순히 webpack을 실행하므로 변경 사항을 실시간으로 반영하지 않는다.**

  여기서 개발 서버를 사용하기위해서는 start에 `webpack-dev-server`를 추가해주어야만 한다.