# Next.js

## 개념 정리

### 파일 기반 라우팅(File-based Routing)

```
app/
	page.tsx → /
	about/page.tsx -> /about
	blog/[id]/page.tsx → /blog/123
```

### 렌더링 방식

| 방식                        | 설명                             | 예시                            |
| --------------------------- | -------------------------------- | ------------------------------- |
| CSR(Client-Side Rendering)  | 브라우저에서 렌더링              | 일반 React 앱                   |
| SSR(Server-Side Rendering)  | 서버에서 HTML을 미리 만들어 전송 | getServerSideProps              |
| SSG(Static Site Generation) | 빌드 시 미리 HTML 파일 생성      | 블로그나 포트폴리오에 자주 사용 |

Next.js는 이 3가지를 상황에 따라 섞어 쓰는 것이 가능한 하이브리드 프레임워크

### 렌더링 순서

1. 서버에서 먼저 HTML 생성
   - 이때는 localStorage 존재하지 않음(window 없음)
2. 브라우저가 HTML을 받아서 hydrate 시작

### API Routes

Next.js 안에서 간단한 백엔드 API 생성이 가능

예: /app/api/hello/route.ts

```js
export async function GET() {
  return Response.json({ message: "Hello from Next.js API"})
}
```

→ /api/hello 로 접속하면 JSON 응답을 줌.

라우팅 특수 파일 : page.tsx, layout.tsx, loading.tsx, not-found.tsx, error.tsx, template.tsx, route.ts

### 데이터 패칭

- fetch() 내장 캐싱/리벨리데이트 정책

### Image, Font, Metadata 최적화

- next / image : 자동 리사이즈, lazy-loading
- next / font 구글폰트 자동 최적화
- metadata : SEO 태그 자동 설정

### 개발 흐름

1. npx create-next-app@latest my-app
2. cd my-app
3. npm run dev → http://localhost:3000 접속
4. app/page.tsx 파일을 수정하며 UI 개발
5. /app/api 폴더에 백엔드 API 작성
6. Vercel에 git push만 해도 자동 배포됨

### use client

```ts
'use client';
```

- 파일의 상단에 해당 문구를 작성하게 되면 이 파일이 클라이언트 컴포넌트/훅에서 실행된다는 선언.
- Next.js에서는 서버 컴포넌트가 기본이라 브라우저 훅을 쓰려면 해당 문구가 반드시 필요함.

## React

### useCallback

- useCallback(fn, deps)은 함수의 "참조(아이덴티티)"를 메모이즈함

- 리액트에서 자식 컴포넌트에 콜백을 props로 내려줄 때, 혹은 useEffect, useMemo의 의존성으로 쓸 때, 함수 참조가 매 렌더마다 바뀌면 불필요한 리렌더/재실행이 발생

- useCallback을 쓰면 defaultMsg가 바뀌지 않은 한 같은 handleError 함수를 재사용하므로, 자식 메모 컴포넌트(memo)의 리렌더를 줄이거나, 의존성 배열을 안정적으로 유지하는 것이 가능해짐

- 의존성 배열 [] 내에 있는 요소의 값이 바뀌면 새 콜백으로 교체해도 된다는 의미.

- 그래서 useCallback "언제" 씀?

  써야 하는 경우

  - 콜백을 자식 컴포넌트 props로 내려보내고, 그 자식이 React.memo/memo로 리렌더 최적화를 하고 있는 경우
  - 콜백을 다른 훅(useEffect, useMemo)의 의존성에 넣어야 할 때 => 참조가 바뀌면 원치 않는 재실행이 발생하므로 고정 필요

  굳이 안 써도 되는 경우

  - 해당 콜백을 외부로 전달하지 않고, 내부에서만 즉시 호출하는 상황

## TIL

### Hydration mismatch

