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



