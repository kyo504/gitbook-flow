---
layout: guide
---

Opaque 타입 별팅은 정의된 파일 밖으로 타입에 대한 접근을 허용하지 않는 타입 별칭입니다.

Opaque type aliases are type aliases that do not allow access to their
underlying type outside of the file in which they are defined.

```js
opaque type ID = string;
```

보통 타입 별칭과 같이 Opaque 타입 별칭은 타입이 사용될 수 있는 어느 곳에서든 사용될 수 있습니다.


```js
// @flow
opaque type ID = string;

function identity(x: ID): ID {
  return x;
}
export type {ID};
```

## Opaque 타입 별칭 문법 <a class="toc" id="toc-opaque-type-alias-syntax" href="#toc-opaque-type-alias-syntax"></a>

Opaque 타입 별칭은 이름 뒤에 `opaque type`, `=` 그리고 타입 정의를 기술하면 됩니다.

```js
opaque type Alias = Type;
```

또안 선택적으로 이름 뒤에 `:`와 타입을 추가해서 opaque 타입 별칭에 subtyping constaint를 추가할 수 있습니다.

You can optionally add a subtyping constraint to an opaque type alias by adding a colon `:` and a type after the name.

```js
opaque type Alias: SuperType = Type;
```

어떤 타입이든 슈퍼 타입 혹은 opaque 타입 별팅으로 나타낼 수 있습니다.

```js
opaque type StringAlias = string;
opaque type ObjectAlias = {
  property: string,
  method(): number,
};
opaque type UnionAlias = 1 | 2 | 3;
opaque type AliasAlias: ObjectAlias = ObjectAlias;
opaque type VeryOpaque: AliasAlias = ObjectAlias;
```

## Opaque 타입 별칭의 타입 검사 <a class="toc" id="toc-opaque-type-alias-type-checking" href="#toc-opaque-type-alias-type-checking"></a>

#### 선언한 파일 내부에서 <a class="toc" id="toc-within-the-defining-file" href="#toc-within-the-defining-file"></a>

같은 파일에서 별칭이 정의되면 opaque 타입 별칭은 일반적인 [타입 별칭](../aliases)이 동작하는 것과 동일하게 동작합니다.

```js
//@flow
opaque type NumberAlias = number;

(0: NumberAlias);

function add(x: NumberAlias, y: NumberAlias): NumberAlias {
    return x + y;
}
function toNumberAlias(x: number): NumberAlias { return x; }
function toNumber(x: NumberAlias): number { return x; }
```

#### 정의된 파일 외부에서 <a class="toc" id="toc-outside-the-defining-file" href="#toc-outside-the-defining-file"></a>

opaque 타입 별칭을 import 하면 기존 타입을 숨기고 [norminal type](../../lang/nominal-structural/#toc-nominal-typing) 같이 동작합니다.

**`exports.js`**

```js
export opaque type NumberAlias = number;
```

**`imports.js`**

```js
import type {NumberAlias} from './exports';

(0: NumberAlias) // Error: 0 is not a NumberAlias!

function convert(x: NumberAlias): number {
  return x; // Error: x is not a number!
}
```

#### Subtyping Constraints <a class="toc" id="toc-subtyping-constraints" href="#toc-subtyping-constraints"></a>

opque 타입 별칭에 subtying constraint를 추가하면 opaque 타입이 정의된 파일 외부에서 슈퍼 타입으로 사용될 수 있게 합니다.

**`exports.js`**

```js
export opaque type ID: string = string;
```

**`imports.js`**

```js
import type {ID} from './exports';

function formatID(x: ID): string {
    return "ID: " + x; // Ok! IDs are strings.
}

function toID(x: string): ID {
    return x; // Error: strings are not IDs.
}
```

subtying constraint로 opaque 타입 별칭을 만들 때 타입 위체에 있는 타입은 슈퍼 타입 위치 안에 있는 타입의 슈퍼 타입이어야 한다.

When you create an opaque type alias with a subtyping constraint, the type in the type position must be a subtype of the type in the super type position.

```js
//@flow
opaque type Bad: string = number; // Error: number is not a subtype of string
opaque type Good: {x: string} = {x: string, y: number};
```

#### 제너릭 <a class="toc" id="toc-generics" href="#toc-generics"></a>

또한 Opacue 타입 별칭은 자기 자신의 [제너릭](../generics)을 가질 수 있습니다. 그리고 일반 [타입 별칭](../aliases/#toc-type-alias-generics)에서의 제너릭과 동일하게 동작합니다.

```js
// @flow
opaque type MyObject<A, B, C>: { foo: A, bar: B } = {
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

#### 라이브러리 정의 <a class="toc" id="toc-library-definitions" href="#toc-library-definitions"></a>

[libdefs](https://flow.org/en/docs/libdefs/)에 있는 opaque 타입 별칭을 선언할 수도 있습니다. 그러면 기본 타입은 생략할 수 있지만 여전히 선택적으로 슈퍼 타입을 포함할 수 있습니다.

```js
declare opaque type Foo;
declare opaque type PositiveNumber: number;
```
