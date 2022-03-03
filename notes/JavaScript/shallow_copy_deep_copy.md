# shallow copy, deep copy

## 원시 타입과 객체 타입

얕은 복사와 깊은 복사를 이해하기 위해서는 먼저 원시타입과 객체타입의 차이를 알아야 한다.

```jsx
var p1 = 1;
var p2 = 1;
console.log(p1 === p2); // true

var o1 = { age: 3 };
var o2 = { age: 3 };
console.log(o1 === o2); // false
```

위 예시에서 p1과 p2, o1과 o2는 각각 같은 값이 할당 되어 동일한 것처럼 보이지만, 비교연산자를 통해 값을 비교했을 때 p1과 p2는 true를 출력하지만 o1과 o2는 false를 출력한다. 원시값은 값 그대로 저장·할당 되지만, 객체는 참조(reference)에 의해 저장되고 복사되기 때문이다.

(엄밀히 얘기하면 자바스크립트의 모든 데이터 타입은 참조형 데이터이다. 다만 기본형은 주솟값을 복사하는 과정이 한 번만 이뤄지고, 참조형은 한 단계를 더 거치게 된다는 차이가 있다. )

<br>
<br>

## 얕은 복사과 깊은 복사

**얕은 복사**(shallow copy)는 바로 아래 단계의 값만 복사하는 방법이고, **깊은 복사**(deep copy)는 내부의 모든 값들을 하나하나 찾아서 전부 복사하는 방법이다.

```jsx
const obj1 = { a: 1, b: 2 };
const obj2 = obj1;

console.log(obj1 === obj2); // true
```

위 예시처럼 객체를 직접 대입하는 경우 참조에 의한 할당이 이루어지므로 obj1과 obj2는 같은 데이터(주소)를 가지고 있다. 이것이 얕은 복사이다.

얕은 복사처럼 주소를 복사해서 공유하는 것이 아니라, 기존에 있던 객체와 똑같으면서도 독립적인 객체를 만들고 싶다고 가정하자.

```jsx
// spread operator 를 통한 복사
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj };
obj2.a = 100;

console.log(obj1.a); // 1
console.log(obj2.a); // 100

// Object.assign() 메서드를 통한 복사
const obj1 = { a: 1, b: 2 };
const obj2 = Object.assign({}, obj1);

obj2.a = 100;
console.log(obj1.a); // 1
console.log(obj2.a); // 100
```

전개 구문(spread operator)과 Object.assign()을 이용한 방법은 새로운 객체를 생성하는 방식이기 때문에, obj2의 a 프로퍼티에 다른 값을 할당한다면 obj1과 obj2의 a의 프로퍼티 값은 달라진다.

하지만 중첩 객체가 내부에 존재할 경우는 다르다.

```jsx
let user1 = {
    name: "John",
    age: 30,
    urls: {
        blog: "http://blog.com",
        facebook: "http://facebook.com/abc",
    },
};

let user2 = { ...user1 };
user1.urls.blog = "http://updatedUrl.com";

console.log("user1", user1); // user1 ... blog: 'http://updatedUrl.com',
console.log("user2", user2); // user2 ... blog: 'http://updatedUrl.com',
```

위 코드를 보면 user1 중첩객체(urls)의 프로퍼티 값을 변경한 후 user1와 user2 안에 있는 중첩 객체의 프로퍼티를 출력하면 같다는 것을 알 수 있다. 전개구문을 통한 방식과 Object.assign() 방식 모두 1 depth 까지만 deep copy가 가능하고 2 depth 이상이면 shallow copy 가 되는 것이다.

<br>
<br>

## Deep copy를 위한 방법

깊은 복사는 얕은 복사와 달리 내부의 모든 값들을 하나하나 찾아서 전부 복사하는 방법이다. 객체의 프로퍼티 중에서 그 값이 기본형 데이터일 경우에는 그대로 복사하면 되지만 참조형 데이터는 다시 그 내부의 프로퍼티들을 복사해야 한다. 이 과정을 참조형 데이터가 있을 때마다 재귀적으로 수행해야만 비로소 깊은 복사가 된다.

깊은 복사를 수행하는 여러 가지 방법이 있다.

1. 커스텀된 재귀함수 사용

    : 복사를 진행하다가 객체를 만나면 함수를 재귀적으로 실행해 깊은 복사를 실현하는 함수

```jsx
function cloneObject(obj) {
    var clone = {};
    for (var key in obj) {
        if (typeof obj[key] == "object" && obj[key] != null) {
            clone[key] = cloneObject(obj[key]);
        } else {
            clone[key] = obj[key];
        }
    }

    return clone;
}
```

1. JSON

    : 객체를 문자열로 변환하는 JSON.stringify와 문자열을 객체로 변환하는 JSON.parse를 이용하여 변환되기 이전 객체에 대한 참조가 없어지는 원리를 이용 (다만 함수, Date 객체, 정규표현식, Infinity 등등의 데이터는 복사되지 않고 유실된다. 또한 속도가 상대적으로 느리다는 단점도 있다)

```jsx
const obj1 = {
    a: 1,
    b: "string",
    c: {
        name: "Leon",
        age: "29",
    },
};

// Deep Copy 방법
const obj2 = JSON.parse(JSON.stringify(obj1));
```

1.  Lodash의 cloneDeep 함수 사용

    : lodash는 JavaScript의 인기있는 라이브러리 중 하나로, 데이터의 필수적인 구조를 쉽게 다룸

```jsx
const original = { a: { b: 2 } };
let copy = _.cloneDeep(original);
copy.a.b = 100;
console.log(original.a.b); //2
```
