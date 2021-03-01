# this에 대한 정리

Java 와 같은 객체지향 언어에서의 `this` 는 클래스(인스턴스화 된 객체) 자기 자신을 뜻한다.

자바스크립트는 함수 호출 방식에 따라 `tihs` 에 바인딩할 객체가 동적으로 결정된다. 함수를 선언할 때 `this` 에 바인딩할 객체가 정적으로 결정되는 것이 아니고, **함수를 호출할 때 함수가 어떻게 호출되었는지**에 따라 `this` 에 바인딩할 객체가 동적으로 결정된다.

함수를 호출하는 방식으로는 다음이 있다.

- 함수 호출
- 메소드 호출
- 생성자 함수 호출
- apply/call/bind 호출

## 1. 함수 호출

전역 객체는 모든 객체의 최상위 객체를 말하며 브라우저에서는 `window` 를 의미한다.

```js
this === window; // true
```

글로벌 스코프에 선언한 함수는 전역 객체의 프로퍼티로 접근할 수 있는 전역 변수의 메소드이다.

```js
var gv = "Global variable";

console.log(gv);
console.log(window.gv);

function foo() {
  console.log("invoked!");
}
window.foo();
```

기본적으로 `this` 는 전역 객체에 바인딩 된다. 전역 함수는 물론이고 내부 함수의 경우도 `this` 는 전역 객체에 바인딩 된다.

```js
function foo() {
  console.log("foo's this: ", this); // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```

내부 함수의 경우에도 `this` 는 `window`

```js
var value = 1;

var obj = {
  value: 100,
  foo: function () {
    console.log("foo's this: ", this); // obj
    console.log("foo's this.value: ", this.value); // 100
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }
    bar();
  },
};

obj.foo();
```

콜백 함수의 경우에도 `this` 는 전역 객체에 바인딩 된다.

```js
var value = 1;

var obj = {
  value: 100,
  foo: function () {
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  },
};

obj.foo();
```

내부 함수는 일반 함수, 메소드, 콜백 함수 어디에서 선언되었든 관계 없이 `this` 는 전역 객체를 바인딩 한다.

내부 함수의 `this` 가 전역 객체를 참조하는 것을 회피하고 싶다면 어떻게 해야할까 ?  
아래 코드를 보자

```js
var value = 1;

var obj = {
  value: 100,
  foo: function () {
    var that = this; // Workaround : this === obj

    console.log("foo's this: ", this); // obj
    console.log("foo's this.value: ", this.value); // 100
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1

      console.log("bar's that: ", that); // obj
      console.log("bar's that.value: ", that.value); // 100
    }
    bar();
  },
};

obj.foo();
```

`obj` 를 가리키는 `this` 를 `that` 변수에 저장 후 `that` 을 내부 함수에서 사용하면 된다.

또는 `apply`, `call` 을 사용할 수도 있다.

## 2. 메소드 호출

메소드 호출 시에는 해당 메소드를 호출한 객체에 바인딩 된다.

```js
function foo() {
  this.check = function () {
    console.log("this is", this);
  };
}

var f = new foo();
f.check(); // this is foo {check: ƒ}
```

## 3. 생성자 함수 호출

자바스크립트의 생성자 함수는 객체를 생성하는 역할을 한다. 기존 함수에 `new` 연산자를 붙여서 호출하면 해당 함수는 생성자 함수로 동작한다.

일반 함수에 `new` 연산자를 붙여서 호출해도 생성자 함수처럼 동작할 수 있기 때문에 일반적으로 생성자 함수는 첫 글자를 대문자로 한다.

생성자 함수가 생성하는 객체로 `this` 가 바인딩 된다.

```js
function Person(name) {
  this.name = name;
}

var kim = new Person("kim");

console.log(kim.name); // {name: kim}
```

하지만 `new` 키워드를 빼면 생성자 함수로 동작하지 않는다.

```js
var kim = Person("kim");

console.log(kim.name); // undefined
```

## 4. apply/call/bind 호출

`this` 에 바인딩 되는 객체는 함수 호출 패턴에 의해 자바스크립트 엔진이 암묵적으로 결정한다. 이러한 방식 말고 `this` 를 특정 객체에 명시적으로 바인딩하는 방법이 `apply` 와 `call` 이다.

`apply` 와 `call` 은 둘 다 `Function.prototype` 객체의 메소드이다

- **Function.prototype.call()**

```js
func.call(thisArg[, arg1[, arg2[, ...]]])

// thisArg : 함수 내부의 this에 바인딩할 객체
// arg1, arg2, ... : 함수에 전달할 인자들
```

아래 코드를 보자

```js
var person1 = {
  name: "Jo",
};

var person2 = {
  name: "Kim",
  study: function () {
    console.log(this.name + "이/가 공부를 하고 있습니다.");
  },
};

person2.study(); // Kim이/가 공부를 하고 있습니다.

// call()
person2.study.call(person1); // Jo이/가 공부를 하고 있습니다.
```

**`person2.study.call(person1);`** 이 코드에서 `person2` 의 함수를 호출하고 있지만 `call` 함수의 첫 번째 매개 변수에 `person1` 을 넣어주고 있기 때문에 `this` 는 `person1` 을 가리키게 된다.

- **Function.prototype.apply()**

```js
func.apply(thisArg, [argsArray]);

// thisArg: 함수 내부의 this에 바인딩할 객체
// argsArray: 함수에 전달할 argument의 배열
```

`apply` 는 두 번째 매개변수를 배열 형태(배열 또는 유사배열 객체)로 넣는다는 차이점 빼고는 `call` 이랑 같다.

## 5. 화살표 함수에서의 this

화살표 함수에서의 `this` 는 일반 함수에서의 `this` 와는 다르다.

화살표 함수는 함수를 선언할 때 this 에 바인딩할 객체가 정적으로 결정된다. 일반 함수와는 달리 화살표 함수의 this 는 언제나 상위 스코프의 this 를 가리킨다. 이를 Lexical this 라고 한다.

```js
function Person() {
  this.age = 0;

  setTimeout(() => {
    this.age++; // this는 상위 스코프인 Person 객체를 참조
    console.log(this.age);
  }, 1000);
}

Person(); // 1
```

## 6. 참고

<https://poiemaweb.com/js-this>  
<https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this>
