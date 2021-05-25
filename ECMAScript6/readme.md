# ES6 문법 정리(1)
https://github.com/lukehoban/es6features 와 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide) 을 참고하여 ES6 문법을 정리해보고자 한다.

# 1. Arrow function
arrow function은 화살표 함수라고도 한다. function 키워드 대신 `=>` 키워드를 사용해서 보다 간략한 방법으로 함수를 선언할 수 있게 해준다.

## 1-1. **화살표 함수의 기본적인 형태**

```js
const elements = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];

elements.map(function(element){
	return element.length;
});
// [8, 6, 7, 9]

// arrow function
// 파라미터가 하나면 괄호 생략 가능하다.
elements.map(element => {
	return element.length;
});

// return 문이 한 줄이면 return과 중괄호({}) 생략 가능하다.
elements.map(element => element.length);
```

## 1-2. **Arrow Function에서의 `this`**

Arrow Function에서의 `this`는 함수가 **선언될 때 정적으로 바인딩** 된다.

동적으로 결정되는 일반 함수와는 달리 Arrow Function의 `this`는 언제나 **상위 스코프의 `this`** 를 가리킨다. (`Lexical this`)


아래의 코드는 `this`를 `print` 메소드 내부에서, 그리고 `print` 메소드 내부의 `forEach` 안에서 접근하고 있다.

`print` 메소드 내부의 `this`는 `obj`를 가리키고 있어 정상적으로 출력이 되지만 `forEach` 내부의 `this`는 `window`를 가리키고 있기 때문에 `undefined`가 출력된다.

```js
let obj = {
    names: ["A","B"],
    text: "님 안녕하세요",
    print: function() {
        console.log(this.text)             //님 안녕하세요
        this.names.forEach(function(name) {
            console.log(name + this.text) 
        })
    }
}
obj.print()
// 님 안녕하세요
// Aundefined
// Bundefined
```

`forEach`를 화살표 함수로 바꾸면 상위 스코프의 `this` 즉, `obj`를 가리킬 수 있다.

```js
let obj = {
    names: ["A","B"],
    text: "님 안녕하세요",
    print: function() {
        console.log(this.text)             //님 안녕하세요
        this.names.forEach((name) => {
            console.log(name + this.text)  
        })
    }
}
obj.print()
// 님 안녕하세요
// A님 안녕하세요
// B님 안녕하세요
```

arrow function에 대한 더욱 자세한 설명은 여기 [MDN - arrow functioin](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

# 2. Classes
`class` 란 객체를 만드는 공장이라고 할 수 있다.

`class` 가 나오기 전에는 객체를 만들 때 생성자 함수를 사용해서 만들 수 있었다.

```js
function Person(name, first, second){
	this.name = name;
  	this.first = first;
  	this.second = second;
}

let shim = new Person('shim', 10, 20);
console.log('shim',shim);

// Person {name: "shim", first: 10, second: 20}
```

위의 코드를 `class` 를 사용해서 작성해보면 다음과 같다.

```js
class Person {
	constructor(name, first, second){
		this.name = name;
      	this.first = first;
      	this.second = second;
    }
}
let shim = new Person('shim', 10, 20);
console.log('shim', shim);

// Person {name: "shim", first: 10, second: 20}
```

**class의 특징**

객체를 만들기 위한 템플릿으로 생성자 함수와 `class` 를 사용할 수 있다. 그럼 어떤 차이점이 있는지 한 번 알아보자.

* `class`에는 객체가 만들어지기 직전에 실행되도록 약속되어진 `constructor` 라는 함수가 존재한다. 이 함수를 이용해서 객체의 초기값을 설정할 수 있다.

* `getter` 와 `setter` 를 사용할 수 있다.
```js
class Person {
	constructor(age){
		this.age = age;
	}
  
  	get age() {
		return this._age;
    }
  
  	set age(value) {
    	this._age = value < 0 ? "0이상의 값을 입력해주세요" : value;
    }
}
let shim = new Person(-1);
console.log(shim);

// 0이상의 값을 입력해주세요.
```

* `class` 로 만든 함수에는 특수 내부 프로퍼티인 `[[FunctionKind]]:"classConstructor"` 가 존재한다. 자바스크립트는 이 프로퍼티를 확인하는 검증 과정이 있기 때문에 클래스를 `new` 키워드와 같이 사용하지 않으면 에러가 발생한다.

* `class`는 항상 `use strict` 모드로 실행된다.

class에 대한 더욱 자세한 설명은 여기 [MDN - class](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes)

# 3. Enhanced Object Literal
ES6에서 객체 리터럴은 기존 자바스크립트에서 사용하던 객체 정의 방식을 개선한 문법이다.

```js
let name = 'shim';
let prop = 'foo';
let obj = {
	// 1. 단축 속성명(속성과 값이 같은 경우)
  	// name: name
  	name,
  	
  	// 2. 메소드 정의할 때 function 생략
  	// property: function() {},
  	property() {},
  
  	// 3. 계산된 속성명 사용가능
  	[prop]: 'hey',
  	['b' + 'ar']: 'there',
}
// {name: "shim", foo: "hey", bar: "there", property: ƒ}
```

Enhanced Object Literal에 대한 자세한 설명은 여기 [MDN - Object Literal](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Grammar_and_types#%EA%B0%9D%EC%B2%B4_%EB%A6%AC%ED%84%B0%EB%9F%B4)

# 4. Template Literal(Strings)
Template Literal(ES5까지는 Template String)은 큰 따옴표나 작은 따옴표 대신 백틱 ` `` `을 사용하여 더욱 편하게 문자열을 만들 수 있게 해준다.

```js
// 1. 기본
`This is text.`

// 2. multi-line
str = "This is \ntext";
console.log(str);
// This is
// text

// Teplate Literal 로 표현하면
str = `This is
text`;
console.log(str);
// This is
// text

// 3. 표현식 삽입
let a = 5;
let b = 10;
console.log("a = " + a + " and\nb = " + b);
// a = 5 and
// b = 10

// Teplate Literal
console.log(`a = ${a} and
b = ${b}`);
// a = 5 and
// b = 10
```

Template Literal에 대한 자세한 설명은 여기 [MDN - Template Literal](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals)

# 5. Destructuring
Destructuring(구조 분해 할당)은 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 해주는 문법이다.

**배열 구조 분해**

```js
let arr = ["Shim", "Jang", "Kim"]

// first 에는 arr[0], second에는 arr[1] 을 할당
let [first, second] = arr;
console.log(first);
console.log(second);
// Shim
// Jang

// 쉼표를 사용해서 필요하지 않은 부분은 무시가 가능하다.
let [first, ,third] = arr;
console.log(first);
console.log(second);
// Shim
// Kim

// 나머지 할당하기 (rest)
let [first, ...rest] = arr;
console.log(first);
console.log(rest);
// Shim
// ["Jang", "Kim"]
```

**객체 구조 분해**

```js
// 기본 할당
let o = {p: 42, q: true};
let {p, q} = o;
console.log(p);
console.log(q);
// 42
// true

// 새로운 변수 이름으로 할당
// o.p를 foo라는 변수에 저장하는 식이다.
let {p: foo, q: bar} = o;
// p -> foo
// q -> bar
console.log(foo);
console.log(bar);
// 42
// true

// 객체로부터 분해(해체)된 값이 undefined 인 경우 변수에 기본값을 할당할 수 있다.
let {a = 10, b = 5} = {a: 3};
console.log(a);
console.log(b);
// 3
// 5

// 기본값 할당 + 새로운 이름의 변수에 할당
let {a: aa = 10, b: bb = 5} = {a: 3};
console.log(aa);
console.log(bb);
// 3
// 5
```

**중첩 구조 분해**

```js
let metaData = {
	title: "Scratchpad",
	translations: [
       {
        locale: "de",
        localization_tags: [ ],
        last_edit: "2014-04-14T08:43:37",
        url: "/de/docs/Tools/Scratchpad",
        title: "JavaScript-Umgebung"
       }
    ],
  // ...
};

let { title: engTitle, translations: [{ title: localeTitle }] } = metaData;

console.log(engTitle);
console.log(localeTitle);
// Scratchpad
// JavaScript-Umgebung
```

**for of 반복문을 사용한 구조 분해**
[for of](8-iterators,-for-of) ?

```js
let people = [
  {
    name: "Mike",
    family: {
    	father: "Harry",
    }, 
  },
  {
    name: "Tom",
    family: {
    	father: "Richard",
    },
  },
]

for (let {name: n, family: { father: f } } of people ) {
	console.log("Name: " + n + ", Father: " + f);
}

// Name: Mike, Father: Harry
// Name: Tom, Father: Richard
```

destructuring에 대한 더욱 자세한 설명은 여기 [MDN - destructuring](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

# 6. Default, Rest, Spread
## 6-1. Default Parameter
Deafult Parameter는 매개 변수에 기본값을 설정할 수 있다.
```js
function f(x, y=12) {
  return x + y;
}
f(3);
// 15
```

## 6-2. Rest Parameter
Rest Parameter는 가변 인자를 사용가능하며, 배열로 치환시켜준다. 함수의 마지막 파라미터의 앞에 `...` 을 붙여 나머지 인자를 배열로 대체한다. **마지막 파라미터만 Rest 파라미터가 될 수 있다.**
```js
function myFun(a, b, ...manyMoreArgs) {
  console.log("a", a);
  console.log("b", b);
  console.log("manyMoreArgs", manyMoreArgs);
}
myFun("one", "two", "three", "four", "five", "six");
// a one
// b two
// manyMoreArgs, [three, four, five, six]
```

## 6-2. Spread Operator
Spread Operator(전개 구문)는 배열, 문자열, 객체 등 반복 가능한 객체 (Iterable Object)를 개별 요소로 분리할 수 있다.

**배열 리터럴에서의 전개**

```js
let parts = ['shoulders', 'knees'];
let lyrics = ['head', ...parts, 'and', 'toes'];
// ["head", "shoulders", "knees", "and", "toes"]
```

spread를 사용해서 배열을 복사할 수도 있다.

```js
let arr = [1, 2, 3];
let arr2 = [...arr]; // arr.slice() 와 유사
arr2.push(4);

// arr2 은 [1, 2, 3, 4] 이 됨
// arr 은 영향을 받지 않고 남아 있음
```

**객체 리터럴에서의 전개**

```js
let obj1 = {
	foo: 'bar',
  	x: 42
};
let obj2 = {
	foo: 'baz',
  	y: 13
};

let cloneObj = { ...obj1 };
// Object { foo: 'bar', x: 42 }

let mergeObj = { ...obj1, ...obj2 };
// Object { foo: 'baz', x: 42, y: 13}
````

default, rest, spread에 대한 더욱 자세한 설명은 여기 [MDN - Default parameter](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Default_parameters), [MDN - Rest parameter](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/rest_parameters), [MDN - Spread Operator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax#%ED%95%A8%EC%88%98_%ED%98%B8%EC%B6%9C%EC%97%90%EC%84%9C%EC%9D%98_%EC%A0%84%EA%B0%9C)

# 7. Let, Const
`let`, `const` 는 ES6 버전에서 새롭게 추가된 변수 선언 방식이다. 

* **이전의 `var`와는 달리 `let`, `const`는 `_block level scope_` 이다.**

> 모든 코드 블록(함수, if, for, while, try/catch 등)내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없다. 즉, 코드 블록 내부에서 선언한 변수는 지역 변수이다.

```js
function f() {
  if (true) {
    let a = 10;
  }
  console.log(a); // ReferenceError
}
// let은 해당 블록 내에서만 유효하기 때문에 에러가 출력 된다.
```

* **`let`, `const`는 재선언이 불가능하다.**

```js
let a = 10;
let a = 20; // SyntaxError

const b = 10;
const b = 20; // SyntaxError
```

* **재할당은 `let`은 가능하지만 `const`는 불가능하다.**

```js
let a = 10;
a = 20;
console.log(a);
// 20

const b = 10;
b = 20;
console.log(b); // TypeError
```

let, const에 대한 자세한 설명은 여기 [MDN - let](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/let), [MDN - const](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/const)

# 8. Iteration, for of
## 8-1. Iteration
`iteration protocol` 두 가지 프로토콜이 있다. 하나는 `iterable protocol` 이고 다른 하나는 `iterator protocol` 이다. 일정 규칙만 충족한다면 어떠한 객체에 의해서도 구현될 수 있다.

## 8-2. Iterable
`iterable` 은 반복 가능한 객체를 의미한다. `iterable`로 평가된 것은 `for..of`, `spread`, `destructuring` 등에 사용할 수 있다.
배열은 대표적은 `iterable` 객체이다.

`iterable`이 되기 위해서 만족해야 하는 조건들이 있다.
* 객체 내에 `[Symbol.iterator] ( @@iterator)` 메소드가 존재해야 한다.
* `[Symbol.iterator]` 메소드는 `iterator` 객체를 반환해야 한다.

```js
let myIterable = {
*[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
}
};

[...myIterable];
// [1, 2, 3]
```
## 8-3. Iterator
`iterator`는 `iterable` 객체에서 반복을 실행하는 반복기를 뜻한다. `iterable` 객체가 반복하면서 어떠한 값을 반환할 것인지 결정하게 된다.

`iterator` 가 되기 위해서 만족해야 하는 조건들이 있다.
* 객체 내에 `next` 메소드가 존재해야 한다.
* `next` 메소드는 `IteratorResult` 객체를 반환해야 한다.
* `IteratorResult` 객체는 `done: boolean` 과 `value: any` 프로퍼티를 가진다.
* 이전 `next` 메소드 호출의 결과로 `done`의 값이 `true`를 리턴했다면, 이후 호출에 대한 `done`값도 `true`여야 한다.

`done`은 `iterator`의 반복이 모두 완료되었는지를 판별하는 역할을 한다. `done` 의 값이 `true`가 될 때까지 반복을 수행한다.

```js
const iterable = {
  [Symbol.iterator]() {
    let i = 0
    // iterator 객체
    return {
      next() {
        while(i < 10) { 
          // i가 10이 될 때까지 반복기 수행
          return { value: i++, done: false }
        }
        return { done: true } 
        // i 가 10이 되면 반복 종료(value 값 생략 가능)
      }
    }
  }
}

for(let num of iterable){
	console.log(num);
}
// 0
// 1
// 2
// ...
```

## 8-4. for..of
`for..of` 반복문은 `iterable` 객체에서 사용 가능한 반복문이다.

array에 대해 반복
```js
let arr = [10, 20, 30];

for(let value of arr){
	console.log(value);
}
// 10
// 20
// 30
```

이 외에도 string, map, set... `iterable` 객체에는 다 사용이 가능하다.

Iterator, for of에 대한 자세한 설명은 여기 [MDN - iterator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration_protocols#iterable), [MDN - for of](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...of)

# 9. Generators
`Generator`는 `function*`, `yield` 키워드를 사용해서 `iterator` 선언을 단순하게 작성할 수 있게 도와준다.

`Generator`는 함수의 실행을 중간에 멈췄다가 재개할 수 있다.

`Generator` 함수가 호출되면 `Generator` 객체가 반환된다. `Generator` 객체의 `next` 메소드를 호출하면 `Generator` 함수가 실행되어 `yield`문을 만날 때까지 진행한다. `yield` 문을 만나면 `value`와 `done` 프로퍼티를 가진 객체를 반환한다.

이후 `next` 메소드를 호출하면 진행이 멈췄던 위치에서부터 재실행한다. `next` 메소드를 인자값과 함께 호출할 경우 진행을 멈췄던 `yield` 문을 `next` 메소드에서 받은 인자값으로 치환하고 그 위치에서 다시 실행한다.

```js
function* logGenerator() {
  console.log(yield);
  console.log(yield);
  console.log(yield);
}

let gen = logGenerator();

gen.next();
gen.next('pretzel'); // pretzel
gen.next('california'); // california
gen.next('mayonnaise'); // mayonnaise
```

Generator에 대한 자세한 설명은 여기 [MDN - function*](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/function*#generator_%EB%8A%94_%EC%83%9D%EC%84%B1%EC%9E%90%EB%A1%9C%EC%84%9C_%EC%82%AC%EC%9A%A9%EB%90%A0_%EC%88%98_%EC%97%86%EB%8B%A4), [MDN - Generator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Generator)

# 참고
* https://github.com/lukehoban/es6features
* [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide)
* [생활코딩](https://www.youtube.com/watch?v=LF23zkHIODQ&list=PLuHgQVnccGMAMctarDlPyv6upFUUnpSO3&index=15)
* [모던 JavaScript 튜토리얼](https://ko.javascript.info/https://ko.javascript.info/)
