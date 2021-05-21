# ES6 문법 정리
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

```
# 6. default, rest, spread
# 7. let, const
# 8. iterators, for of
# 9. generators
# 10. unicode
# 11. modules
# 12. module loaders
# 13. map, set, weakmap, weaset
# 14. proxies
# 15. symbols
# 16. subclassable built-ins
# 17. promise
# 18. math, number, string, array, object API
# 19. binary and octal literals
# 20. reflect api
# 21. tail calls
# 21
# 참고
* https://github.com/lukehoban/es6features
* [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide)
* [생활코딩](https://www.youtube.com/watch?v=LF23zkHIODQ&list=PLuHgQVnccGMAMctarDlPyv6upFUUnpSO3&index=15)
* [모던 JavaScript 튜토리얼](https://ko.javascript.info/https://ko.javascript.info/)
