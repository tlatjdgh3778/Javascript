# var, let, const에 대한 정리

---

## 1. `var`

자바스크립트 `es5` 버전까지의 변수 선언 방식이었다.

### `var` 키워드는 생략 가능하다.

```js
(var) a = 10;
console.log(a); // 10
```

### 재선언과 재할당이 모두 가능하다.

```js
var a = "10";
console.log(a); //10

var a = "20";
console.log(a); //20

a = "30";
console.log(a); //30
```

### `var`키워드는 함수 레벨 스코프(`function level scope`)이다.

자바스크립트는 다른 언어(`java`, `c`)와 달리 기본적으로 블록 레벨 스코프(`block level scope`)가 아니라 `function level scope`이다.
`function level scope`란 함수 안에서 사용하면 함수 밖을 제외한 내부 어디서든 접근이 가능하고 함수 외부에서 선언된 모든 변수는 전역 변수로 한다는 뜻이다.

```js
function f() {
  var hello = "hello world";
  console.log(hello);
}
f(); // hello world
console.log(hello); // ReferenceError
```

`hello`는 `var`로 선언된 `function level scope`이기 때문에 함수 외부에서 `hello`에 접근하면 `ReferenceError`가 발생한다.  
전역 변수의 사용은 변수 이름이 중복될 수 있고, 의도치 않은 재할당에 의한 상태 변화로 코드를 예측하기 어렵게 만들 수 있으므로 사용을 지양해야 한다. 이러한 문제점들을 해결하기 위해 `es6`부터는 `let`과 `const`의 도입으로 `block level scope`를 사용할 수 있게 해준다.

## 2. `let`, `const`

### 재선언은 `let`, `const` 둘 다 불가능하다.

```js
let a = 10;
let a = 20; // SyntaxError
```

```js
const b = 10;
const b = 20; // SyntaxError
```

### 재할당은 `let`은 가능하지만 `const`는 불가능하다.

```js
let a = 10;
a = 20;
console.log(a); // 20
```

```js
const b = 10;
b = 20;
console.log(b); // TypeError
```

### `let`, `const`는 블록 레벨 스코프(`block level scope`)이다.

`block level scope`란 모든 코드 블록(함수, if문, for문, while문, try/catch문 등..)내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없다. 즉, 코드 블록 내부에서 선언한 변수는 지역 변수이다.

```js
function f() {
  if (true) {
    let a = 10;
  }
  console.log(a); // ReferenceError
}
```

`let` 키워드는 해당 블록 안에서만 유효하기 때문에 `ReferenceError`가 출력된다. (`const`도 마찬가지)

## 3. 호이스팅

호이스팅(Hoisting) : **스코프 안의 어디에서든 변수 선언은 해당 스코프의 최상위에서 선언된 것과 동등하다.**

그 전에 잠깐 변수의 생성 단계를 보자면, 변수는 3단계의 생성 과정을 거친다.

**선언 단계** : 변수를 실행 컨텍스트의 변수 객체에 등록한다. 이 변수 객체는 스코프가 참조하는 대상이 된다.  
**초기화 단계** : 변수 객체에 등록된 변수를 위한 공간을 메모리에 확보한다. 이 단계에서는 변수는 undefined로 초기화된다.  
**할당 단계** : undefined로 초기화된 변수에 실제 값을 할당한다.

### `var`키워드로 변수를 생성하면 선언 단계와 초기화 단계가 동시에 이루어진다.

![](https://images.velog.io/images/tlatjdgh3778/post/be33b2de-c79d-4fe8-8e2c-82c55bfa1e33/var-lifecycle.png)

```js
console.log(a); // undefined
var a = 10;
```

아래의 코드와 위의 코드는 같다.

```js
var a; // hoisting
console.log(a); // undefined
a = 10;
```

이 코드에서 `a`가 선언과 초기화가 동시에 이루어지기 때문에 `undefined`가 출력되는 것이다.

```js
var a = 10;
function f() {
  console.log(a); // undefined
  var a = 20;
  console.log(a); // 20
}
f();
```

실제로는 이런식으로 동작하는 것이다.

```js
var a = 10;
function f() {
  var a; // hosting
  console.log(a); // undefined
  a = 20;
  console.log(a); // 20
}
f();
```

`let`, `const`키워드에서 호이스팅은 조금 다르다.

![](https://images.velog.io/images/tlatjdgh3778/post/7abfd2c7-bb4a-4988-af0e-16443261f26e/let-lifecycle.png)

`let`은 선언과 초기화가 분리되어 진행되는데, 스코프에 변수를 등록하지만 **초기화 단계는 변수 선언문에 도달했을 때** 이루어진다.`hoisting`이 되면 스코프 최상단에서 선언이 진행되고 초기화는 되지 않았기 때문에 `ReferenceError`가 뜬다.

```js
console.log(a); // ReferenceError
let a = 10; // 변수 선언문에서 초기화 단계 실행
```

**스코프의 시작 지점부터 초기화 시작 지점까지는 변수를 참조할 수 없다**. 스코프의 시작 지점부터 초기화 시작 지점까지의 구간을 일시적 사각지대`TDZ(Temporal Dead Zone)`라고 한다.
`let`과 `const`는 `TDZ`의 영향을 받는 구문들이다.

```js
// 					TDZ
console.log(a); // ReferenceError	TDZ
let a = 10;										
```

```javascript
// 					TDZ
console.log(a); // ReferenceError	TDZ
const a = 10;
```

위의 코드 `hoisting`을 수행하지만 초기화되기 전에 참조를 해서 `ReferenceError`가 출력된다.

## 4. `var` vs `let` vs `const`

변수 선언에는 기본적으로 `const`를 사용하고 `let`은 재할당이 필요한 경우에 사용하는 것이 좋다. `const` 키워드를 사용하면 의도치 않는 재할당을 방지해 주기 때문에 보다 안전하다.

1. 재할당이 필요한 경우에 한정해서 `let`을 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
2. 변경이 발생하지 않는 상수와 객체에는 `const`를 사용한다.

## 5. 참고

<https://ui.toast.com/weekly-pick/ko_20191014>  
<https://poiemaweb.com/es6-block-scope>
