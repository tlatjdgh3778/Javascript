# 화살표 함수(Arrow function)에 대한 정리

화살표 함수(Arrow function)는 함수를 보다 간결하게 `=>` 키워드를 사용해서 선언할 수 있다.

## 1. 구문

```js
(param1, param2) => { ... } // 매개 변수가 2개 이상이면 괄호는 생략 불가능
singleParam => { ... } // 매개 변수가 하나이면 괄호는 생략 가능
() => { ... } // 매개 변수가 없으면 괄호는 생략 불가능

param => { return expression; } // single line block
param => expression // 함수 몸체가 한 줄이면 중괄호 생략이 가능하고 암묵적으로 return 한다.
```

```js
() => { return {foo : bar}; }
() => ({ foo : bar }) // 객체 리터럴 표현을 반환할 때 소괄호 사용

() => {         // multi line block
    const x = 10;
    return x * x;
}
```

## 2. 화살표 함수의 호출

화살표 함수는 익명 함수로만 사용할 수 있기 때문에 화살표 함수를 호출하기 위해서는 함수 표현식을 사용한다.

```js
var pow = function (x) {
  return x * x;
};
console.log(pow(10));
```

이 함수를 화살표 함수로 만들면

```js
var pow = (x) => x * x;
console.log(pow(10));
```

물론 콜백 함수에서도 사용할 수 있다.

```js
var arr = [1, 2, 3];
var pow = arr.map(function (x) {
  // x는 요소값
  return x * x;
});

console.log(pow); // [ 1, 4, 9 ]
```

화살표 함수로 바꾸면

```js
const arr = [1, 2, 3];
const pow = arr.map((x) => x * x);

console.log(pow); // [ 1, 4, 9 ]
```

## 3. this

일반 함수에서의 `this` 는 함수를 호출할 때 함수가 어떻게 호출되었는지에 따라 `this` 에 바인딩할 객체가 동적으로 결정된다.  
[this](www.naver.com)

화살표 함수는 함수를 선언할 때 `this` 에 바인딩할 객체가 정적으로 결정된다. 일반 함수와는 달리 화살표 함수의 `this` 는 언제나 상위 스코프의 `this` 를 가리킨다. 이를 **Lexical this** 라고 한다.

화살표 함수의 `this` 바인딩 객체 결정 방식은 함수의 상위 스코프를 결정하는 방식인 **렉시컬 스코프** 와 유사하다.

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

## 4. 화살표 함수를 사용하면 안되는 경우

- 메소드로 사용

```js
var obj = {
  //
  i: 10,
  b: () => console.log(this.i, this),
  c: function () {
    console.log(this.i, this);
  },
};
obj.b(); // window
obj.c(); // 10
```

메소드로 정의한 화살표 함수의 `this` 는 메소드를 소유한 객체 `obj` 를 가리키지 않고 상위 스코프인 `window` 를 가리키게 된다.

- new 연산자 사용

화살표 함수는 생성자로 사용될 수 없으며, `new` 와 함께 사용하면 오류가 발생한다.

```js
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```

## 5. 참고

<https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Arrow_functions>  
<https://poiemaweb.com/es6-arrow-function> 
