# React Tips & Cautions

## Avoid Direct DOM

>  "직접 DOM에 접근하는 것은 지양해야 한다."에 대한 이유를 찾아서

### 브라우저 렌더링

- 브라우저 구조

  사용자 인터페이스, 브라우저 엔진, 렌더링 엔진, 네트워크, JS 해석기, 클라이언트 서버, 데이터 스토리지

- 렌더링되는 과정

  데이터 서버로부터 요청 => 데이터 스트림 => 스트링 문자열로 변환(encoding) => 의미있는 토큰으로 변환(tokenization) => 노드 => DOM parse과정

  DOM, CSSOM (render tree) => layout => paint => composite

  layout => paint => composite 과정을 반복(Reflow, Repaint)

  정확한 px값을 계산하는 `layout`단계, 실제로 여러개의 레이어로 나누어 그리는 `paint`단계, 그리고 이를 합쳐서 보여주는 `composite` 단계

- 화면의 DOM이 수정될 때마다(요소의 위치, 크기 변경, 폰트/이미지 변경, 윈도우 리사이징 등) Reflow, Repaint 작업을 반복해야한다.

  > layout부터 다시 시작하는(Reflow작업) 스타일
  >
  > ```css
  > width height padding margin border
  > display position float overflow
  > top left right bottom
  > font-size font-familiy font-weight
  > text-align vertical-align line-height
  > clear white-space
  > ```

  작은 변화에도 처음부터 동일한 작업을 반복하고, 규모가 커질수록 렌더링 부담이 커져서 VirtualDOM 방식이 나오게 됨

### VirtualDOM

- VirtualDOM은 특정 기술이라기보다는 위 과정을 줄이고, 보다 효율적인 렌더링을 위해서 실제 DOM과 동기화하는 프로그래밍 개념으로 볼 수 있다.

- 리액트에서는 렌더링 과정에서 2가지 페이스를 거치는데,

  1. render phase : 가상 DOM을 만들고, 실제 DOM과 비교해서 업데이트가 필요한 부분을 찾음

     `Reconciliation(조화)` : 컴포넌트의 가상 DOM(React 엘리먼트 트리)을 만들고 이전 가상 DOM과 비교하여 변경된 부분을 찾음

     `Render(렌더)` : 변경된 부분을 기반으로 실제 DOM 업데이트를 수행하기 위해 컴포넌트의 `render` 메서드 호출. 다만 이 단계에서는 실제로 DOM 업데이트가 이루어지지 않고, React는 변경된 내용을 기록하기 위해 가상 DOM에 대한 작업을 수행함.

  2. commit phase : 실제 업데이트 부분

     `Pre-commit` :DOM 업데이트가 실제 이루어지기 전에 수행되는 작업으로, 예를들어 레이아웃과 같은 최적화 작업이 이루어짐

     `Commit` : 변경된 부분이 실제 DOM에 반영.

- 리액트는 위와같이 가상 DOM을 사용하여 실제 DOM에 대한 추상화를 제공하며, 상태(state)에 따라서 UI를 업데이트하게됨.

### DOM에 직접접근

- 따라서 직접 document에 접근하는 것은 리액트가 Virtual DOM을 통해 관리하고 있는 상태와 실제 DOM 간의 불일치를 초래할 수 있다. 이는 예측할 수 없는 결과를 가져올 수 있고, 리액트가 제공하는 성능 이점을 상실하게 됨.

- 그래서 직접접근을 하지 말라는건가?

  지양하라는 것. 예를들어 스크롤 이벤트를 처리할 때, `window`객체나 `document` 객체 중 하나는 사용하게 된다. 이러한 경우에는 컴포넌트가 마운트 및 언마운트될 때 이벤트 리스너를 등록/제거하는 방식으로 `useEffect`를 활용하여 처리하는 것이 일반적

  ```react
  const ScrollComponent = () => {
    useEffect(() => {
      const handleScroll = () => {
        // 스크롤 이벤트 발생시 실행
      }
      window.addEventListener('scroll', handleScroll);
      return () => {
        window.removeEventListener('scroll', handleScroll)
      }
    }, [])
  }
  ```

  이외에도 외부 라이브러리와 통합, 특정 *브라우저 이슈* 처리, 포커스 설정, 외부 스크립트 통합 등 비교적 드물지만 몇 가지 상황에서 필요할 수 있다.

- *브라우저 이슈*? 주로 다양한 브라우저 간의 호환성 문제 혹은 특정 브라우저에서 제공되는 API에 대한 차이로 인해 발생할 수 있다.

  1. CSS 스타일 처리의 불일치

     ```javascript
     // 특정 브라우저에서 스타일이 정상적으로 적용되지 않을 때
     document.getElementById('el').style.width = '10px'
     
     // IE에서 flex 속성이 제대로 동작하지 않을 때
     document.getElementById('myElement').style.display = '-ms-flexbox';
     document.getElementById('myElement').style.display = '-webkit-flex';
     document.getElementById('myElement').style.display = 'flex';
     ```

  2. 이벤트 처리의 차이

     ```javascript
     // 특정 브라우저에서 이벤트가 제대로 동작하지 않을 때
     document.getElementById('el').addEventListener('click', () => {
       ...
     })
     ```

  3. 브라우저 간 API 차이

     ```javascript
     // 특정 브라우저에서만 지원하는 API를 사용할 때
     if (document.browserSpecificAPI) {
       document.browserSpecificAPI();
     }
     
     // IE에서만 지원되는 API를 사용할때
     if (document.all) {
       // document.all은 Internet Explorer에서만 지원됨
     }
     ```

  4. 렌더링 엔진 차이
     ```javascript
     // 특정 브라우저에서 레이아웃이나 스타일 계산에 문제가 있을 때
     document.getElementById('el').stlye.display = 'flex'
     ```

- 직접 접근하는 방식 대신에 DOM 요소 접근(`ref`)

  React에서는 보통 `ref`와 상태(state)를 활용하여 DOM 요소에 접근함으로써 가상 DOM과 상태 관리를 유지하면서 필요한 DOM을 조작함

  ```react
  const Component = () => {
    const targetElementRef = useRef(null);
    
    useEffect(() => {
      if (targetElementRef.current) {
        targetElementRef.current.style.color = 'red';
      }
    }, [])
    
    return (
    	<>
      	<div ref={targetElementRef}>content</div>
      </>
    )
  }
  ```

  

  

