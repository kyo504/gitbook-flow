---
layout: guide
---

자바스크립트 코드에서 "optional" 값을 사용하는 것이 일반적이므로 값을 버리거나 `null`을 대신 전달하는 옵션이 있습니다.

Flow를 이용하면 이러한 값들에 대해 Maybe 타입을 사용할 수 있습니다. Maybw 타입은 단순히 `?number` 처럼 타입 앞에 물음표(`?`)를 붙이면 됩니다.

Maybe 타입은 설정한 타입 뿐만 아니라 `null` 혹은 `undefined`을 받을 수 있습니다. 그래서 `?number`는 `number`, `null`, 혹은 `undefined`를 의미합니다.

```js
// @flow
function acceptsMaybeNumber(value: ?number) {
  // ...
}

acceptsMaybeNumber(42);        // Works!
acceptsMaybeNumber();          // Works!
acceptsMaybeNumber(undefined); // Works!
acceptsMaybeNumber(null);      // Works!
acceptsMaybeNumber("42");      // Error!
```

## Maybe 타입 리파인먼트 <a class="toc" id="toc-refining-maybe-types" href="#toc-refining-maybe-types"></a>

`?number` 타입을 쓴다고 가정합시다. 만약 `number` 타입의 값을 사용한다고 하면 먼저 `null` 혹은 `undefined`인지 확인할 필요가 있을 것입니다.

```js
// @flow
function acceptsMaybeNumber(value: ?number) {
  if (value !== null && value !== undefined) {
    return value * 2;
  }
}
```

`!= null`을 이용해서 `null` 그리고 `undefined`에 대한 체크를 간단하게 할 수 있습니다.

```js
// @flow
function acceptsMaybeNumber(value: ?number) {
  if (value != null) {
    return value * 2;
  }
}
```

또한, 사용하기 전에 값이 `number` 타입인지를 체크하는 방법도 있습니다.

```js
// @flow
function acceptsMaybeNumber(value: ?number) {
  if (typeof value === 'number') {
    return value * 2;
  }
}
```

하지만, 타입 리파인먼트를 잃어버릴수도 있습니다. 예를 들면, 객체의 프로퍼티 타입을 다듬은 이후에 함수릃 호출하는 것은 이 리파인먼트를 무효화하게 됩니다. 왜 flow가 이런 방식으로 동작하는지 이해하고 이런 일반적인 함정을 피하는 방법에 대한 더 자세한 내용은 [다듬기 무효화](../../lang/refinements/#toc-refinement-invalidation) 문서를 참고하세요. 
