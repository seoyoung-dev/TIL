# 5장 레거시 프로젝트를 위한 클래스형 컴포넌트

모든 컴포넌트는 초기화 - 업데이트 - 소멸의 단계를 거친다. 이 과정에서 호출되는 메서드를 생명 주기(Life Cycle) 메서드라고 한다. 한마디로 생명 주기 메서드는 리액트가 컴포넌트를 생성하고 없애는 방법이다.

![R02](../../assets/R02.png)

-   **mount**는 컴포넌트가 브라우저 상에서 나타나는 것, **update**는 컴포넌트의 props나 state가 바뀌었을 때, **unmount**는 컴포넌트가 브라우저 상에서 사라지는 경우를 말함.
-   constructor: 생성자 함수로, 컴포넌트가 처음 브라우저 상에 나타나게 될 때 가장 먼저 실행되는 메서드. constructor 메서드 내부에서 반드시 super 함수를 호출해야 함. 대표적으로 초기 속성값으로부터 상탯값을 만드는 경우 필요.

```jsx
class App extends React.Component {
    constructor(props) {
        super(props);
        console.log("hi");
    }
...
```

-   getDerivedStateFromProps: props로 받은 값을 상탯값에 동기화하고 싶을 때 사용한다. 이는 mounting 과정과 updating 과정에서 props가 바뀌면 실행된다. \*(derive from : ~에서 ~을 얻다)
-   render: 컴포넌트를 정의할 때 반드시 작성해야 하며, 이 메서드의 반환값은 화면에 보여질 내용을 결정한다.
-   componentDidMount: render 메서드의 반환값이 실제 돔에 반영된 직후 호출됨. API 호출을 통해 데이터를 가져올 때 적합. (render 메서드에서는 부수효과를 발생시면 안됨. 서버와 통신하기, 브라우저의 쿠키에 저장하기 등을 부수 효과라고 함.)

```jsx
class App extends React.Component {
    state = {
        isLoading: true,
        movies: [],
    };
    getMovies = async () => {
        const movies = await axios.get(
            "https://yts-proxy.now.sh/list_movies.json"
        );
        this.setState({ movies, isLoading: false });
    };
    componentDidMount() {
        this.getMovies();
    }
    render() {
        const { isLoading } = this.state;
        return <div>{isLoading ? "Loading" : "We are ready"}</div>;
    }
}
```

-   shouldComponentUpdate: 컴포넌트가 업데이트되는 성능을 최적화 시키고 싶을 때 사용. true 값이나 false 값을 반환하며, true 값을 반환한다면 랜더링 프로세스를 거치고 false를 반환한다면 랜더링이 되지 않기 때문에 화면에도 반영되지 않음.
-   componentWillUnMount: 소멸 단계에서 호출되는 유일한 메서드. componentDidMount 메서드가 호출되면 componentWillUnMount 메서드도 호출되는 것이 보장됨. 따라서 componentDidMount 메서드에서 구독하고 componentWillUnMount에서 구독을 해제하는 코드가 자주 사용됨.

<br>
<br>

### 클래스형 컴포넌트 활용예시

```jsx
class App extends React.Component {
    constructor(props) {
        super(props);
    }
    state = {
        count: 0,
    };
    add = () => {
        this.setState(this.state.count + 1);
    };
    minus = () => {
        this.setState((current) => ({ count: current.count - 1 }));
    };

    render() {
        return (
            <div>
                <h1>The number is : {this.state.count}</h1>
                <button onClick={this.add}>Add</button>
                <button onClick={this.minus}>Minus</button>
            </div>
        );
    }
}
```

-   함수형 컴포넌트는 정의한 함수가 return한 값이 화면에 표시되는 반면, 클래스형 컴포넌트는 react component로부터 **확장**되고 화면에 표시할 것을 render 메소드에 넣어야 하는 차이점이 있음.
-   리액트는 자동으로 클래스 컴포넌트의 render 메소드를 실행함.
-   state는 object이고 컴포넌트의 데이터를 넣을 공간을 가짐. 이 데이터는 변할 수 있음.
-   state의 값을 가져오기 위해서는 this.state.isLoading과 같이 this 키워드를 사용해야 함.
-   클릭이벤트 발생 시 add 함수가 실행되게 하기 위해서 this.add()가 아닌 this.add로 명시한다.
-   state의 값을 직접 변경할 경우 render 메서드가 호출되지 않기 때문에, 반드시 setState 함수를 통해 state의 값을 변경해야 함.
-   setState 함수를 호출하면 리액트는 state를 refresh하고 새로운 state와 함께 render 메서드를 호출함.
-   외부의 상태에 의존하지 않기 위해서 this.state.count+1 의 방법 대신 current => ({count: current.count + 1})이 더 좋음.
