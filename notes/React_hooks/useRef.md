# UseRef

```jsx
const ref = useRef(initialValue);
console.log(ref); // { current : initialValue }
```

useRef 훅은 하나의 객체를 반환하는데 그 객체는 current라는 key를 가지고, 함수의 첫번째 인자로 받아온 초기값을 value로 가진다

```jsx
ref.current = "hello world";
```

ref 객체의 값은 언제든 변경이 가능하다

<br>
<br>

## useRef 훅의 사용 1 : 렌더링과 무관한 값 저장하기

useRef 훅은 useState 훅과 같이 어떤 정보를 저장하는 저장공간의 기능을 한다. 하지만 useState 훅과는 분명한 차이가 있다. useState 훅의 경우 state 값에 변화가 생기면 렌더링이 발생되고 이에 따라 컴포넌트 내부의 변수들이 초기화된다. 반면, useRef 훅은 Ref 값이 변화하더라도 렌더링이 이루어지지 않고 변수들의 값이 계속 유지된다. 따라서 변경해도 렌더링이 발생하지 않는 값을 다루고 싶을 때 useRef를 사용할 수 있다.

예시를 통해 useRef와 useState의 차이점을 더 자세히 알아보자. 화면이 렌더링되는 횟수를 확인할 수 있는 함수를 만들었다고 가정하자.

```jsx
import React, { useState, useEffect } from "react";

export function App(props) {
    const [count, setCount] = useState(1);
    const [renderCount, setRenderCount] = useState(1);

    useEffect(() => {
        console.log("Rendered...");
        setRenderCount(renderCount + 1);
    });

    return (
        <div className="App">
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>+1</button>
        </div>
    );
}
```

위의 코드를 실행해 +1 버튼을 클릭할 경우 State 값에 변화가 생기기 때문에 useEffect 훅의 함수가 호출된다. 이때 useEffect 훅 안에 있는 setRenderCount 함수가 호출되면 State 값이 변화하면서 useEffect가 또 실행되고, 그 안에 있는 setRenderCount가 또다시 호출되고… 이처럼 useEffect와 setRenderCount 함수가 무한히 호출되면서 에러메세지가 뜨게 된다.

```jsx
import React, { useRef, useState, useEffect } from "react";

export function App(props) {
    const [count, setCount] = useState(1);
    const renderCount = useRef(1);

    useEffect(() => {
        renderCount.current = renderCount.current + 1;
        console.log("렌더링 수: ", renderCount.current);
    });

    return (
        <div className="App">
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>+1</button>
        </div>
    );
}
```

이번에는 useState가 아닌 useRef 훅을 사용해서 렌더링 횟수를 카운트해보았다. 화면이 렌더링될 때마다 useEffect 속 함수가 실행되면서 renderCount의 값은 실제로 +1한 값으로 업데이트 되지만, Ref의 값은 변화해도 리렌더링을 유발하지 않기 때문에 앞선 코드와 달리 무한 루프에 빠지지 않는다.

<br>
<br>

## useRef 훅의 사용 2 : DOM 요소에 접근하기

useRef 훅은 DOM 요소에 직접 접근해야 할 경우에도 유용하다. 돔 요소에 포커스를 주거나 돔 요소의 크기나 스크롤 위치를 알고 싶은 경우를 예로 들 수 있다.

```jsx
import React, { useRef, useEffect } from "react";

export function App(props) {
    const inputRef = useRef();

    useEffect(() => {
        inputRef.current.focus();
    });

    return (
        <div className="App">
            <input type="text" ref={inputRef} />
            <button>저장</button>
        </div>
    );
}
```

useRef 훅을 통해서 화면이 처음 렌더링 될 때 input 태그에 focus가 되도록 DOM 요소를 제어하였다.
