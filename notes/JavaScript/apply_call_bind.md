# apply/call/bind

apply, call, bind 메서드는 Function.prototype의 메서드다. 이것들은 모든 함수가 상속받아 사용할 수 있는 메서드이다. 이 메서드를 통해 함수의 호출 방식과 관계없이 this를 지정할 수 있다.

<br>
<br>

## call

어떤 함수에 call 메서드를 사용하고 this를 객체로 넘기면 해당 함수의 this를 특정값으로 지정할 수 있다.

```jsx
const jennie = {
    name: "Jennie"
};

function update(gender, age) {
    this.gender = gender;
    this.age = age;
}

update.call(jennie, "female", 22);
console.log(jennie); // { name: "Jennie", gender: "female", age: 22 }
```

<br>
<br>

## apply

call과 동일하게 동작한다. 매개변수를 배열로 받는다는 점에서 차이가 있다.

```jsx
update.apply(jennie, ["female", 22]);
console.log(jennie); // { name: "Jennie", gender: "female", age: 22 }
```

<br>
<br>

## bind

함수의 this 값을 영구히 바꿀 수 있다.

```jsx
const jennie = {
    name: "Jennie"
};

function update(gender, age) {
    this.gender = gender;
    this.age = age;
}

const updateJennie = update.bind(jennie);
updateJennie("female", 22);
console.log(jennie); // { name: "Jennie", gender: "female", age: 22 }
```

```

```
