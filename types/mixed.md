---
layout: guide
---

일반적으로, 프로그램은 여러가지 다른 종류의 타입을 가지고 있습니다:

**싱글 타입:**

아래 코드에서 입력 값은 `number`만 올 수 있습니다.

```js
function square(n: number) {
  return n * n;
}
```

**다른 허용 가능한 타입들의 그룹:**

아래 코드에서 입력 값은 `string`이나 `number`가 될 수 있습니다.

```js
function stringifyBasicValue(value: string | number) {
  return '' + value;
}
```

**다른 타입을 기반으로 한 타입:**

아래 코드에서 반환 타입은 함수의 입력 값의 타입에 따라 결정 됩니다.

```js
function identity<T>(value: T): T {
  return value;
}
```

이러한 세 가지가 가장 일반적인 타입의 종류입니다. 이들은 여러분이 작성하는 타입의 대다수를 차지하게 될 것입니다.

하지만 다른 한 종류가 더 있습니다.

**어떤 것이든 될 수 있는 임의의 타입:**

아래 코드에서 전달된 값에 대한 타입은 알 수 없습니다. 어떠한 타입이 오더라도 함수는 정상적으로 동작하게 됩니다.

Here the passed in value is an unknown type, it could be any type and the
function would still work.

```js
function getTypeOf(value: mixed): string {
  return typeof value;
}
```

이러한 알 수 없는 타입은 흔하지 않지만 때로는 유용합니다.

이러한 값을 `mixed`라고 표현합니다.

## 아무거나 들어올 수 있지만 아무것도 나갈 수 없다 <a class="toc" id="toc-anything-goes-in-nothing-comes-out" href="#toc-anything-goes-in-nothing-comes-out"></a>

`mixed`는 어떠한 타입도 받을 수 있습니다. 문자열, 숫자, 객체, 함수 어떤 것이든 상관 없습니다.

```js
// @flow
function stringify(value: mixed) {
  // ...
}

stringify("foo");
stringify(3.14);
stringify(null);
stringify({});
```

`mixed` 타입의 값을 사용할 때 반드시 실제 타입이 무엇인지 알아야 하며 그렇지 않으면 에러를 발생하게 됩니다.

```js
// @flow
function stringify(value: mixed) {
  // $ExpectError
  return "" + value; // Error!
}

stringify("foo");
```

대신 해당 값이 특정 타입이라는 것을 정제하는 과정이 있어야 합니다.

```js
// @flow
function stringify(value: mixed) {
  if (typeof value === 'string') {
    return "" + value; // Works!
  } else {
    return "";
  }
}

stringify("foo");
```

`typeof value === 'string'`이라는 체크 때문에 flow는 `value`가 `if`문 안에서 `string`이라는 것을 알 수 있습니다. 이런걸 [refinement](../../lang/refinements)라고 합니다.
