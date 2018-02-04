---
layout: guide
---

Flow에서 자바스크립트 [클래스](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)는 값과 타입 둘 모두로 동작합니다.

Flow 없이 쓰는 것과 동일하게 클래스를 작성하면 됩니다. 하지만 클래스 이름은 타입으로도 사용할 수 있습니다.

```js
class MyClass {
  // ...
}

let myInstance: MyClass = new MyClass();
```

이것은 Flow에서 클래스틑 [nomially typed](../../lang/nominal-structural)이기 때문입니다.

## 클래스 문법 <a class="toc" id="toc-class-syntax" href="#toc-class-syntax"></a>

Flow에서 클래스는 타입을 추가된 것 외에는 일반 자바스크립트 클래스와 동일합니다.

##### 클래스 메서드 <a class="toc" id="toc-class-methods" href="#toc-class-methods"></a>

함수와 마찬가지로 클래서 메서드는 파라미터와 반환값에 대한 어노테이션을 가질 수 있습니다.

```js
class MyClass {
  method(value: string): number { /* ... */ }
}
```

##### 클래스 프로퍼티 <a class="toc" id="toc-class-fields-properties" href="#toc-class-fields-properties"></a>

Flow에서 클래스 필드를 사용할때마다 어노테이션을 먼저 기술해야 합니다.

```js
// @flow
class MyClass {
  method() {
    // $ExpectError
    this.prop = 42; // Error!
  }
}
```

필드는 클래스 안쪽에서 어노테이션을 가져야 하고 필드 이름 뒤에 `:` 그리고 타입을 적습니다.

```js
// @flow
class MyClass {
  prop: number;
  method() {
    this.prop = 42;
  }
}
```

Flow는 [클래스 속성 문법](https://tc39.github.io/proposal-class-public-fields/)을 지원합니다.

```js
class MyClass {
  prop = 42;
}
```

이 문법을 사용할 때 타입 어노테이션 꼭 주어야 하는건 아니지만 필요하다면 줄 수 있습니다.

```js
class MyClass {
  prop: number = 42;
}
```

##### 클래스 제네릭 <a class="toc" id="toc-class-generics" href="#toc-class-generics"></a>

또한 클래스는 자기 자신의 [제네릭](../generics)을 가질 수 있습니다.

```js
class MyClass<A, B, C> {
  property: A;
  method(val: B): C {
    // ...
  }
}
```

클래스 제네릭은 [매개변수화](../generics/#toc-parameterized-generics)됩니다. 클래스를 타입으로 사용할 때 각 제네릭에 대해 파라미터를 제공해야 합니다.

```js
// @flow
class MyClass<A, B, C> {
  constructor(arg1: A, arg2: B, arg3: C) {
    // ...
  }
}

var val: MyClass<number, boolean, string> = new MyClass(1, true, 'three');
```
