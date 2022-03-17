# this

this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다. 실행 컨텍스트는 함수를 호출할 때 생성되므로 this는 함수를 호출할 때 결정된다. 함수를 호출하는 방식에 따라 값이 달라진다.

## 전역 공간에서의 this

전역 공간에서 this는 전역 객체를 가리킨다. 컨텍스트를 생성하는 주체가 전역 객체이기 때문이다.

```jsx
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```

a를 호출했을 때 1이 나오는 것은 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 전역 스코프에서 해당 프로퍼티 a를 발견했기 때문이다. window.a가 1이 출력되는 것은 전역변수를 a를 선언하면서 자바스크립트 엔진이 이를 전역객체의 프로퍼티로 할당했기 때문이다. 따라서 a는 (window.)이 생략되었다고 봐도 무방하다. this가 가리키는 것 역시 전역객체 이므로 this.a을 출력하면 1을 확인할 수 있다.

<br>
<br>

## 메서드로서 호출할 경우 메서드 내부에서의 this

함수와 메서드는 독립성이라는 측면에서 차이를 보인다. 함수는 그 자체로 독립적인 기능을 수행하지만, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행한다. 자바스크립트는 상황별로 this 키워드에 다른 값을 부여하게 함으로써 이를 구현했다.

어떤 함수를 호출할 때 그 함수 이름 앞에 객체가 명시돼 있는 경우는 메서드로 호출한 것이고, 그렇지 않은 모든 경우에는 함수로 호출된 것이다.

```jsx
var func = function (x) {
    console.log(this, x);
};
func(1); // Window { ... } 1

var obj = {
    method: func,
};
obj.method(2); // { method: f } 2
```

위 예시에서 같은 func 함수를 호출했지만 결과는 다르다. 원래의 익명함수는 그대로인데 이를 변수에 담아 호출한 경우와 obj 객체의 프로퍼트에 할당해서 호출한 경우에 this가 달라지는 것이다.

이를 통해 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 무조건 메서드가 되는 것이 아니라 객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않으면 함수로 동작한다는 것을 확인할 수 있다.

<br>
<br>

## 함수로서 호출할 경우 함수 내부에서의 this

일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩된다.

```jsx
// 중첩 함수
function foo() {
    console.log("foo's this ", this); // window
    function bar() {
        console.log("foo's this ", this); // window
    }
    bar();
}
foo();
```

메서드 내부의 중첩 함수나 콜백 함수의 this 바인딩을 메서드의 this 바인딩과 일치시키기 위해서는 this 바인딩을 변수에 할당해서 사용하거나, this를 명시적으로 바인딩할 수 있는 Function.prototype.apply, Function.prototype.call, Function.prototype.bind 메서드를 사용해야 한다.

ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하기 위해서 **화살표 함수**(arrow function)을 도입했다. 화살표 함수는 this를 바인딩 하는 과정 자체가 빠지게 되면서 상위 스코프의 this를 그대로 사용할 수 있다.

<br>
<br>

## 생성자 함수 내부에서의 this

객체지향 언어에서 생성자를 **클래스**(class), 클래스를 통해 만든 객체를 **인스턴스**(instance)라고 한다. 생성자는 구체적인 인스턴스를 만들기 위한 일종의 틀이다. new 명령어와 함께 함수를 호출하면 해당 함수가 생성자로서 동작하게 된다. 그리고 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.

```jsx
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20

// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다.
const circle3 = Circle(15); //
console.log(circle3); // undefined
```

위 예제에서 new 명령어와 함께 Circle 함수를 호출해서 변수 circle1, circle2에 각각 할당했다. 출력해보니 circle1와 circle2는 각각 Cat 클래스의 인스턴스 객체이고, 생성자 함수 내부에서의 this는 각각 circle1 인스턴스를, circle2 인스턴스를 가리킴을 알 수 있다.
