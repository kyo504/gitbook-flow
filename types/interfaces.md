---
layout: guide
---

Flow에서 클래스는 norminally types 됩니다. 이 말은 두개의 다른 클래스가 정확히 같은 함수와 프로퍼티를 가지더라도 하나의 타입을 다른 걸 대체하여 사용할 수 없다는 것입니다.

```js
// @flow
class Foo {
  serialize() { return '[Foo]'; }
}

class Bar {
  serialize() { return '[Bar]'; }
}

// $ExpectError
const foo: Foo = new Bar(); // Error!
```

대신, 기대하는 클래스 구조를 선언하기 위해서는 `interface`를 사용할 수 있습니다.

```js
// @flow
interface Serializable {
  serialize(): string;
}

class Foo {
  serialize() { return '[Foo]'; }
}

class Bar {
  serialize() { return '[Bar]'; }
}

const foo: Serializable = new Foo(); // Works!
const bar: Serializable = new Bar(); // Works!
```

또한 `interface`를 이용해서 Flow가 인터페이스와 매치되는 클래스를 원한다고 할 수 있다. 이는 클래스를 수정할 때 호환되지 않는 변화를 만드는 것을 방지한다.


```js
// @flow
interface Serializable {
  serialize(): string;
}

class Foo implements Serializable {
  serialize() { return '[Foo]'; } // Works!
}

class Bar implements Serializable {
  // $ExpectError
  serialize() { return 42; } // Error!
}
```

또한 `interface`를 이용해서 여러 개의 인터페이스를 사용할 수 있다.

```js
class Foo implements Bar, Baz {
  // ...
}
```

## 인터페이스 문법 <a class="toc" id="toc-interface-syntax" href="#toc-interface-syntax"></a>

인터페이스는 `interface` 키워드를 사용해서 만드는데 `interface` 다음에 이름을 쓰고 타입 정의 부분을 기술하면 됩니다.

```js
interface MyInterface {
  // ...
}
```

블록의 문법은 객체 타입의 문법 동일하며 모든 같은 기능들을 가지고 있습니다.

##### 인터페이스 메서드 <a class="toc" id="toc-interface-methods" href="#toc-interface-methods"></a>

객체 메서드와 동일하게 인터페이스에도 매서드를 추가할 수 있습니다.

```js
interface MyInterface {
  method(value: string): number;
}
```

##### 인터페이스 프로퍼티 <a class="toc" id="toc-interface-properties" href="#toc-interface-properties"></a>

객체 프로퍼티와 동일하게 인터페이스에도 프로퍼티를 추가할 수 있습니다.

```js
interface MyInterface {
  property: string;
}
```

인터페이스 프로퍼티는 선택사항입니다.

```js
interface MyInterface {
  property?: string;
}
```

##### 맵 형태의 인터페이스 <a class="toc" id="toc-interfaces-as-maps" href="#toc-interfaces-as-maps"></a>

객체와 동일한 방법으로 ["인덱서 프로퍼티"](../objects/#toc-objects-as-maps)를 생성할 수 있습니다.

```js
interface MyInterface {
  [key: string]: number;
}
```

#### 인터페이스 제너릭 <a class="toc" id="toc-interface-generics" href="#toc-interface-generics"></a>

인터페이스도 자기 자신의 [제너릭](../generics)를 가질 수 있습니다.

```js
interface MyInterface<A, B, C> {
  property: A;
  method(val: B): C;
}
```

인터페이스 제너릭은 [parameterized](../generics/#toc-parameterized-generics) 됩니다. 인터페이스를 사용할 때 각각의 제너릭에 대한 파라미터를 전달해야 합니다.

```js
// @flow
interface MyInterface<A, B, C> {
  foo: A;
  bar: B;
  baz: C;
}

var val: MyInterface<number, boolean, string> = {
  foo: 1,
  bar: true,
  baz: 'three',
};
```

<!-- [TODO: Overloading interface methods -->

## 인터페이스 내부의 읽기 전용 혹은 쓰기 전용 프로퍼티 <a class="toc" id="toc-interface-property-variance-read-only-and-write-only" href="#toc-interface-property-variance-read-only-and-write-only"></a>

인터페이스 프로퍼티는 기본적으로 [invariant](../../lang/variance/) 입니다. 그러나 수정자를 추가해서 읽기 전용(covariant)이나 쓰기 전용(contravariant)으로 만들 수 있습니다.

```js
interface MyInterface {
  +covariant: number;     // read-only
  -contravariant: number; // write-only
}
```

#### 읽기 전용 (read-only) 프로퍼티 <a class="toc" id="toc-covariant-read-only-properties-on-interfaces" href="#toc-covariant-read-only-properties-on-interfaces"></a>

프로퍼티를 읽기 전용으로 만들기 위해서는 프로퍼티 이름 앞에 `+` 심볼을 더하면 됩니다.

```js
interface MyInterface {
  +readOnly: number | string;
}
```

이는 해당 프로퍼티 대신 더 상세한 타입을 전달하도록 합니다.

```js
// @flow
// $ExpectError
interface Invariant {  property: number | string }
interface Covariant { +readOnly: number | string }

var value1: Invariant = { property: 42 }; // Error!
var value2: Covariant = { readOnly: 42 }; // Works!
```

또한 읽기 전용이 동작하는 방법 때문에 일기 전용 프로퍼티는 사용될 때 읽기 전용이 됩니다. 이는 일반 프로퍼티에 비해 유용합니다.

```js
// @flow
interface Invariant {  property: number | string }
interface Covariant { +readOnly: number | string }

function method1(value: Invariant) {
  value.property;        // Works!
  value.property = 3.14; // Works!
}

function method2(value: Covariant) {
  value.readOnly;        // Works!
  // $ExpectError
  value.readOnly = 3.14; // Error!
}
```

#### 쓰기 전용 (write-only) 프로퍼티 <a class="toc" id="toc-contravariant-write-only-properties-on-interfaces" href="#toc-contravariant-write-only-properties-on-interfaces"></a>

프로퍼티를 쓰기 전용으로 만들기 위해서는 프로퍼티 이름 앞에 `-` 심볼을 더하면 됩니다.

```js
interface InterfaceName {
  -writeOnly: number;
}
```

이는 해당 프로퍼티 대신 더 상세한 타입을 전달하도록 합니다.

```js
// @flow
interface Invariant     {  property: number }
interface Contravariant { -writeOnly: number }

var numberOrString = Math.random() > 0.5 ? 42 : 'forty-two';

// $ExpectError
var value1: Invariant     = { property: numberOrString };  // Error!
var value2: Contravariant = { writeOnly: numberOrString }; // Works!
```

또한 쓰기 전용이 동작하는 방법 때문에 일기 전용 프로퍼티는 사용될 때 쓰기 전용이 됩니다. 이는 일반 프로퍼티에 비해 유용합니다.

```js
interface Invariant     {   property: number }
interface Contravariant { -writeOnly: number }

function method1(value: Invariant) {
  value.property;        // Works!
  value.property = 3.14; // Works!
}

function method2(value: Contravariant) {
  // $ExpectError
  value.writeOnly;        // Error!
  value.writeOnly = 3.14; // Works!
}
```
