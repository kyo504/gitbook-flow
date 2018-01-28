---
layout: guide
---

객체는 자바스크립트에서 다양한 방법으로 사용될 수 있습니다. 모든 경우를 지원하기 위해 객체에 타입을 지정하는 다양한 방법이 존재합니다.

## 객체 타입 문법 <a class="toc" id="toc-object-type-syntax" href="#toc-object-type-syntax"></a>

객체 타입은 최대한 자바스크립트에서의 객체 문법과 일치하려 합니다. `{}`을 사용하고 `:`을 이용해서 이름-값 쌍을 사용하고, 그리고 콤마(`,`)로 이를 구분합니다.

```js
// @flow
var obj1: { foo: boolean } = { foo: true };
var obj2: {
  foo: number,
  bar: boolean,
  baz: string,
} = {
  foo: 1,
  bar: true,
  baz: 'three',
};
```

> **Note:** 이전에 객체 타입은 이름-값의 쌍을 `;`을 이용해서 분리하였습니다. 이 문법은 여전히 유용하지만 `,`를 써야 합니다.

#### 선택적 객체 타입 프로퍼티 <a class="toc" id="toc-optional-object-type-properties" href="#toc-optional-object-type-properties"></a>

자바스크립트에서 존재하지 않는 프로퍼티를 접근하는 것은 `undefined`로 귀결됩니다. 이는 자바스크립트 프로그램에서 일반적인 에러이기에 flow는 이들을 에러로 분류합니다.

```js
// @flow
var obj = { foo: "bar" };
// $ExpectError
obj.bar; // Error!
```

때때로 프로퍼티가 없는 객체가 있다면 프로퍼티 이름 뒤에 물음표(`?`)를 붙여서 _선택적 프로퍼티_로 만들 수 있습니다.

```js
// @flow
var obj: { foo?: boolean } = {};

obj.foo = true;    // Works!
// $ExpectError
obj.foo = 'hello'; // Error!
```

선택적 프로퍼티는 설정한 타입, `void` 혹은 생략될 수 있지만 `null`이 될 수는 없습니다.

```js
// @flow
function acceptsObject(value: { foo?: string }) {
  // ...
}

acceptsObject({ foo: "bar" });     // Works!
acceptsObject({ foo: undefined }); // Works!
// $ExpectError
acceptsObject({ foo: null });      // Error!
acceptsObject({});                 // Works!
```

## 객체 타입 추론 <a class="toc" id="toc-object-type-inference" href="#toc-object-type-inference"></a>

flow는 어떻게 사용되는지에 따라서 객체 리터럴의 타입을 두가지 다른 방법으로 추론할 수 있습니다.

### 봉인된 객체(Sealed Objects) <a class="toc" id="toc-sealed-objects" href="#toc-sealed-objects"></a>

프로퍼티로 객체를 만들 때 flow는 봉인된 객체를 만듭니다. 이러한 봉인된 객체는 선언한 프로퍼티의 값과 이 값의 타입에 대해서 모두 알고 있습니다.

**Note:** 봉인된 객체(Sealed Object)란 객체안에 프로퍼티 정보가 존재하는 객체를 의미합니다.

```js
// @flow
var obj = {
  foo: 1,
  bar: true,
  baz: 'three'
};

var foo: number  = obj.foo; // Works!
var bar: boolean = obj.bar; // Works!
// $ExpectError
var baz: null    = obj.baz; // Error!
var bat: string  = obj.bat; // Error!
```

하지만 객체가 봉인되면 flow는 새로운 프로퍼티가 추가되는 것을 허용하지 않습니다.

```js
// @flow
var obj = {
  foo: 1
};

// $ExpectError
obj.bar = true;    // Error!
// $ExpectError
obj.baz = 'three'; // Error!
```

이를 피하기 위해서는 객체를 _미봉인 객체_로 바꿔야 합니다.

### 미봉인된 객체(Unsealed Objects) <a class="toc" id="toc-unsealed-objects" href="#toc-unsealed-objects"></a>

프로퍼티가 없는 객체를 만들면 flow에서는 _미봉인_ 객체 타입을 만듭니다. 이런 미봉인 객체는 프로퍼티에 대해 전혀 모르며 새로운 프로퍼티가 추가되는 것을 허용합니다.

**Note:** 미봉인된 객체(Unsealed Object)란 객체안에 프로퍼티 정보가 존재하지 않는 객체를 의미합니다.

```js
// @flow
var obj = {};

obj.foo = 1;       // Works!
obj.bar = true;    // Works!
obj.baz = 'three'; // Works!
```

프로퍼티의 추론된 타입은 어떤 것을 설정했느냐에 따라 다릅니다.

```js
// @flow
var obj = {};
obj.foo = 42;
var num: number = obj.foo;
```

##### 미봉인 객체 타입을 재할당하기 <a class="toc" id="toc-reassigning-unsealed-object-properties" href="#toc-reassigning-unsealed-object-properties"></a>

[`var`, `let` 변수](../variables/#toc-reassigning-variables)와 비슷하게 unseald 객체의 프로퍼티를 재할당 하면 기본저으로 flow는 모든 가능한 타입을 제공합니다.

```js
// @flow
var obj = {};

if (Math.random()) obj.prop = true;
else obj.prop = "hello";

// $ExpectError
var val1: boolean = obj.prop; // Error!
// $ExpectError
var val2: string  = obj.prop; // Error!
var val3: boolean | string = obj.prop; // Works!
```

때로 flow는 재할당 이후에 프로퍼티의 타입을 알아차릴 수 있습니다. 이 경우에 flow는 알고 있는 타입을 전달합니다.

```js
// @flow
var obj = {};

obj.prop = true;
obj.prop = "hello";

// $ExpectError
var val1: boolean = obj.prop; // Error!
var val2: string  = obj.prop; // Works!
```

flow는 점점 더 진화하고 있기 때문에 이러한 사니라오는 경우의 수는 줄어들 것입니다.

##### 미봉인 객체에 있는 알 수 없는 프로퍼티 접근은 안전하지 않습니다 <a class="toc" id="toc-unknown-property-lookup-on-unsealed-objects-is-unsafe" href="#toc-unknown-property-lookup-on-unsealed-objects-is-unsafe"></a>

미봉인 객체는 어느 시점이든 새로운 프로퍼티가 설정될 수 있습니다. flow는 읽기와 쓰기가 compatible하다는 것을 확신하지만 쓰기가 읽기 전에 일어난다는 것은 확신할 수 없습니다.

이는 쓰여진 적이 없는 프로퍼티를 가진 미봉인 객체로 부터 읽기에 대해 전혀 타입이 체크되지 않는다는 의미입니다. 이는 flow의 안전히지 못한 동작인데 향후에 개선될 예정입니다.

```js
var obj = {};

obj.foo = 1;
obj.bar = true;

var foo: number  = obj.foo; // Works!
var bar: boolean = obj.bar; // Works!
var baz: string  = obj.baz; // Works?
```

## 정확한 객체 타입 <a class="toc" id="toc-exact-object-types" href="#toc-exact-object-types"></a>

flow에서는 일반 객체 타입이 예상되는 경우 새로운 프로퍼티를 추가하는 것을 안전하다고 판단합니다.

```js
// @flow
function method(obj: { foo: string }) {
  // ...
}

method({
  foo: "test", // Works!
  bar: 42      // Works!
});
```

> **Note:** 이건 ["width subtyping"](../../lang/width-subtyping/)때문입니다.

때때로 이 동작을 비활성화하고 특정 속성만 허용하는 것이 유용합니다. 이를 위해 flow는 "정확한" 객체 타입을 지원합니다.

```js
{| foo: string, bar: number |}
```

일반적인 객체타입과 달리, 정확한 객체 타입에 "추가" 프로퍼티를 전달하는 것은 유효하지 않습니다.

```js
// @flow
var foo: {| foo: string |} = { foo: "Hello", bar: "World!" }; // Error!
```

## 객체를 맵으로 사용하기 <a class="toc" id="toc-objects-as-maps" href="#toc-objects-as-maps"></a>

새로운 자바스크립트 버전에서는 `Map` 클래스를 지원합니다. 하지만 여전히 객체를 맵으로 사용하는 것이 일반적입니다. 이런 경우에 객체는 생명주기 동안에 프로퍼티가 추가되거나 읽혀지는 프로퍼티를 가지게 됩니다. 게다가 프로퍼티 키는 정적으로 알 수 없기에 타입 어노테이션을 기술하는 것은 가능하지 않습니다.

이러한 객체를 위해 flow는 "인덱서 프로퍼티"라는 특별한 프로퍼티를 제공합니다. 인덱서 프로퍼티는 키를 이용해서 읽기, 쓰기를 허용하며 키는 인덱서 키 타입과 매칭됩니다.

```js
// @flow
var o: { [string]: number } = {};
o["foo"] = 0;
o["bar"] = 1;
var foo: number = o["foo"];
```

인덱서는 선택적으로 이름을 붙일 수 있습니다:

```js
// @flow
var obj: { [user_id: number]: string } = {};
obj[1] = "Julia";
obj[2] = "Camille";
obj[3] = "Justin";
obj[4] = "Mark";
```

객체 타입이 인덱서 프로퍼티를 가지면 객체가 런타임에 값을 가지지 않더라도 설정된 타입을 가지고 있다고 가정합니다. 안전한 접근인지에 대해서는 개발자가 확인해야 하는 부분입니다: 

```js
var obj: { [number]: string } = {};
obj[42].length; // No type error, but will throw at runtime
```

인덱서 프로퍼티는 이름을 가진 프로퍼티와 섞어서 사용할 수 있습니다.

```js
// @flow
var obj: {
  size: number,
  [id: number]: string
} = {
  size: 0
};

function add(id: number, name: string) {
  obj[id] = name;
  obj.size++;
}
```
