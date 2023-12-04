# Browser

## window vs. document

- 두 가지 모두 웹 브라우저 환경에서 사용되는 객체. 

  두 객체 모두 웹 개발에서 핵심적이고, JavaScript에서 웹 브라우저와 상호작용하는 데 필수적.

  다만 서로 역할이 다름.

- 간단히 `window`는 전역 컨텍스트와 브라우저 창을 나타내고, `document`는 현재 웹 페이지의 DOM에 접근하여 문서의 내용을 조작하는 데 사용된다.

- `window` 객체는 `document` 객체를 포함하는 상위 객체로 볼 수 있다. 

  웹 브라우저 환경에서 `window`는 전역 객체로서 모든 JavaScript 코드의 최상위 컨텍스트를 제공하며, 여기에 `document` 객체도 포함된다.

### window

> 전역 스코프를 나타내고, 브라우저 창(또는 탭)을 관리

- **전역 객체**

  `window` 객체는 전역 객체로써(Node,js에서는 `global`) 브라우저 환경에서 모든 JavaScript 코드가 실행되는 컨텍스트를 제공. 

  즉, 모든 전역 변수와 함수는 실제로 `window`객체의 속성이나 메소드

- **브라우저 창(또는 탭)을 나타냄**

  `window` 객체는 현재 열려 있는 브라우저 창(또는 탭)을 나타낸다.

  따라서 `window` 객체를 통해 브라우저 창과 관련된 여러 속성과 메소드를 사용할 수 있다.

- **전역 스코프**

  `window` 객체의 속성으로 선언된 변수는 전역 스코프에서 접근 가능하며, 다른 스크립트 파일에서도 공유될 수 있다.

#### window 속성/메소드

| 속성                                      | 설명                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `window.innerWidth`, `window.innerHeight` | 현재 브라우저 창의 내부 너비와 높이 반환                     |
| `window.outerWidth`, `window.outerHeight` | 현재 브라우저 창의 외부 너비와 높이 반환. 즉, 브라우저 툴바 등을 포함한 전체 창 크기 |
| `window.location`                         | 현재 페이지의 URL 정보를 나타내는 Location 객체 반환         |
| `window.document`                         | 현재 문서(Document) 객체에 대한 참조를 반환                  |

| 메소드                                | 설명                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| `window.alert(msg)`                   | 경고 다이얼로그 표시                                         |
| `window.confitm(msg)`                 | 확인 다이얼로그 표시. 사용자 선택에 따라 true 또는 false 반환 |
| `window.prompt(msg, default)`         | 프롬프트 다이얼로그 표시. 사용자로부터 입력을 받는다.        |
| `window.open(url, target, features)`  | 새로운 브라우저 창 열기                                      |
| `window.close()`                      | 현재 창 닫기                                                 |
| `window.setTimeout(callback, delay)`  | 주어진 시간 지난 후 에 콜백 함수 실행                        |
| `window.setInterval(callback, delay)` | 주기적으로 콜백 함수 실행                                    |
| `window.clearTimeout(timeoutId)`      | `setTimeout`으로 설정한 타임아웃 취소                        |
| `window.clearInterval(intervalId)`    | `setInterval`로 설정한 주기적 실행 중지                      |
| `window.scrollTo(x, y)`               | 브라우저 창을 지정 좌표로 스크롤                             |
| `window.history`                      | 브라우저의 세션 히스토리에 접근하는 History 객체 반환        |
| `window.navigator`                    | 브라우저와 관련된 정보를 나타내는 Navigator 객체 반환        |

### document

> 현재 웹 페이지의 DOM에 접근하여 HTML 문서의 구조를 나타내고 조작

- **DOM(Document Object Model)에 접근**

  `document` 객체는 현재 웹 페이지의 DOM에 접근할 수 있는 인터페이스를 제공.

  HTML 문서의 구조화된 표현을 나타내는 DOM을 JavaScript를 통해 동적으로 조작하는 것이 가능

- **문서의 내용 조작**

  `document` 객체를 사용하여 문서의 내용을 변경하거나 조작할 수 있다.

- **이벤트 처리**

  `document` 객체는 사용자의 동작에 대응하여 이벤트를 처리할 수 있는 매커니즘을 제공.(이벤트리스너 등록, 이벤트 감지)