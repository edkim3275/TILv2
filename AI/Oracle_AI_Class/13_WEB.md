# WEB

> Agent만 만들어서는 서비스 제공이 어려우니 그 중에서도 사람들의 접근성이 높은 웹 화면 개발을 위한 HTML, CSS를 간단히 학습하고, python을 사용한 flask, fastAPI에 대해 학습

## HTML5

- head : HTML 문서의 메타데이터(문서 정보)를 정의

- style : 내부에 css 태그 부여

- meta : 웹 페이지 요약 정보

  브라우저에게 "이 문서를 어떻게 해석하고 보여줘야 하는지"알려주는 역할. 즉, 화면에 보이는 내용은 body 태그가 담당하고, 그걸 어떤 기준으로 처리할지는 head의 meta 태그가 담당

- script : javascript 영역

- link : 포함되는 다른 파일 정보(포함시켜야할 css파일이나 js파일에 대한 정보)

- form : HTML에서 입력된 데이터를 서버로 전소할 때 사용

- ...

### 실습

- meta

  ```html
  <head>
    <meta charset="UTF-8">
  	<meta name="viewport" content="width=device-width, initial-scale=1.0">
  	<meta http-equiv="X-UA-Compatible" content="ie=edge">
  </head>
  ```

  `<meta charset="UTF-8">` : 이 문서가 어떤 글자 인코딩(문자표)를 쓰는지 알려줌. UTF-8은 전 세계 대부분의 언어를 표현할 수 있는 문자셋. 만약 이걸 안 쓰면 한글이 �� 식으로 깨질 수 있음

  💬 쉽게 말하면: “이 문서의 글자는 UTF-8 방식으로 읽어줘! 그래야 한글이 안 깨져.”

  `<meta name="viewport" content="width=device-width, initial-scale=1.0">` : **모바일 화면 크기를 조절**하기 위한 태그

  viewport는 "화면에 보여지는 영역"을 의미

  **width=device-width는 "기기의 실제 화면 너비에 맞게 보여줘"**

  Initial-scale=1.0은 "처음 화면을 1배 확대/축소 비율로 보여줘"(데스크톱 브라우저에서는 속성이 무시됨)

  💬 쉽게 말하면: "스마트폰에서도 화면이 깨지거나 너무 작게 나오지 않게, 화면 크기에 맞춰 보여줘!"

  `<meta http-equiv="X-UA-Compatible" content="ie=edge"` : 인터넷 익스플로러가 예전 호환 모드로 열리지 않게 하는 설정(예전에는 IE가 오래된 호환 모드로 사이트를 열어서 화면이 깨지는 경우가 있었음)

  ie=edge는 "가능한 최신 버전으로 렌더링해라"라는 의미

  💬 쉽게 말하면: "IE야, 옛날 방식말고 최신 방식으로 이 페이지를 보여줘" => 현재는 IE 서비스 종료로 불필요한 코드

- a 태그 target 속성

  > "링크를 클릭했을 때 어디에 열 것인가"를 결정

  ```html
  <!-- 5. 새 탭에서 열기 -->
  <p><a href="https://www.google.com" target="_blank">새 탭에서 구글 열기</a></p>
  ```

  target="_self" : 현장 창(혹은 현재 프레임)에서 링크 열기(기본값)

  target="_blank" : 새 탭(또는 새 창)에서 링크 열기

  target="_parent" : 현재 프레임의 부모 프레임에서 링크 열기(iframe이 중첩된 구조에서 의미가 있음)

  target="_top" : 가장 최상단 프레임에서 링크 열기

  프레임 구조(iframe, frameset)를 이해하면 훨씬 명확해진다. 현재는 일반 웹 페이지(프레임이 없는 구조)를 사용하니까 _self, _parent, _top이 거의 비슷하게 보임

  :bulb: _blank로 열 때는 보안 이슈가 하나 있어서 아래처럼 쓰는 것이 좋다.

  ```html
  <a href="https://example.com" target="_blank" rel="noopener noreferrer">열기</a>
  ```

  이렇게 해야 새로 열린 페이지가 원래 페이지(window.opener)에 접근하지 못함.(대부분 막아주지만 습관적으로 써주는게좋음)

- 인라인(inline) 요소 span

  span태그는 기본적으로 inline 요소. 텍스트 라인 안에 존재. 문장안에서 글자처럼 다뤄지고 아래와 같은 특성을 가짐.

  - 줄바꿈을 하지 않는다
  - width, height 값을 직접 줄 수 없다
  - padding과 border는 들어가지만, 그 공간이 줄 간격(line height)을 밀어내지 못한다.

  인라인 요소의 종류

  span, a, b, i, strong, img, label, input, textarea, select 등

  display: Inline-block 설정으로 인라인 처럼 나란히 놓지만 박스처럼 width, height, padding 마음대로 조절가능

- 블록(block) 요소 div

  블록 요소의 종류

  div, p, h1, ul, section 등

## CSS

### 선택자 종류

- 혼합선택자 : 여러개 선택자를 혼합

  ```css
  /* main안에 모든 article */
  main article { ... }
  ```

- 하위선택자 : 부모와 자식 태그를 함께 지정

  ```css
  /* main안에 모든 article */
  main article { ... }
  
  /* main의 직계 자식 article 요소 선택 */
  main > article { ... }
  
  /* main과 article 따로 선택 */
  main, article { ... }
  ```

### 적용시 우선 순위

!important > HTML 내부 지정 > #id > .class-name > 태그이름

### CSS

| 분류           | 속성       | 설명                                                         |
| -------------- | ---------- | ------------------------------------------------------------ |
| 레이아웃 지정  | display    | 어떻게 보여줄 것인가 혹은 감출것인지를 정의<br />none: 감춤, block: 한블록전체 사용, inline: 가로 한줄, inline-block:  혼용 |
|                | float      | 해당 요소를 어떻게 배치할것인지 지정<br />inherit: 부모요소와 동일한 방식, left: 왼쪽, right: 오른쪽, none: 적용x |
|                | position   | static: 기본값, absolute: 절대좌표지정, relative: 상대좌표지정, fixed: 스크롤 상관없이 지정, inherit: 부모요소와 동일한 방식 적용 |
| 박스형태       | width      |                                                              |
|                | height     |                                                              |
|                | margin     | 바깥쪽 여백                                                  |
|                | padding    | 안쪽 여백                                                    |
|                | border     | 테두리                                                       |
| 배경           | background | 배경지정<br />background-color, background-image, background-repeat, background-position |
| 폰트, 글자형태 | font       | 글자 폰트<br />font-style: 글자스타일<br />font-weight, font-size, line-height, font-family, font-variant |
|                | color      | 색상                                                         |
|                | text-align | 텍스트 정렬 방향 left, right, center                         |

- float : 요소를 왼쪽 혹은 오른쪽으로 띄워서, 다른 요소를(특히 텍스트)가 그 주위를 "흐르듯 감싸게" 만드는 속성 (이미지와 텍스트를 나란히 배치하기 위해 만들어짐)

- :visited

  "사용자가 이미 방문한 링크"에 적용되는 스타일

  - <a href="..."> 링크를 클릭해서

  - 한 번이라도 그 주소에 들어갔다가 돌아오면

    브라우저가 “이건 방문한 적 있는 링크구나!”라고 기억해요.

    그리고 그 링크에 :visited 스타일이 적용됩니다.

  - 사용자의 "방문 이력"을 유추할 수 있어서 브라우저들이 보안상 제한을 걸어둠. => 지금은 :visited로 바꿀 쑤 있는 속성이 매우 제한적

- 가상 클래스(pseudo-class)

  HTML에 별도 class 이름이 없어도, 상태(state)에 따라 자동으로 스타일을 적용할 수 있게 해주는 기능

  | 선택자   | 설명             |
  | -------- | ---------------- |
  | :hover   | 마우스 올렸을 때 |
  | :active  | 클릭 중일 때     |
  | :focus   | 포커스 된 상태   |
  | :visited | 방문한 링크      |
  | :checked | 체크박스 선택됨  |

- background-image

  요소의 배경에 이미지 넣는 속성

  ```css
  div {
    background-image: url("image.jpg")
  }
  ```

  지정된 이미지가 배경으로 깔림

  보통은 background-color와 함께 써서 색 + 이미지를 함께 표현

  이미지를 없애고 싶다면 속성값으로 none을 주면 됨

- background-repeat

  배경 이미지가 반복되는 방식을 지정하는 속성

  ```css
  div {
    background-image: url("repeat-pattern.jpg");
    background-repeat: repeat; /* 기본값 */
  }
  ```

  repeat : 가로 + 세로 반복

  repeat-x : 가로만 반복

## flask

- Flask app 구조

  ```
  **app/**
  |_app.py
  |_**templates/**
  	|_base.html
  	|_index.html
  	|_add.html
  |_**static/**
  	|_js/
  		|_mycode.js
  	|_css/
  		|_style.css
  ```

- jinja 템플릿

  Flask에서 HTML내에 데이터처리를 위한 template 처리 엔진

  python 변수를 HTML에서 표시가능하고, if, for 등 간단한 제어문 적용가능

- Flask.g

  Flask의 전역공간

  ```python
  from Flask import g
  
  g.username = logged_user
  ```



