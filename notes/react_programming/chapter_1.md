# 1장 리액트 프로젝트 시작하기

<aside>
💡 리액트를 왜 사용할까?
가상 돔(virtual dom)을 통해 불필요한 UI 업데이트를 줄여 성능을 향상 시킬 수 있다.
Component와 Hook을 사용하여 작은 단위의, 독립적인 요소로 개발하여 개발자의 생산성과 코드 재사용성을 높일 수 있다.

</aside>

<br>
<br>

### 바벨과 JSX

바벨(babel)은 자바스크립트 코드를 변환해주는 컴파일러다. 최신문법을 지원하지 않는 환경에서 최신 문법을 사용할 수 있게 해준다. 리액트에서는 보통 JSX 문법을 사용하는데, 이는 HTML에서 태그를 사용하는 방법과 유사하여 가독성 있는 코드를 작성할 수 있도록 한다. React 공식문서에는 JSX 문법을 이렇게 설명하고 있다.

> 근본적으로 JSX는 React.createElement(component, props, ...children) 함수에 대한 문법적 설탕을 제공할 뿐입니다.

바벨은 JSX로 작성된 코드를 createElement 함수로 호출하는 코드로 변환해준다. JSX는 기본적으로 HTML 작성법과 유사하지만 속성값을 작성하는 방법에서 차이를 보인다.

-   JSX와 HTML의 차이점

    1. HTML에서 클래스 이름을 부여할 때 class 키워드를 사용했다면, JSX에서는 className 키워드를 사용한다. 자바스크립트의 class 키워드와 이름이 같이 때문이다.
    2. 리액트 컴포넌트를 사용할 때 속성값이 문자열이 아닌 경우에는 중괄호를 사용하여 입력한다.
       <Example title=”good”  width={200} />
    3. JSX는 보통 속성이름을 카멜케이스로 짓는다. (이벤트 객체 역시 마찬가지)
       onclick(x) onClick(o)
    4. style 속성값을 작성할 때는 속성들을 object 형식으로 작성해서 사용한다.

        <Example title=”good” style={{ marginTop: ‘10px’ }} />

바벨이 컴파일러의 역할을 하지만, 만능은 아니다. 변환을 하더라도 브라우저에서 이해할 수 없는 코드들이 생겨날 수 있다. 이러한 코드를 채우기 위해서는 폴리필(Polyfill)을 사용하기도 한다.

<br>
<br>

### 웹팩

웹팩(webpack)은 자바스크립트로 만든 프로그램을 배포하기 좋은 형태로 묶어주는 도구이다. 웹팩을 사용하지 않을 경우 여러 개의 자바스크립트 파일을 script 태그를 이용해서 head 태그 안에 적어줘야 한다. 이는 파일 관리의 측면에서 어려움이 있고 의존성의 문제도 있기 때문에 비효율적이다.

웹팩을 통해 여러개의 자바스크립트 파일을 하나의 파일로 만드는 것이 가능해진다. 웹팩은 ESM(ES6의 모듈 시스템)과 commonJS를 모두 지원한다.

-   ESM과 CommonJS의 차이점
    가장 큰 차이는 ESM는 import와 export를, CommonJS는 require()와 module.exports의 구현체를 사용하는 것이다.

<br>
<br>

# create-react-app

create-react-app은 React 개발환경을 쉽게 구축하는 기능을 하는 라이브러리이다. 바벨와 웹팩 역시 내장되어 있고 ES6의 모든 기능을 지원한다. async await 함수, rest operator(나머지 연산자), spread operator(전개 연산자) 와 같은 주요 문법을 모두 사용할 수 있다.

```
folder
  |-- README.md
  |-- node_modules
  |-- package.json
  |-- public
  |     |-- favicon.ico
  |     |-- index.html - index.js에 의해 랜더링된 결과가 표시됨
  |
  |--src
  |     |-- App.css
  |     |-- App.js - 컴포넌트를 정의, 루트 컴포넌트 기능
  |     |-- App.test.js
  |     |-- index.css
  |     |-- index.js - HTML 템플릿 및 컴포넌트를 조합하여 렌더링하고 실제 표시
  |     |-- logo.svg
  |---- |-- serviceWorker.js
```

index.js와 index.html 파일은 빌드 시 예약된 파일 이므로 삭제해선 안된다.

<br>
<br>

### single page application

리액트는 기본적으로 SPA 방식을 따르고 있다. 이는 사용자가 한 페이지 내에서 머무르면서 필요한 데이터를 받아와서 부분적으로 업데이트하는 방식이다. 전통적인 방식은 페이지가 전환될 때마다 서버에서 렌더링된 페이지를 전달해주는 과정을 거쳤지만, SPA는 렌더링을 클라이언트에서 하기 때문에 자연스러운 페이지 전환이 가능해진다.

-   SPA와 CSR의 차이
    기존에는 여러개의 웹 페이지가 합쳐져서 하나의 웹 사이트가 되었다면 SPA는 기본적으로 단일 페이지로 구성된다. CSR(client-side-rendering)은 SPA와 온전히 같은 것은 아니고, SPA를 구현하는 방식이 CSR이다. 단어에서 유추 가능하듯 클라이언트, 즉 브라우저에서 뷰를 렌더링하는 방식이다. 새로운 데이터가 필요하면 서버 API를 호출해서 필요한 데이터만 새로 불러와서 업데이트 한다.
    ![R01](../../assets/R01.png)

<br>
<br>

### 페이지 이동 방식

> Link

-   클릭 시 바로 이동하는 로직 구현 시에 사용

```jsx
// Router.js
function Router() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Login />} />
        <Route path="/signup" element={<Signup />} />
        <Route path="/main" element={<Main />} />
      </Routes>
    </BrowserRouter>
  );
}

export default Router;

// Login.js

import React from "react";
import { Link } from "react-router-dom";

function Login() {
  return (
    <div>
      <Link to="/signup">회원가입</Link>
    </div>
  );
}

export default Login;
```

> useNavigate

-   페이지 전환 시 추가로 처리해야 하는 로직이 있을 경우 사용
-   ex) 로그인 버튼 클릭 시
    -   회원가입이 되어 있는 사용자 → Main 페이지로 이동
    -   회원가입이 되어 있지 않은 사용자 → SignUp 페이지로 이동

```jsx
import React from "react";
import { useNavigate } from "react-router-dom";

function Login() {
    const navigate = useNavigate();

    const goToMain = () => {
        if (response.message === "valid user") {
            navigate("/main");
        } else {
            alert("가입된 회원이 아닙니다. 회원가입을 먼저 해주세요.");
            navigate("/signup");
        }
    };

    return (
        <div>
            <button className="loginBtn" onClick={goToMain}>
                로그인
            </button>
        </div>
    );
}

export default Login;
```
