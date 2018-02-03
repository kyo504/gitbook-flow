---
layout: guide
---

여라 위치에서 재사용이 필요한 복작한 타입을 가지고 있다면 **타입 별칭**을 사용하여 다른 이름을 사용할 수 있습니다.

```js
// @flow
type MyObject = {
  foo: number,
  bar: boolean,
  baz: string,
};
```

이런 타입 별칭은 타입을 사용할 수 있는 어느 곳에서든 사용할 수 있습니다.

```js
// @flow
type MyObject = {
  // ...
};

var val: MyObject = { /* ... */ };
function method(val: MyObject) { /* ... */ }
class Foo { constructor(val: MyObject) { /* ... */ } }
```

## 타입 별칭 문법 <a class="toc" id="toc-type-alias-syntax" href="#toc-type-alias-syntax"></a>

타입 별칭은 `Type` 뒤에 이름, `=` 그리고 타입 정의를 기술하여 생성 할 수 있습니다.

```js
type Alias = Type;
```

어떤 타입이든 타입 별팅 안에 표현할 수 있습니다.

```js
type NumberAlias = number;
type ObjectAlias = {
  property: string,
  method(): number,
};
type UnionAlias = 1 | 2 | 3;
type AliasAlias = ObjectAlias;
```

#### 타입 별칭 제너릭 <a class="toc" id="toc-type-alias-generics" href="#toc-type-alias-generics"></a>

타입 별칭은 자기 자신의 [generics](../generics)를 가질 수 있습니다.

```js
type MyObject<A, B, C> = {
  property: A,
  method(val: B): C,
};
```

타입 별칭 제너릭은 [parameterized](../generics/#toc-parameterized-generics)될 수 있습니다. 타입 별칭을 사용할 때 각 제너릭에 대한 파라미터를 전달해야 합니다.

```js
// @flow
type MyObject<A, B, C> = {
  foo: A,
  bar: B,
  baz: C,
};

var val: MyObject<number, boolean, string> = {
  foo: 1,
  bar: true,
  baz: 'three',
};
```
