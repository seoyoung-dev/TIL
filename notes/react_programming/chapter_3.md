# 3장 중요하지만 헷갈리는 리액트 개념 이해하기

### 상태값과 속성값

UI 데이터는 컴포넌트 내부에서 관리되는 상태값과 부모 컴포넌트에서 내려주는 속성값으로 구성된다.

```jsx
let color = 'red';
function MyComponent() {
	function onClick() {
		color = 'blue';
	{
	return (
		<button style={{backgroundColor: color}} onClick={onClick}>
			좋아요
		</button>
	)
}
```

위의 예시는 상태값을 사용하지 않는 코드이다. MyComponent 함수를 실행하면 color 식별자의 값은 의도대로 blue로 변경이 되지만 실제 화면에서는 여전히 빨간색의 버튼이 나타나게 된다. 이것은 리액트가 UI 데이터가 변경되었다는 사실을 모르기 때문이다. 상태값을 이용하기 위해서는 다음과 같이 코드를 변경하는 작업이 필요하다.

```jsx
import React, { useState } from 'react';

function MyComponent() {
const [color, setColor] = useState('red');
	function onClick() {
		setColor('blue');
	{
	return (
		<button style={{backgroundColor: color}} onClick={onClick}>
			좋아요
		</button>
	)
}
```

위의 코드는 useState 훅을 사용한다. useState 에서 사용된 setColor 함수가 호출되면 상태값이 변경되고 해당 컴포넌트가 다시 렌더링된다.

앞서 속성값은 부모 컴포넌트가 전달해주는 데이터라고 했다.

```jsx
function Title(props) {
    return <p>{props.title}</p>;
}

function Todo() {
    const [count, setCount] = useState(0);
    function onClick() {
        setCount(count + 1);
    }
    return (
        <div>
            <Title title={`현재 카운드:${count}`} />
            <button onClick={onClick}>증가</button>
        </div>
    );
}
```

위의 코드에서 count라는 상태값에 변화가 생기면 부모 컴포넌트인 Todo가 다시 렌더링되고 자식 컴포넌트인 Title 컴포넌트는 새로운 title 속성값을 내려받는다. 상태값과 속성값을 모두 이용한 예시이다. (속성값이 변경될 때만 렌더링되길 원한다면 React.memo를 이용할 수도 있다)

속성값은 상위 컴포넌트에서 관리하기 때문에 해당 컴포넌트 안에서 수정이 불가하다. title 데이터를 수정하고 싶다면 title의 상태값을 가진 컴포넌트에서 관리하는 상태값 변경함수를 이용해야 한다.

<br>
<br>

### 리액트 요소와 가상 돔

브라우저에서 돔을 변경하는 것은 비교적 오래 걸리는 작업이다. 따라서 빠른 렌더링을 위해서 돔 변경을 최소화해야 하고, 이를 위해 리액트는 메모리에 가상 돔을 올려놓고 이전과 이후의 가상 돔을 합리적인 알고리즘으로 비교해서(diffing) 변경된 부분만 실제 돔에 반영한다. (가상 돔은 DOM의 구조만 간결히 흉내낸 자바스크립트 객체이다. 실제 돔보다 상대적으로 가벼운 가상 돔으로 시뮬레이션을 돌려서 가장 경제적으로 돔에 변화를 가하는 방법을 찾아낸다)

데이터 변경에 의한 화면 업데이트는 랜더단계와 커밋단계가 있다. 렌더단계에서는 실제 돔에 반영할 변경 사항을 파악하고, 커밋단계에서는 변경 사항을 실제 돔에 반영한다.

<br>
<br>

### 리액트 훅 기초

-   useState
    -   useState 훅은 상태값을 추가하고 변경하는 기능
    -   useState 훅은 배열을 반환하는데, 배열의 첫번째 원소는 상태값이고 두번째 원소는 상태값 변경함수이다
    -   상태값 변경함수가 호출되면 해당 컴포넌트를 다시 그리고 이때 자식 컴포넌트도 같이 렌더링된다
    -   상태값 변경 함수로 입력된 함수는 자신이 호출되기 직전의 상태값을 매개변수로 받는다
    -   리액트는 가능하다면 상태값 변경을 배치(batch: 여러개의 state 업데이트를 하나의 리렌더링으로 묶는 것)로 처리한다

```jsx
function MyComponent() {
	const [count, setCount] = useState(0);
	function onClick() {
		setCount(prev => prev+1);
		setCount(prev => prev+1);
	}
	console.log('render called');
......
}
```

-   useEffect

    -   useEffect 훅에 입력된 함수는 렌더링 결과가 실에 돔에 반영된 후 호출된다
    -   API를 호출하거나 이벤트 처리 함수를 등록하고 해체하기 위해 사용된다
    -   useEffect 훅의 두 번째 매개변수로 배열을 입력하면, 배열의 값이 변경되는 경우에만 함수가 호출된다(의존성 배열)

        ```jsx
        const [isOn, setIsOn] = useState(false);

          useEffect(() => {
           fetch('http://example.com')
             .then(res => console.log(res))
          }, [isOn]);
        }
        ```

+) 의존성이 없을 경우 매 렌더링마다 실행된다

<br>
<br>

### 리액트 내장 훅

-   기본 Hook
    -   `[useState](https://ko.reactjs.org/docs/hooks-reference.html#usestate)` : 컴포넌트에서 상태값 관리
    -   `[useEffect](https://ko.reactjs.org/docs/hooks-reference.html#useeffect)` : 컴포넌트에서 부수 효과 처리
    -   `[useContext](https://ko.reactjs.org/docs/hooks-reference.html#usecontext)` : Consumer 컴포넌트 없이 콘텍스트 사용
-   추가 Hooks
    -   `[useReducer](https://ko.reactjs.org/docs/hooks-reference.html#usereducer)` : 컴포넌트의 상태값을 리덕스처럼 관리하기
    -   `[useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback)` : 메모이제이션을 위한 훅
    -   `[useMemo](https://ko.reactjs.org/docs/hooks-reference.html#usememo)` : 메모이제이션을 위한 훅
    -   `[useRef](https://ko.reactjs.org/docs/hooks-reference.html#useref)` : 렌더링과 무관한 값 저장
    -   `[useImperativeHandle](https://ko.reactjs.org/docs/hooks-reference.html#useimperativehandle)` : 함수형 컴포넌트
    -   `[useLayoutEffect](https://ko.reactjs.org/docs/hooks-reference.html#uselayouteffect)` : 부수 효과 함수를 동기로 호출
    -   `[useDebugValue](https://ko.reactjs.org/docs/hooks-reference.html#usedebugvalue)` : 커스텀 훅의 내부 상태를 관찰 가능

<br>
<br>

### 컨텍스트 API

상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달할 때 속성값이 사용된다고 언급했는데, 만약 하위 컴포넌트가 상위 컴포넌트에서 상당히 멀리 떨어져 있다면 중간에 있는 컴포넌트들은 기계적으로 속상값을 전달하는 코드를 반복해야한다. 이는 번거롭고 복잡한 과정을 생략하고, 상위 컴포넌트에서 하위에 있는 모든 컴포넌트에 직접 데이터를 전달할 수 있도록 하는 것이 컨텍스트 API이다.

```jsx
React.createContext(dafaultValue) => {Provider, Consumer}
```

컨텍스트 API를 사용하기 위해서는 위와 같은 createContext 함수를 호출해야 한다. Provider 컴포넌트를 이용해서 데이터를 전달하고 Consumer 컴포넌트를 이용해서 데이터를 사용할 수 있다.

Consumer 컴포넌트는 데이터를 찾기 위해서 상위로 올라가면서 가장 가까운 Provider 컴포넌트를 찾는다.

혹은 useContext를 이용하여 Consumer 컴포넌트를 사용하지 않고 부모 컴포넌트에서 전달된 콘텍스트 데이터를 사용할 수 있다.

```jsx
function ChildComponent() {
    const user = useContext(UserContext);
    console.log(`user: ${user.name}`);
}
```

+) 전역 상태관리 라이브러리인 Recoil, Mobx, Redux 등을 통해서도 동일한 기능을 수행할 수 있다
