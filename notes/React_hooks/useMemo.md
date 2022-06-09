# UseMemo

useMemo 훅은 계산량이 많은 함수의 반환값을 재활용하는 경우 사용된다. useMemo 훅에서 ‘memo’는 `memoization`을 나타낸다. `memoization`은 동일한 계산을 계속 반복해야 할 때, 이전에 계산한 값을 미리 메모리에 저장하여 불필요한 연산이 반복되지 않도록 하는 것을 말한다.

```jsx
const memo = useMemo(() => {
    return anyFunction();
}, [item]);
```

useMemo 훅은 기본적으로 두개의 인자를 받는데, 첫번째 인자는 콜백함수이다. 콜백함수가 return 하는 값이 useMemo가 return하는 값이 된다. 두번째 인자는 의존성 배열이다. 배열 안의 요소의 값이 업데이트되는 경우 첫번째 인자인 콜백함수를 호출하여 다시 memoization을 한다.(빈 배열일 경우 처음 화면이 렌더링될 때만 호출됨)

기본적으로 함수형 컴포넌트는 하나의 함수로 정의되고, 이 함수는 리렌더링시에 다시 호출 된다. 이때 함수 내부의 모든 변수는 초기화된다. useMemo 훅을 통해서 어떤 조건이 만족되었을 때만 변수가 초기화되도록 할 수 있다.

```jsx
import React, { useState, useMemo } from "react";

export function App(props) {
    const [num1, setnum1] = useState(0);
    const [num2, setnum2] = useState(0);
    const [string, setString] = useState();

    const sum = useMemo(() => {
        console.log("콜백함수 실행");
        return num1 + num2;
    }, [num1, num2]);

    return (
        <div className="App">
            <input
                type="number"
                value={num1}
                onChange={() => setnum1(num1 + 1)}
            />
            <input
                type="number"
                value={num2}
                onChange={() => setnum2(num2 + 1)}
            />
            <h2>두 수를 더한 값 : {sum}</h2>

            <input type="text" onChange={(e) => setString(e.target.value)} />
        </div>
    );
}
```

위의 코드에는 세 개의 input 태그가 있다. 위의 컴포넌트에서 `num1`과 `num2`의 값이 변경되었을 때 useMemo 훅의 콜백함수가 호출된다.
