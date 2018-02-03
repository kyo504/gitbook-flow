---
layout: guide
---

튜플은 리스트의 한 종류이지만 제한된 아이템 셋을 가집니다. 자바스크립트에서 튜플은 배열을 이용해서 생상할 수 있습니다.

Flow에서는 `[타입, 타입, 타입]` 문법을 이용해 튜플을 만들 수 있습니다

```js
let tuple1: [number] = [1];
let tuple2: [number, boolean] = [1, true];
let tuple3: [number, boolean, string] = [1, true, "three"];
```

특정 인덱스에 있는 튜플의 값을 얻으면 해당 인덱스에서 타입을 반환합니다.

```js
// @flow
let tuple: [number, boolean, string] = [1, true, "three"];

let num  : number  = tuple[0]; // Works!
let bool : boolean = tuple[1]; // Works!
let str  : string  = tuple[2]; // Works!
```

만약 존재하지 않은 인덱스로부터 얻으려 한다면 `void`를 반환합니다.

```js
// @flow
let tuple: [number, boolean, string] = [1, true, "three"];

let none: void = tuple[3];
```

만약 Flow가 접근하려는 인덱스를 모른다면 모든 가능한 타입을 반환합니다.

```js
// @flow
let tuple: [number, boolean, string] = [1, true, "three"];

function getItem(n: number) {
  let val: number | boolean | string = tuple[n];
  // ...
}
```

튜플안에 새로운 값을 설정할 때 새로운 값은 해당 인덱스에 있는 타입과 일치해야 합니다.

```js
// @flow
let tuple: [number, boolean, string] = [1, true, "three"];

tuple[0] = 2;     // Works!
tuple[1] = false; // Works!
tuple[2] = "foo"; // Works!

// $ExpectError
tuple[0] = "bar"; // Error!
// $ExpectError
tuple[1] = 42;    // Error!
// $ExpectError
tuple[2] = false; // Error!
```

## 엄격하게 적용되는 튜플 길이 (arity) <a class="toc" id="toc-strictly-enforced-tuple-length-arity" href="#toc-strictly-enforced-tuple-length-arity"></a>

튜플의 길이는 "arity"로 알려져 있습니다. 튜플의 길이는 Flow에서 엄격하게 적용됩니다.

##### 튜플은 같은 길이를 가진 튜플만 허용합니다. <a class="toc" id="toc-tuples-only-match-tuples-with-same-length" href="#toc-tuples-only-match-tuples-with-same-length"></a>

이 말은 짧은 튜플은 긴 튜플을 대체할 수 없다는 의미입니다.

```js
// @flow
let tuple1: [number, boolean]       = [1, true];
// $ExpectError
let tuple2: [number, boolean, void] = tuple1; // Error!
```

또한, 긴 튜플은 짧은 튜플을 대체할 수 없습니다.

```js
// @flow
let tuple1: [number, boolean, void] = [1, true];
// $ExpectError
let tuple2: [number, boolean]       = tuple1; // Error!
```

##### 튜플은 배열 타입과 일치하지 않습니다. <a class="toc" id="toc-tuples-don-t-match-array-types" href="#toc-tuples-don-t-match-array-types"></a>

Flow가 배열을 길이를 모르기 때문에 `Array<T>` 타입은 튜플로 전달될 수 없습니다.

```js
// @flow
let array: Array<number>    = [1, 2];
// $ExpectError
let tuple: [number, number] = array; // Error!
```

또한 튜플 타입은 `Array<T>` 타입으로 전달될 수 없습니다. 그럴 경우 안전하지 않은 방법으로 튜플을 변형할 수 있기 때문입니다.

```js
// @flow
let tuple: [number, number] = [1, 2];
// $ExpectError
let array: Array<number>    = tuple; // Error!
```

##### 튜플에서 배열 함수를 Cannot use mutating array methods on tuples <a class="toc" id="toc-cannot-use-mutating-array-methods-on-tuples" href="#toc-cannot-use-mutating-array-methods-on-tuples"></a>

튜플을 조작하는 `Array.prototype` 매서드를 사용할 수 없습니다.

```js
// @flow
let tuple: [number, number] = [1, 2];
tuple.join(', '); // Works!
// $ExpectError
tuple.push(3);    // Error!
```
