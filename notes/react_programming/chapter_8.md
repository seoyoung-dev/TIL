# 8장 서버사이드 렌더링 그리고 Next.js

### Server-side rendering

서버사이드 렌더링이란 서버에서 리액트 코드를 실행하여 렌더링 하는 것을 말한다.

서버사이드 렌더링을 통해 1. 초기 페이지 로딩이 오래 걸리고, 2. SEO가 어려운 CSR(client-side-rendering)의 단점을 보완할 수 있다.

<br>
<br>

## Next.js

<aside>
💡 Next.js란?
’The React Framework for Production’ 
리액트를 기반으로 하는 프레임워크. 리액트를 기반으로 개발 환경을 구축한다는 점에서 CRA와 비슷하지만 Next.js는 서버사이드 렌더링에 특화된 프레임워크라는 점에서 차이가 있다.

</aside>
<br>

+) 프레임워크와 라이브러리의 차이

라이브러리는 단순 활용한 도구들의 집합을 이야기한다. 동작하는 중에 필요한 기능이 있을 경우에만 능동적으로 라이브러리를 사용한다. 반면, 프레임워크는 애플리케이션 코드가 프레임워크에 의해 사용되는 개념으로, 그 틀안에 이미 제어 흐름에 대한 주도성이 내재되어 있다.

<br>
<br>

### Dynamic Routes

별도의 라우팅 처리 없이 pages 폴더 안에 컴포넌트를 생성하면 자동으로 경로가 설정된다.

-   예) /movies, /movies/all
    ```jsx
    pages
    	|- movies
    	| |- index.js
    	| |- all.js
    ```
-   예) /movies/id
    ```jsx
    pages
    	|- movies
    	| |- [id].js
    ```

React에서 react-router-dom을 이용하여 페이지를 전환했다면 Next.js에서는 next/link와 next/router을 이용하여 페이지를 전환한다.

```jsx
import Link from "next/link";

<Link href="/tomato">
    <a>move to tomato</a>
</Link>;
```

```jsx
import { useRouter } from "next/router";

const router = useRouter();
router.push("/home");
```

<br>

### \_document와 \_app

`_document`와 `_app` 파일에 페이지에 공통적으로 적용될 코드를 작성한다. `_app` 은 서버로 요청이 들어왔을 때 가장 먼저 실행되는 컴포넌트로, 페이지에 적용할 공통 레이아웃의 역할을 한다. `_document`는 `_app` 다음에 실행되며 공통적으로 활용될 head 나 body 태그 안에 들어갈 내용들을 커스텀할 때 활용한다.

```jsx
// _app
import NavBar from "../components/NavBar";
import "../styles/globals.css";

export default function App({ Component, pageProps }) {
    return (
        <div>
            <NavBar></NavBar>
            <Component {...pageProps} />
        </div>
    );
}
```

```jsx
// _document
class MyDocument extends Document {
...
    render() {
        return (
            <Html>
                <Head>
                    <link
                        href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700&family=Noto+Serif+KR:wght@200;600;700&family=Roboto:wght@300;500;700&display=swap"
                        rel="stylesheet"
                    />
										<title>초록친구</title>
			              <meta
		                    property="og:title"
		                    content="초록친구 바로가기"
		                    key="greenery_title"
		                />
		                <meta
		                    property="og:description"
		                    content="반려식물을 죽이지 않고 '잘' 기르고 싶은 사람들을 위한 서비스"
		                    key="greenery_description"
		                />
		                <meta property="og:image" content="#" key="greenery_image" />
                </Head>
                <body>
                    <Main />
                    <NextScript />
                </body>
            </Html>
        );
    }
}
```

<br>

### getInitialProps

Data Fetching을 서버에서 하는 방법. 이 방법은 브라우저에서의 연산을 서버와 함께 하면서 미리 데이터를 받아오고 브라우저는 렌더링만 할 수 있기 때문에 속도가 빨라지고, 코드 상의 처리가 깔끔해진 다는 장점이 있다.

Next.js는 `getStaticProps`, `getStaticPaths`, `getServerSideProps` 등 data를 fetching 하는 다양한 방법을 지원한다.

```jsx
export async function getServerSideProps() {
    const { results } = await (
        await fetch(`http://localhost:3000/api/movies`)
    ).json();
    return {
        props: {
            results
        }
    };
}
```

<br>

### Redirects & Rewrites

redirects 속성은 클라이언트가 접근한 경로를 우회하여 전송해주는 방법을 명시하는 Next.js의 설정 속성이다.

rewirtes 속성은 요청 경로를 다른 목적지 경로로 매핑해주는 설정 객체를 배열로 가지는 속성이다.

redirects는 source URL이 destination URL로 바뀌는 것을 유저가 확인할 수 있지만, rewrites는 source URL 만 유저가 확인할 수 있고 destination URL은 유저가 알 수 없다.

```jsx
async redirects() {
    return [
      {
        source: "/old-blog/:path*",
        destination: "/new-blog/:path*",
        permanent: false,
      },
    ];
  }
```

```jsx
async rewrites() {
    return [
      {
        source: "/api/movies",
        destination: `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`,
      },
    ];
}
```

<br>
<br>
<br>

### [Reference]

[https://webclub.tistory.com/458](https://webclub.tistory.com/458) : 프레임워크와 라이브러리의 차이점
