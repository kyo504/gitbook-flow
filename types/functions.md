---
layout: guide
---

함수는 타입이 적용되는 곳이 둘 있습니다: 파라미터(입력)과 반환값(결과)

```js
// @flow
function concat(a: string, b: string): string {
  return a + b;
}

concat("foo", "bar"); // Works!
// $ExpectError
concat(true, false);  // Error!
```

추론을 통해서 타입은 선택적이 될 수 있습니다:


```js
// @flow
function concat(a, b) {
  return a + b;
}

concat("foo", "bar"); // Works!
// $ExpectError
concat(true, false);  // Error!
```

때로는 flow의 추론이 의도한 것보다 더 관대한 타입을 생성합니다.

```js
// @flow
function concat(a, b) {
  return a + b;
}

concat("foo", "bar"); // Works!
concat(1, 2);         // Works!
```

이런 이유료, 중요한 함수에 대해서 타입을 쓰는게 유용합니다.

## 함수의 문법 <a class="toc" id="toc-syntax-of-functions" href="#toc-syntax-of-functions"></a>

함수를 만드는 방법은 3가지가 있습니다.


### 함수 선언 <a class="toc" id="toc-function-declarations" href="#toc-function-declarations"></a>

타입을 추가하거나 하지 않은 함수 선언 문법이 있습니다.

```js
function method(str, bool, ...nums) {
  // ...
}

function method(str: string, bool?: boolean, ...nums: Array<number>): void {
  // ...
}
```

### Arrow 함수 <a class="toc" id="toc-arrow-functions" href="#toc-arrow-functions"></a>

타입을 추가하거나 하지 않은 함수 선언 문법이 있습니다.

```js
let method = (str, bool, ...nums) => {
  // ...
};

let method = (str: string, bool?: boolean, ...nums: Array<number>): void => {
  // ...
};
```

### 함수 타입 <a class="toc" id="toc-function-types" href="#toc-function-types"></a>

함수의 타입을 작성하는 문법은 아래와 같습니다.

```js
(str: string, bool?: boolean, ...nums: Array<number>) => void
```

파마리터 명를 선택적으로 뺄 수도 있습니다.

```js
(string, boolean | void, Array<number>) => void
```

콜백같은 것에 대한 함수 타입을 사용할 수 있습니다.

```js
function method(callback: (error: Error | null, value: string | null) => void) {
  // ...
}
```

## 함수 파라미터 <a class="toc" id="toc-function-parameters" href="#toc-function-parameters"></a>

함수 파마리터는 파마리터 명 뒤에 `:`를 붙여서 타입을 명시할 수 있습니다.

```js
function method(param1: string, param2: boolean) {
  // ...
}
```

## 선택적 파라미터 <a class="toc" id="toc-optional-parameters" href="#toc-optional-parameters"></a>

선태적 파마리터의 경우 파라미터 명 뒤에 그리고 `:`앞에 `?`를 붙여줍니다.

```js
function method(optionalValue?: string) {
  // ...
}
```

선택적 파마리터는 파라미터가 없거나, `undefined` 혹은 매칭된 타입을 받을 수 있지만 `null`을 받지는 않습니다.

```js
// @flow
function method(optionalValue?: string) {
  // ...
}

method();          // Works.
method(undefined); // Works.
method("string");  // Works.
// $ExpectError
method(null);      // Error!
```

### Rest 파라미터 <a class="toc" id="toc-rest-parameters" href="#toc-rest-parameters"></a>

자바스립트는 rest 파라미터 혹은 리스트의 끝에서 인자의 배열을 모은 파라미터를 지원합니다. 이들 앞에는 말줄임표(`...`)을 사용합니다.

같은 문법을 이용해서 rest 파라미터의 타입 어노테이션을 추가할 수 있습니다.

```js
function method(...args: Array<number>) {
  // ...
}
```

원하는 만큼의 인자를 rest 파라미터로 보낼 수 있씁니다.

```js
// @flow
function method(...args: Array<number>) {
  // ...
}

method();        // Works.
method(1);       // Works.
method(1, 2);    // Works.
method(1, 2, 3); // Works.
```

> Note: 만약 rest 파라미터에 타입 어노테이션을 추가한다면
> 항상 명시적으로 `Array` 타입이어야 합니다.

### 함수 반환 <a class="toc" id="toc-function-returns" href="#toc-function-returns"></a>

함수 반환도 인자 목록 뒤에 `:`을 추가할 수 있습니다.

```js
function method(): number {
  // ...
}
```

반환 타입이 함수의 모든 조건이 같은 타입을 반환해야 합니다. 이는 우연히 특정 조건에서 값을 반환하지 않는 것을 방지할 수 있습니다.

```js
// @flow
// $ExpectError
function method(): boolean {
  if (Math.random() > 0.5) {
    return true;
  }
}
```

### 함수 `this` <a class="toc" id="toc-function-this" href="#toc-function-this"></a>

자바스크립트에서 모든 함수는 `this`라는 특별한 컨택스트와 함께 호출됩니다. 원하는 어떤 컨택스트릉 이용해서 함수를 호출할 수 있습니다.

flow는 `this`의 타입을 명시하지 않고 함수와 함께 허출되는 어떤 컨텍스트든 체크할 것입니다.


```js
function method() {
  return this;
}

var num: number = method.call(42);
// $ExpectError
var str: string = method.call(42);
```

### Predicate 함수 <a class="toc" id="toc-function-checks" href="#toc-predicate-functions"></a>

땨로 조건을 `if`문에서 별도의 함수로 이동하고 싶을 수 있습니다.

```js
function concat(a: ?string, b: ?string): string {
  if (a && b) {
    return a + b;
  }
  return '';
}
```

그러나 flow는 아래 코드에서 에러를 표시합니다:

```js
function truthy(a, b): boolean {
  return a && b;
}

function concat(a: ?string, b: ?string): string {
  if (truthy(a, b)) {
    // $ExpectError
    return a + b;
  }
  return '';
}
```

이 에러는 아래와 같이 `%checks` 어노테이션을 사용해서 truthy`를 *predicate 함수*로 만들어서 고칠 수 있습니다.


```js
function truthy(a, b): boolean %checks {
  return !!a && !!b;
}

function concat(a: ?string, b: ?string): string {
  if (truthy(a, b)) {
    return a + b;
  }
  return '';
}
```

predicate 함수의 내부는 표현식이어야 한다(예를 들면, 로컬 변수는 허용하지 않는다). 그러나 predicate 함수 내부에서 다른 predicate 함수를 호출 할 수 있다. 예를 들면:

```js
function isString(y): %checks {
  return typeof y === "string";
}

function isNumber(y): %checks {
  return typeof y === "number";
}

function isNumberOrString(y): %checks {
  return isString(y) || isNumber(y);
}

function foo(x): string | number {
  if (isNumberOrString(x)) {
    return x + x;
  } else {
  	return x.length; // no error, because Flow infers that x can only be an array
  }
}

foo('a');
foo(5);
foo([]);
```


### `Function` 타입 <a class="toc" id="toc-function-type" href="#toc-function-type"></a>

때로는 임의의 함수를 받는 타입을 작성하는 것이 유용합니다. 이런 경우에는 아래와 같이 `() => mixed`를 사용합면 됩니다.

```js
function method(func: () => mixed) {
  // ...
}
```

그러나 타입 검사를 사용하길 원치 않고 `any`로 간다면 `Function`을 사용하면 됩니다. **`Function`은 안전하지 않고 피해야 합니다.**

예를 들면, 다음 코드는 어떠한 에러도 알려주지 않습니다.

```js
function method(func: Function) {
  func(1, 2);     // Works.
  func("1", "2"); // Works.
  func({}, []);   // Works.
}

method(function(a: number, b: number) {
  // ...
});
```

> ***`Function`을 사용할 때 `any`를 사용할 때와 [모두 같은 규칙](../any)을 사용해야 합니다.***
