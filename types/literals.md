---
layout: guide
---

Flow는 리터럴 값에 대한 [기본 타입](../primitives)을 가지고 있고 리터럴 값을 타입으로 사용할 수도 있습니다.

예를 들어, `number` 타입을 받는 대신 리터럴 값인 `2`만 받을 수도 있습니다.

```js
// @flow
function acceptsTwo(value: 2) {
  // ...
}

acceptsTwo(2);   // Works!
// $ExpectError
acceptsTwo(3);   // Error!
// $ExpectError
acceptsTwo("2"); // Error!
```

릴터럴 타입은 기본 값(primitive values)을 사용할 수 있습니다:

- boolean: `true` 혹은 `false`
- number: `42` 혹은 `3.14` 같은 값
- strings: `"foo"` 혹은 `"bar"` 같은 값

리터럴 타입을 [유니온 타입](../unions/)과 함께 사용하면 강력합니다:

```js
// @flow
function getColor(name: "success" | "warning" | "danger") {
  switch (name) {
    case "success" : return "green";
    case "warning" : return "yellow";
    case "danger"  : return "red";
  }
}

getColor("success"); // Works!
getColor("danger");  // Works!
// $ExpectError
getColor("error");   // Error!
```
