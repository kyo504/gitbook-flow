---
layout: guide
---

자바스크립트는 여러가지 타입을 가지고 있습니다.
([MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures)):

- Booleans
- Strings
- Numbers
- `null`
- `undefined` (flow에서는 `void`로 취급됩니다.)
- Symbols (ECMAScript 2015에서 소개되었지만, 현재 지원하지 않습니다.)

기본 타입은 리터럴 값으로 표현되거나

```js
true;
"hello";
3.14;
null;
undefined;
```

혹은 동적으로 생성된 래퍼 객체로 표현됩니다.

```js
new Boolean(false);
new String("world");
new Number(42);
Symbol("foo");
```

리터럴 값에 대한 타입은 소문자로 표기합니다.

```js
// @flow
function method(x: number, y: string, z: boolean) {
  // ...
}

method(3.14, "hello", true);
```

래퍼 객체에 대한 타입은 대문자료 표기합니다.

```js
// @flow
function method(x: Number, y: String, z: Boolean) {
  // ...
}

method(new Number(42), new String("world"), new Boolean(false));
```

이러한 래퍼 객체는 사실 사용하는 경우가 드뭅니다.

## Booleans <a class="toc" id="toc-booleans" href="#toc-booleans"></a>

Boolean은 자바스크립트에서 `true` and `false` 값을 가집니다. `boolean` 타입은 이러한 값을 허용합니다.

```js
// @flow
function acceptsBoolean(value: boolean) {
  // ...
}

acceptsBoolean(true);  // Works!
acceptsBoolean(false); // Works!
acceptsBoolean("foo"); // Error!
```

자바스크립트는 암시적으로 다른 타입의 값을 boolean으로 변환할 수 있습니다.

```js
if (42) {} // 42 => true
if ("") {} // "" => false
```

flow는 이러한 변환을 이해하고 있어서 `if` 문이나 다른 종류의 표현식의 일부로 이러한 변환을 허용합니다.

non-boolean을 명시적으로 변환해서 boolean 타입을 얻을 수 있으며 `Boolean(x)` 혹은 `!!x`를 사용하면 됩니다.

```js
// @flow
function acceptsBoolean(value: boolean) {
  // ...
}

acceptsBoolean(0);          // Error!
acceptsBoolean(Boolean(0)); // Works!
acceptsBoolean(!!0);        // Works!
```

`boolean`과 `Boolean`은 다른 타입이라는 것을 기억하세요.

- `boolean` 은 `true`, `false` 혹은 `a === b` 같은 표현식의 결과같은 리터럴 값입니다.
- `Boolean`은 `new Boolean(x)`을 통해서 생성된 래퍼 객체입니다.

## Numbers <a class="toc" id="toc-numbers" href="#toc-numbers"></a>

다른 언어와 다르게 자바스크립트는 하나의 숫자 타입만 가지고 있습니다. `42` 혹은 `3.14` 같은 값으로 표현됩니다. 또한 자바스크립트도 `Infinity` 와 `NaN`을 숫자로 취급합니다. `number` 타입은 자바스크립트가 숫자라고 취급하는 모든 것을 포함합니다.

```js
// @flow
function acceptsNumber(value: number) {
  // ...
}

acceptsNumber(42);       // Works!
acceptsNumber(3.14);     // Works!
acceptsNumber(NaN);      // Works!
acceptsNumber(Infinity); // Works!
acceptsNumber("foo");    // Error!
```

`number`와 `Number`가 다른 타입이라는 것을 기억하세요.

- `number`는 `42`, `3.14` 혹은 `parseFloat(x)`같은 표현식의 결과같은 리터럴 값입니다.
- `Number`는 `new Number(x)`로 생성된 래퍼 객체입니다.

## Strings <a class="toc" id="toc-strings" href="#toc-strings"></a>

String은 자바스크립트에서 `"foo"` 같은 문자열입니다. flow에서 `string` 타입은 이러한 값을 허용합니다.

```js
// @flow
function acceptsString(value: string) {
  // ...
}

acceptsString("foo"); // Works!
acceptsString(false); // Error!
```

자바스크립트에서는 다른 타입의 값을 합치는 경우 암시적으로 값을 문자열로 변환합니다.

```js
"foo" + 42; // "foo42"
"foo" + {}; // "foo[object Object]"
```

flow는 문자열과 숫자를 합치는 경우만 허용합니다.

```js
// @flow
"foo" + "foo"; // Works!
"foo" + 42;    // Works!
"foo" + {};    // Error!
"foo" + [];    // Error!
```

다른 타입을 문자열로 변환은 명시적으로 하시여 합니다. 즉, 문자열 관련 함수를 이용해서 문자열을 반환 받아서 합쳐햐 합니다.

```js
// @flow
"foo" + String({});     // Works!
"foo" + [].toString();  // Works!
"" + JSON.stringify({}) // Works!
```

`string`과 `String`은 다른 타입이라는 것을 기억하세요.

- `string`은 `"foo"` 혹은 `"" + 42` 같은 표현식의 결과 같은 리터럴 값입니다.
- `String`은 `new String(x)`를 통해서 생성된 래퍼 객체입니다.

## `null`과 `void` <a class="toc" id="toc-null-and-void" href="#toc-null-and-void"></a>

자바스크립트는 `null`과 `undefined`를 가지고 있습니다. flow는 이들을 다른 타입으로 취급합니다: `null`과 `void`(`undefined`와 대응)

```js
// @flow
function acceptsNull(value: null) {
  /* ... */
}

function acceptsUndefined(value: void) {
  /* ... */
}

acceptsNull(null);      // Works!
acceptsNull(undefined); // Error!
acceptsUndefined(null);      // Error!
acceptsUndefined(undefined); // Works!
```

`null`과 `void`는 다른 타입으로 표현됩니다.

### Maybe 타입 <a class="toc" id="toc-maybe-types" href="#toc-maybe-types"></a>

Maybe 타입은 값이 선택적인 곳에서 사용됩니다. 그리고 `?string` 혹은 `?number`처럼 타입의 앞에 물음표를 더함으로써 생성할 수 있습니다.

`?type`은 `type` 이거나 `null` 혹은 `void`가 될 수 있습니다.

```js
// @flow
function acceptsMaybeString(value: ?string) {
  // ...
}

acceptsMaybeString("bar");     // Works!
acceptsMaybeString(undefined); // Works!
acceptsMaybeString(null);      // Works!
acceptsMaybeString();          // Works!
```

### 선택적 객체 프로퍼티 <a class="toc" id="toc-optional-object-properties" href="#toc-optional-object-properties"></a>

객체 타입은 프로퍼티 이름 뒤에 `?`를 붙여서 선택적으로 프로퍼티를 가질 수 있습니다. 

```js
{ propertyName?: string }
```

이들 선택적 파마리터는 `void`이거나 생략될수는 있지만 `null`이 될 수는 없습니다.

```js
// @flow
function acceptsObject(value: { foo?: string }) {
  // ...
}

acceptsObject({ foo: "bar" });     // Works!
acceptsObject({ foo: undefined }); // Works!
acceptsObject({ foo: null });      // Error!
acceptsObject({});                 // Works!
```

### 선택적 함수 파라미터 <a class="toc" id="toc-optional-function-parameters" href="#toc-optional-function-parameters"></a>

함수는 파라미터 이름 뒤에 `?`를 붙은 선택적 파마리터를 가질 수 있습니다.

```js
function method(param?: string) { /* ... */ }
```

이들 선택적 파마리터는 `void`이거나 생략될수는 있지만 `null`이 될 수는 없습니다.

```js
// @flow
function acceptsOptionalString(value?: string) {
  // ...
}

acceptsOptionalString("bar");     // Works!
acceptsOptionalString(undefined); // Works!
acceptsOptionalString(null);      // Error!
acceptsOptionalString();          // Works!
```

### 함수 파라미터의 초기값 <a class="toc" id="toc-function-parameters-with-defaults" href="#toc-function-parameters-with-defaults"></a>


또한 함수 파라미터는 초기값을 가질 수 있습니다. 이건 ECMAScript 2015에 포함된 기능입니다.

```js
function method(value: string = "default") { /* ... */ }
```

초기 파라미터는 `void`이거나 생략될 수는 있지만 `null`이 될 수는 없습니다.

```js
// @flow
function acceptsOptionalString(value: string = "foo") {
  // ...
}

acceptsOptionalString("bar");     // Works!
acceptsOptionalString(undefined); // Works!
acceptsOptionalString(null);      // Error!
acceptsOptionalString();          // Works!
```

## Symbols <a class="toc" id="toc-symbols" href="#toc-symbols"></a>

Symbol은 현재 flow에서 지원되지 않습니다. 관련된 정보는 아래 두 이슈에서 확인하실 수 있습니다:

- [facebook/flow#810](https://github.com/facebook/flow/issues/810)
- [facebook/flow#1015](https://github.com/facebook/flow/issues/1015)
