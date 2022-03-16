# prototype

## 프로토타입이란?

자바스크립트는 프로토타입(prototype) 기반의 언어이다. 프로토타입 기반 언어는 상속을 사용하는 클래스 기반의 언어와 다르게 어떤 객체를 원형으로 삼고 이를 복제(참조)하면서 상속과 비슷한 효과를 얻는다. 많은 프로그래밍 언어들이 클래스 기반인 것을 고려할 때 자바스크립트의 이러한 특징은 주목할 만 하다.

어떤 생성자 함수(Constructor)를 new 연산자와 함께 호출하면 Constructor에 정의된 내용을 바탕으로 새로운 인스턴스(instance)가 생성된다. 이때 instance에는 ** proto **라는 프로퍼티가 자동으로 부여되는데, 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조한다. prototype 객체 내부에는 인스턴스가 사용한 메서드를 저장한다. 그러면 인스턴스에서 ** proto **를 통해 이 메서드에 접근할 수 있다.

```jsx
var Person = function (name) {
    this._name = name;
};

Person.prototype.getName = function () {
    return this._name;
};
```

Person이라는 생성자 함수의 prototype에 getName이라는 메서드를 지정했다.

```jsx
var me = new Person("Mary");
me.__proto__.getName(); // undefined
```

Person의 인스턴스인 me는 ** proto ** \*\*\*\*프로퍼티를 통해 getName을 호출할 수 있다. 여기서 this는 prototype 객체(me. **proto**)를 가리키는 데 prototype 상에는 name 프로퍼티가 없기 때문에 undefined를 출력한다. 즉, undefined의 값이 출력된 것은 이 변수가 호출할 수 있는 함수에 해당하는 것을 의미한다. 이는 getName이 실제로 실행되었다는 것이다.

undefined가 아니라 Mary를 출력하고 싶다면 어떻게 해야할까?

```jsx
var me = new Person("Mary", 28);
me.getName(); // Mary
```

위 코드에서 Mary의 값이 출력되는 것이 가능한 것은 ** proto **가 생략 가능한 프로퍼티이기 때문이다.

** proto **를 생략하지 않으면 this는 me.** proto **를 가리키지만, 이를 생략하면 me를 가리킨다. prototype 객체를 참조하는 ** proto **를 생략하면 인스턴스는 prototype에 정의된 프로퍼티나 메서드를 마치 자신의 것처럼 사용할 수 있다.

<br>
<br>

## 프로토타입의 활용

```jsx
const car = {
    wheels: 4,
    drive() {
        console.log("driving now");
    },
};

const bmw = {
    color: "red",
    navigation: 1,
};

const benz = {
    color: "black",
};

bmw.__proto__ = car;
benz.__proto__ = car;
```

bmw와 benz 모두 wheels 프로퍼티와 drive 메서드를 가지는 것은 동일하다. 이를 통해 car 라는 새로운 객체를 생성하고, 불필요한 코드의 중복을 막을 수 있다.

<br>
<br>

## 프로토타입 체이닝

어떤 데이터의 ** proto ** 프로퍼티 내부에 다시 ** proto ** 프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인(prototype chain)이라 하고, 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝(prototype chaining)이라고 한다. 한 마디로 ** proto **를 따라 탐색하는 것을 프로토타입 체이닝이라고 한다.

```jsx
const a = {
    attr1: "Hello",
};

const b = {
    attr2: "World",
};

a.__proto__ = b;
console.log(a.attr2); // World
```

a 라는 객체에 attr라는 프로퍼티가 직접적으로 없지만 프로토타입 체이닝을 통해 attr2의 값을 출력할 수 있다.

기본적으로 모든 객체의 ** proto **에는 Object.prototype이 연결된다. 프로토타입 체이닝은 Object 라는 생성자의 prototype에 도달하면 멈추게 된다.

+) Object, String, Number, Function, Array, RegExp, Date, Promise 등과 같은 빌트인 생성자 함수는 전역 객체가 생성되는 시점이 생성된다. 전역 객체는 클라이언트 사이드 환경(브라우저)에서는 window, 서버 사이드 환경(Node.js)에서는 global 객체를 의미한다.
