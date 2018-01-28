---
layout: guide
---

새로운 변수를 선언할 때, 변수의 타입을 선택적으로 선언할 수 있습니다.

자바스크립트는 로컬 변수를 선언하는 세 가지 방법이 있습니다.

- `var` - 변수를 선언하는데 선택적으로 값을 할당한다. ([MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var))
- `let` - block-scoped 변수를 선언하는데 선택적으로 값을 할당한다. ([MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let))
- `const` - block-scoped 변수 재할당 할 수 없는 값을 할당한다. ([MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const))

Flow에서는 두개의 그룹으로 나뉜다.

- `let`과 `var` - 재할당 **할 수 있는** 변수들
-  `const` - 재할당 **할 수 없는** 변수들


```js
var varVariable = 1;
let letVariable = 1;
const constVariable = 1;

varVariable = 2;   // Works!
letVariable = 2;   // Works!
// $ExpectError
constVariable = 2; // Error!
```

## `const` <a class="toc" id="toc-const" href="#toc-const"></a>

`const` 변수는 나중에 재할당 할 수 없기 때문에 간단하다.

Flow는 변수에 할당한 값으로 부터 타입을 추론하거나 타입을 직접 제공할 수 있다.

```js
// @flow
const foo /* : number */ = 1;
const bar: number = 2;
```

## `var` and `let` <a class="toc" id="toc-var-and-let" href="#toc-var-and-let"></a>

`var`와 `let`은 재할당 할 수 있기 때문에 알아야 할 규칙이 조금 더 있습니다.

`const`와 유사하게 flow는 변수에 할당한 값을 통해서 타입을 추론하거나 변수에 타입을 제공할 수 있습니다.

```js
// @flow
var fooVar /* : number */ = 1;
let fooLet /* : number */ = 1;
var barVar: number = 2;
let barLet: number = 2;
```

타입을 제공할 때, 값을 재할당 할 수 있을 것입니다. 하지만 타입은 항상 호환가능한 타입이어야 합니다.

```js
// @flow
let foo: number = 1;
foo = 2;   // Works!
// $ExpectError
foo = "3"; // Error!
```

타입을 제공하지 않을 때, 값을 재할당 한다면 추론된 타입은 둘 중에 하나가 될 것입니다.

## 변수 재할당하기 <a class="toc" id="toc-reassigning-variables" href="#toc-reassigning-variables"></a>

기본적으로 변수를 재할당할 때 flow는 모든 할당 가능한 타입을 제시합니다.

```js
let foo = 42;

if (Math.random()) foo = true;
if (Math.random()) foo = "hello";

let isOneOf: number | boolean | string = foo; // Works!
```

때때로 flow는 재할당 이후에 변수의 타입을 확실하게 알 수도 있습니다. 이 경우에 flow는 알려진 타입을 제시합니다.

```js
// @flow
let foo = 42;
let isNumber: number = foo; // Works!

foo = true;
let isBoolean: boolean = foo; // Works!

foo = "hello";
let isString: string = foo; // Works!
```

구문, 함수 그리고 조건에 따라 동작하는 코드는 모두 flow가 타입이 무엇인지 정확하게 알기 어렵게 합니다.

```js
// @flow
let foo = 42;

function mutate() {
  foo = true;
  foo = "hello";
}

mutate();

// $ExpectError
let isString: string = foo; // Error!
```

flow가 더 나아질수록 이러한 시나리오는 줄어들어야 할 것입니다.

