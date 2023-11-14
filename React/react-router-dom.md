# react-router-dom

## PrivateRoute

로그인이후 토큰을 얻은 상태와 같이 어떠한 권한이 있는 상태에서 특정 페이지로 이동할 경우, 해당 페이지가 렌더링되기 이전에 권한 유무를 파악해야 한다.

보통 `useEffect`를 사용하여 조건에 부합하면 페이지를 렌더링을하고 부합하지 않으면 다른 페이지로 다시 이동 시키곤 하는데 이는 해당 url로 한 번은 진입하게 된다는 단점이 존재

```react
// App.tsx
const App = () => {
  return (
  	<div>
    	<Routes>
      	<Route path="/" element={<Main />}/>
      </Routes>
    </div>
  )
}
```

```react
const Main = () => {
  cosnt navigate = useNavigate()
  useEffect(() => {
    // 권한이 없는 경우 로그인 페이지로 이동
    if (!authentication) navigate("/signin")
  }, [])
  return (...)
}
```

이러한 점을 보완할 수 있는 것이 `react-router-dom`에서 제공하는 `PrivateRoute` 라고 볼 수 있음. 해당 라우트로 이동하기 전 해당 라우트가 `PrivateRoute`에 해당한다면 url로 이동하기 이전에 먼저 해당 컴포넌트를 실행하는 것

```react
const PrivateRoute = ({ children }) => {
  const { authentication } = ...
  return authentication ? (
    // 권한이 있을 경우(ex. 로그인권한이 있다면 메인으로)
    <Navigate to="/" />
  ) : (
    // 권한이 없을 경우(ex. 로그인권한이 없다면 로그인페이지)
    <>{children}</>
  )
}
```

이에 대한 적용은 아래와 같이 해준다.

```react
// App.tsx
const App = () => {
  return (
  	<div>
    	<Routes>
      	<Route path="/" element={<PrivateRoute children={<Main />}/>}/>
      </Routes>
    </div>
  )
}
```

