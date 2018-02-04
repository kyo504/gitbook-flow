---
layout: guide
---

제네릭(때로는 다형성 타입으로 불리는)은 타입을 추상화하는 방법입니다.

아래 전달받은 함수를 그대로 반환하는 `identity` 함수를 작성한다고 생각해보죠.

```js
function identity(value) {
  return value;
}
```

타입이 어떤 것이든 될 수 있기 때문에 이 함수에 대해 특정 타입을 기술하는데 어려움을 겪을 수 있습니다.

```js
function identity(value: string): string {
  return value;
}
```

대신 함수안에 제네릭을 만들고 다른 타입을 대신해서 사용할 수 있습니다.

```js
function identity<T>(value: T): T {
  return value;
}
```

제네릭은 함수, 함수 타입, 클래스, 타입 별칭, 그리고 인터페이스에서 사용할 수 있습니다.

### 제네릭 문법 <a class="toc" id="toc-syntax-of-generics" href="#toc-syntax-of-generics"></a>

제네릭을 문법적으로 표현하는 여러가지 방법이 있습니다.

##### 함수 제네릭 <a class="toc" id="toc-functions-with-generics" href="#toc-functions-with-generics"></a>

함수는 파라미터 목록 앞에 타입 파라미터 목록 `<T>`를 붙여서 제네릭을 생성할 수 있습니다.

함수 내부의 파미미터 혹은 반환 타입같은 다른 타입에도 제네릭을 사용할 수 있습니다.

```js
function method<T>(param: T): T {
  // ...
}

function<T>(param: T): T {
  // ...
}
```

##### 함수 타입 제네릭 <a class="toc" id="toc-function-types-with-generics" href="#toc-function-types-with-generics"></a>

함수 타입은 일반 함수처럼 타입 파라미터 목록 앞에 타입 파라미터 목록 `<T>`를 추가해서 제네릭을 생성할 수 있습니다.

함수 내부의 파미미터 혹은 반환 타입같은 다른 타입에도 제네릭을 사용할 수 있습니다.

```js
<T>(param: T) => T
```

이렇게 생성된 함수 타입 제네릭은 자신의 타입으로 사용됩니다.

```js
function method(func: <T>(param: T) => T) {
  // ...
}
```

##### 클래스 제네릭 <a class="toc" id="toc-classes-with-generics" href="#toc-classes-with-generics"></a>

클래스는 클래스 이름 뒤에 타입 파마리터 목록을 추가해서 제네릭을 만들 수 있습니다.

```js
class Item<T> {
  // ...
}
```

클래스 안에 있는 프로퍼티 타입, 메서드 파라미터 타입, 반환 타입도 같은 방법으로 제네릭을 사용할 수 있습니다.

```js
class Item<T> {
  prop: T;

  constructor(param: T) {
    this.prop = param;
  }

  method(): T {
    return this.prop;
  }
}
```

##### 타입 별칭 제네릭 <a class="toc" id="toc-type-aliases-with-generics" href="#toc-type-aliases-with-generics"></a>

```js
type Item<T> = {
  foo: T,
  bar: T,
};
```

##### 인터페이스 제네릭 <a class="toc" id="toc-interfaces-with-generics" href="#toc-interfaces-with-generics"></a>

```js
interface Item<T> {
  foo: T,
  bar: T,
}
```

## 제네릭의 동작 방식 <a class="toc" id="toc-behavior-of-generics" href="#toc-behavior-of-generics"></a>

#### 제네릭은 변수같은 역할을 한다 <a class="toc" id="toc-generics-act-like-variables" href="#toc-generics-act-like-variables"></a>

제네릭 타입은 타입으로 사용된다는 점 외에는 변수 혹은 함수 파라미터와 매우 유사하게 동작합니다. 스코프 안에서 언제든지 사용할 수 있습니다.

```js
function constant<T>(value: T) {
  return function(): T {
    return value;
  };
}
```

#### 필요한 만큼 제네릭을 생성하라 <a class="toc" id="toc-create-as-many-generics-as-you-need" href="#toc-create-as-many-generics-as-you-need"></a>

이름을 무엇으로 하든 필요한 곳 어디든 원하는 만큼 제네릭을 사용할 수 있습니다.

```js
function identity<One, Two, Three>(one: One, two: Two, three: Three) {
  // ...
}
```

#### 제네릭은 값을 추적한다 <a class="toc" id="toc-generics-track-values-around" href="#toc-generics-track-values-around"></a>

어떤 값에 대해 제네릭 타입을 사용하면 Flow는 값을 추적하고 해당 값이 다른 무언가로 바뀌지 않는지 확인합니다.

```js
// @flow
function identity<T>(value: T): T {
  // $ExpectError
  return "foo"; // Error!
}

function identity<T>(value: T): T {
  // $ExpectError
  value = "foo"; // Error!
  // $ExpectError
  return value;  // Error!
}
```

Flow는 제네릭을 통해 전달한 값의 특정 타입을 추적하고 나중에 그것을 사용하도록 합니다.

```js
// @flow
function identity<T>(value: T): T {
  return value;
}

let one: 1 = identity(1);
let two: 2 = identity(2);
// $ExpectError
let three: 3 = identity(42);
```

#### 제네릭에 타입 추가 <a class="toc" id="toc-adding-types-to-generics" href="#toc-adding-types-to-generics"></a>

`mixed`와 비슷하게 제네릭도 "알려지지 않은" 타입을 가집니다. 제네릭을 특정 타입인 것처럼 사용할 수는 없습니다.

```js
// @flow
function logFoo<T>(obj: T): T {
  // $ExpectError
  console.log(obj.foo); // Error!
  return obj;
}
```

이때 타입을 구체화 할 수 있습니다, 그러나 제네릭은 여전히 어떤 타입이든 허용하게 됩니다.

You could refine the type, but the generic will still allow any type to be
passed in.

```js
// @flow
function logFoo<T>(obj: T): T {
  if (obj && obj.foo) {
    console.log(obj.foo); // Works.
  }
  return obj;
}

logFoo({ foo: 'foo', bar: 'bar' });  // Works.
logFoo({ bar: 'bar' }); // Works. :(
```

대신, 함수 파라미터를 쓰는 것처럼 제네릭에 타입을 추가할 수 있습니다.

```js
// @flow
function logFoo<T: { foo: string }>(obj: T): T {
  console.log(obj.foo); // Works!
  return obj;
}

logFoo({ foo: 'foo', bar: 'bar' });  // Works!
// $ExpectError
logFoo({ bar: 'bar' }); // Error!
```

이런식으로, 사용될 특정 타입을 허용하면서도 제네릭의 특징을 유지할 수 있습니다.

```js
// @flow
function identity<T: number>(value: T): T {
  return value;
}

let one: 1 = identity(1);
let two: 2 = identity(2);
// $ExpectError
let three: "three" = identity("three");
```

#### 제네릭 타입은 경계 역할을 한다 <a class="toc" id="toc-generic-types-act-as-bounds" href="#toc-generic-types-act-as-bounds"></a>

```js
// @flow
function identity<T>(val: T): T {
  return val;
}

let foo: 'foo' = 'foo';           // Works!
let bar: 'bar' = identity('bar'); // Works!
```

Flow에서 어떤 타입을 다른 타입으로 전달하는 경우에 대부분 원래 타입을 잃어버리게 됩니다. 그래서 특정 타입을 덜 상세한 타입으로 전달하게 되면 Flow는 더 상세했던 시기를 "잃어버립니다".

```js
// @flow
function identity(val: string): string {
  return val;
}

let foo: 'foo' = 'foo';           // Works!
// $ExpectError
let bar: 'bar' = identity('bar'); // Error!
```

제네릭은 제약을 추가하면 더욱 상세한 타입으로 유지되도록 합니다. 이런 방식으로 제네릭에서 타입은 "경계" 역할을 합니다.


```js
// @flow
function identity<T: string>(val: T): T {
  return val;
}

let foo: 'foo' = 'foo';           // Works!
let bar: 'bar' = identity('bar'); // Works!
```

경계를 가진 제네릭 타입의 값이 있을 때 더 상세한 타입인 것처럼 사용할 수 없다는 것을 알아두세요.

```js
// @flow
function identity<T: string>(val: T): T {
  let str: string = val; // Works!
  // $ExpectError
  let bar: 'bar'  = val; // Error!
  return val;
}

identity('bar');
```

#### 매개변수화 된 제네릭 <a class="toc" id="toc-parameterized-generics" href="#toc-parameterized-generics"></a>

때때로 제네릭은 함수에 인자를 전달하는 것처럼 타입을 전달할 수 있게 해줍니다. 이를 매개변수화 된 제네릭이라고 합니다.

예를 들어, 제네릭을 가진 타입 별칭은 매개변수화됩니다. 타입 별칭을 사용하려고 할 때 타입 인자를 제공해야 합니다.

```js
type Item<T> = {
  prop: T,
}

let item: Item<string> = {
  prop: "value"
};
```

이것은 인자를 함수에 전달하는 것처럼 생각할 수 있으며 반환 값만 타입으로 사용할 수 있습니다.

타입으로 사용되는 클래스, 타입 별칭 그리고 인터페이스 모두 타입 인자를 전달해야 합니다. 함수와 함수 타입은 매개변수화된 제네릭을 가지지 않습니다.

***클래스***

```js
// @flow
class Item<T> {
  prop: T;
  constructor(param: T) {
    this.prop = param;
  }
}

let item1: Item<number> = new Item(42); // Works!
// $ExpectError
let item2: Item = new Item(42); // Error!
```

***타입 별칭***

```js
// @flow
type Item<T> = {
  prop: T,
};

let item1: Item<number> = { prop: 42 }; // Works!
// $ExpectError
let item2: Item = { prop: 42 }; // Error!
```

***인터페이스***

```js
// @flow
interface HasProp<T> {
  prop: T,
}

class Item {
  prop: string;
}

(Item.prototype: HasProp<string>); // Works!
// $ExpectError
(Item.prototype: HasProp); // Error!
```

##### 기본 타입을 매개변수화 된 제네릭에 추가하기 <a class="toc" id="toc-adding-defaults-to-parameterized-generics" href="#toc-adding-defaults-to-parameterized-generics"></a>

함수의 파라미터처럼 기본 타입을 매개변수화 된 제네릭에 추가할 수 있습니다.

```js
type Item<T: number = 1> = {
  prop: T,
};

let foo: Item<> = { prop: 1 };
let bar: Item<2> = { prop: 2 };
```

함수 호출할 때 괄호를 쓰는 것처럼 타입을 사용할 때 `<>`을 포함해야 합니다.
