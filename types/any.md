---
layout: guide
---

> **경고:** `any`와 `mixed`를 오해하지 마세요. [더 보기](../mixed)

타입 검사기를 사용하고 싶지 않다는 것은 `any`를 사용한다는 것과 같습니다. **`any`를 사용하는 것은 절대 안전하지 않으며 가능하면 피해야 합니다.**

예를 들어, 다음 코드는 아무런 에러를 알려주지 않습니다.

```js
// @flow
function add(one: any, two: any): number {
  return one + two;
}

add(1, 2);     // Works.
add("1", "2"); // Works.
add({}, []);   // Works.
```

심지어 런타임 에러를 야기하는 코드를 flow가 감지하지 못하게 됩니다.

```js
// @flow
function getNestedProperty(obj: any) {
  return obj.foo.bar.baz;
}

getNestedProperty({});
```

`any`의 사용을 고려할 수 있는 두 가지 정도의 시나리오가 있습니다.

1. 이미 존재하는 코드에 대하여 flow를 이용해서 타입을 지정하도록 하는 변환 과정에 있고 현재 타입을 체크하는 코드 때문에 일의 진행이 되지 않을 때(아마도 다른 코드가 먼저 변환되어야 하는 경우)

2. 코드 동작에 확신이 있고 어떤 이유로 인해 flow가 해당 코드의 타입을 정상적으로 체크하지 못하는 경우. 줄어들고 있자만 자바스크립트에서 flow가 정적으로 타입을 체크하기 어려운 언어적 표현들이 많습니다.

## `any`가 누수되는 상황 피하기 <a class="toc" id="toc-avoid-leaking-any" href="#toc-avoid-leaking-any"></a>

값을 `any` 타입과 함께 쓰는 경우, flow는 수행하는 모든 동작에 대한 결과를 `any`로 추론하는 상황을 초래할 수 있습니다.

예를 들어, `any`로 타입을 지정한 객체의 프로퍼티를 얻는 경우 결과값 또한 `any` 타입을 가지게 됩니다.

```js
// @flow
function fn(obj: any) {
  let foo /* (:any) */ = obj.foo;
}
```

이후에 결과값을 다른 곳에서 사용할 수도 있는데 결과는 `any`가 됩니다.

```js
// @flow
function fn(obj: any) {
  let foo /* (:any) */ = obj.foo;
  let bar /* (:any) */ = foo * 2;
}
```

`any`가 여러분의 코드 전반에 영향을 미칠때까지 이 과정을 계속 될 수 있습니다.

```js
// @flow
function fn(obj: any) /* (:any) */ {
  let foo /* (:any) */ = obj.foo;
  let bar /* (:any) */ = foo * 2;
  return bar;
}

let bar /* (:any) */ = fn({ foo: 2 });
let baz /* (:any) */ = "baz:" + bar;
```

이러한 상황을 방지히가 위해서는 가능한 빨리 다른 타입으로 캐스팅해야 합니다.

```js
// @flow
function fn(obj: any) {
  let foo: number = obj.foo;
}
```

이제는 `any`가 더 이상 누수되지 않습니다.

```js
// @flow
function fn(obj: any) /* (:number) */ {
  let foo: number = obj.foo;
  let bar /* (:number) */ = foo * 2;
  return bar;
}

let bar /* (:number) */ = fn({ foo: 2 });
let baz /* (:string) */ = "baz:" + bar;
```
