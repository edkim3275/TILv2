# Input

## 실습

### (React) 커스텀 파일 요소 만들기

input요소의 type을 file로 지정할 경우에 파일명을 확인할 수 있는 부분과 파일선택을 하는 버튼을 커스텀해보자.

기본적으로 `<input type="file" />`요소를 만들경우 아래와 같이 렌더링 된다.

![default_file_input](https://github.com/edkim3275/TILv2/assets/77393619/5fb1edae-e008-4f05-b007-9f30604a63fc)

이러한 요소를 원하는 방식대로 커스텀 하는 방식을 생각해보면

- TRY-1

  1. 기본 input요소는 화면에서 지우고(`display="none"`)
  2. id와 연결되어있는 label요소와 업로드된 파일정보를 보여줄 inline요소를 사용

  ```react
  const CustomFileInput = () => {
    const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
      const file = event.target.files[0];
      setFileName(file);
    }
    return (
    	<div className="custom-file-input">
      	<input id="file-input" type="file" onChange={handleChange} />
        <label htmlFor="file-input">파일선택</label>
        <span>{fileName}</span>
      </div>
    )
  }
  ```

  ```scss
  .custom-file-input {
    input[type="file"] {
      display: none;
    }
    label {
      ...
    }
    span {
      ...
    }
  }
  ```

  ![custom_file_input](https://github.com/edkim3275/TILv2/assets/77393619/ec43c9c2-27cf-4d53-a8e4-eabb83707806)

- TRY-2

  inline요소에도 이벤트 달아줌으로써 자연스럽게 파일 첨부할 수 있도록하기

  ```react
  const CustomFileInput = () => {
    const inputRef = useRef(null);
    ...
    
    return (
    	<div className="custom-file-input">
      	<input id="file-input" type="file" onChange={handleChange} />
        <label htmlFor="file-input">파일선택</label>
        <span onClick={() => {
            if (inputRef.current) {
              inputRef.current.click();
            }
          }}>{fileName}</span>
      </div>
    )
  }
  ```

  