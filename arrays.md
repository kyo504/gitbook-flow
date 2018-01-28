---
layout: guide
---

> **Note:** 배열 또한 떄때로 자바스크립트에서 tuple처럼 쓰입니다. 이 둘의 타입은 flow에서 다르게 기술됩니다. 더 많은 정보는 Tuple 문서를 참고하세요.

배열은 자바스크립트에서 특별한 리스트 같은 객체 타입입니다. 배열을 생성하는 방법은 몇 가지가 있습니다.

```js
new Array(1, 2, 3); // [1, 2, 3];
new Array(3);       // [undefined, undefined, undefined]
[1, 2, 3];          // [1, 2, 3];
```

또한 배열을 생성한 이후에 값을 추가할 수 있습니다.

```js
let arr = []; // []
arr[0] = 1;   // [1]
arr[1] = 2;   // [1, 2]
arr[2] = 3;   // [1, 2, 3]
```

## `Array` 타입 <a class="toc" id="toc-array-type" href="#toc-array-type"></a>

배열 타입을 생성하려면 `Array<Type>` 타입을 사용하면 됩니다. 여기서 `Type`은 배열 안에 있는 원소의 타입입니다. 예를 들어, number의 배열에 대한 타입을 만들려면 `Array<number>`를 사용하면 됩니다.

```js
let arr: Array<number> = [1, 2, 3];
```

`Array<Type>`안에는 어떠한 타입도 넣을 수 있습니다.

```js
let arr1: Array<boolean> = [true, false, true];
let arr2: Array<string> = ["A", "B", "C"];
let arr3: Array<mixed> = [1, true, "three"]
```

## `Array` 타입 약칭 문법 <a class="toc" id="toc-array-type-shorthand-syntax" href="#toc-array-type-shorthand-syntax"></a>

위에서 언급한 문법을 간단하게 표기할 수 있습니다: `Type[]`.

```js
let arr: number[] = [0, 1, 2, 3];
```

`?Type[]`는 `?Array<T>`와 동일하지만 `Array<?T>`는 아닙니다.

```js
// @flow
let arr1: ?number[] = null;   // Works!
let arr2: ?number[] = [1, 2]; // Works!
let arr3: ?number[] = [null]; // Error!
```

`Array<?T>`를 만들려면 괄호를 사용하세요: `(?Ttype)[]`

```js
// @flow
let arr1: (?number)[] = null;   // Error!
let arr2: (?number)[] = [1, 2]; // Works!
let arr3: (?number)[] = [null]; // Works!
```

## 배열 접근은 안전하지 않습니다. <a class="toc" id="toc-array-access-is-unsafe" href="#toc-array-access-is-unsafe"></a>

배열에서 원소를 취할 때 `undefined`의 가능성을 항상 인지해야 합니다. 배열의 범위를 넘어서는 인덱스를 접근하거나 배열이 "희소 배열(sparse array)" 이기 떄문에 존재하지 않는 원소를 접근할 수도 있습니다.

**Note:** 희소 배열은 배열에 속한 원소의 위치가 연속적이지 않은 배열을 말한다.

예를 들면, 배열의 범위를 넘어서는 원소를 접근할 수 있습니다.

```js
// @flow
let array: Array<number> = [0, 1, 2];
let value: number = array[3]; // Works.
                       // ^ undefined
```

혹은 배열이 "희소 배열(sparse array)" 라면 존재하지 않는 원소에 접근할 수도 있습니다.

```js
// @flow
let array: Array<number> = [];

array[0] = 0;
array[2] = 2;

let value: number = array[1]; // Works.
                       // ^ undefined
```

안전하게 접근하기 위해서 flow는 모든 배열 접근에 대해서 "*possibly undefined*"라고 표시해야 합니다.

flow는 사용하기에 너무 불편하기때문에 이를 지원하지 않습니다. 배열에 접근할 때 얻는 모든 값의 타입을 다듬어야 합니다.

```js
let array: Array<number> = [0, 1, 2];
let value: number | void = array[1];

if (value !== undefined) {
  // number
}
```

 flow가 진화함에 따라 나중에 이러한 문제는 해결될 수도 있습니다. 하지만 현재는 이를 개발자가 인지하고 있어야 합니다.
