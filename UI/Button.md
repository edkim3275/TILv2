# Button :white_square_button:

## 실습

### (React)상황별 활성화 버튼 만들기

```react
const enButtonList = [
  {title: 'a', content: 'a'},
  {title: 'b', content: 'b'}, 
]
const koButtonList = [
  {title: 'ㄱ', content: 'ㄱ'},
  {title: 'ㄴ', content: 'ㄴ'},
  {title: 'ㄷ', content: 'ㄷ'},
]
const Buttons = () => {
  return (
  	<>
    	{enButtonList.map((item, index) => (
  			<button
          className="enBtn"
          key={index}
          onClick={handleActiveButton}
        >
    			{item.title}
    		</button>
  		))}
			{koButtonList.map((item, index) => (
      	...
      ))}
    </>
  )
}
```

```scss
.active {
  background-color: '#3184ff'
}
```

위와 같이 버튼 리스트들에 대한 정보를 화면에 렌더링하고 선택된 버튼의 css를 변경시켜 활성화 UX를 제공하고자 할 때 `handleActiveButton` 로직을 어떻게 짜면 될까

- TRY-1

  default로 활성화 되어있는 버튼이 존재하고, 이후에 클릭이 될 때 변경되는 로직을 구현

  ```react
  const Buttons = () => {
    const handleActiveButton = () => {
      if (e.target.id === "default" && e.target.classList.contains("active")) return
      else if (e.target.id === "default") {
        document.getElementByClassName("active")[0].classList.remove("active");
        e.target.classList.add("active")
      } else {
        if (e.target.classList.contains("active")) return
        else {
          document.getElementByClassName("active")[0].classList.remove("active");
        	e.target.classList.add("active");
        }
      }
    }
    return (
    	{enButtonList.map((item, index) => (
        <button
          id={index===0 ? "default" : ""}
          className="enBtn"
          key={index}
          onClick={handleActiveButton}
        >
          {item.title}
        </button>
      ))}
  		...
    )
  }
  ```

- TRY-2(리팩토링)

  효율적인 리팩토링 방법 중 하나는 코드의 중복을 줄이고 더 유연한 구조를 만드는 것 => 좀 더 간결하게 로직구현

  ```react
  const handleActiveButton = () => {
    if (e.target.classList.contains("active")) return
    else {
      document.getElementByClassName("active")[0].classList.remove("active");
      e.target.classList.add("active")
    }
  }
  ```

- TRY-3

  리액트에서는 HTML 요소를 직접적으로 건드리는 방식은 **지양한다**... 따라서 상태에 따라 해당 요소의 클래스에 변화를 주는 방식이 적합하다고 볼 수 있다

  ```react
  const Buttons = () => {
    const [selectedButton, setSelectedButton] = useState(0);
    const handleActiveButton = (index) => {
      setSelectedButton(index);
    }
    return (
      {enButtonList.map((item, index) => (
        <button
          className={`enBtn ${selectedButton ? 'active': ''}`}
          key={index}
          onClick={handleActiveButton}
        >
          {item.title}
        </button>
      ))}
    )
  }
  ```

   여러가지 종류의 버튼 목록을 관리하고, 각 목록의 선택된 항목을 별도로 추적해야 하는 경우, 상태를 분리하고 컴포넌트를 더 동적으로 관리하는 것이 좋다

  ```react
  const Buttons = () => {
    const [selectedEnButton, setSelectedEnButton] = useState(0);
    const [selectedKoButton, setSelectedKoButton] = useState(0);
    const handleActiveButton = (index, type) => {
      if (type === 'en') {
        setSelectedEnButton(index);
        setSelectedKoButton(0); // 다른 종류의 버튼을 클릭할 때 Ko종류의 버튼은 초기화
      } else if (type === 'ko') {
        setSelectedKoButton(index);
        setSelectedEnButton(0); // 다른 종류의 버튼을 클릭할 때 En종류의 버튼은 초기화
      }
    }
    
    return (
    	{enButtonList.map((item, index) => (
        <button
          className={`enBtn ${selectedEnButton === index ? 'active': ''}`}
          key={index}
          onClick={handleActiveButton}
        >
          {item.title}
        </button>
      ))}
    )
  }
  ```

- TRY-4

  그렇다면 관리해야하는 상태가 10개 이상처럼 보다 많아지는 경우에는 어떻게 해야할까

  ```react
  const Buttons = () => {
    const buttonCategories = [
      {
        title: 'en',
        items: enButtonList
      },
      {
        title: 'ko',
        items: koButtonList
      }
    ]
    const [selectedItems, setSelectedItems] = useState({});
    const handleActiveButton = (tabIndex, index) => {
      setSelectedItems({ ...selectedItems, [tabIndex]: index });
    };
    return (
    	<>
      	{buttonCategories.map((category, categoryIndex) => (
    			<div>
      			{category.items.length > 0 && 
            	category.items.map((item, index) => (
        				<button
                  className={`${selectedItems[categoryIndex] === index ? 'active' : ''}`}
                  key={index}
                  onClick={() => handleActiveButton(tabIndex, index)}
                >
          				{item.title}
          			</button>
        			))
            }
      		</div>
    		))}
      </>
    )
  } 
  ```

  