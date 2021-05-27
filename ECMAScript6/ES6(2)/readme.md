# ES6 문법 정리(2)
[ES6 문법 정리 1편](./../ES6(1)/readme.md)

# 목차
* [10. Module](#10-module)
* [11. Map, Set, Weakmap, WeakSet](#11-map-set-weakmap-weakset)
* [12. Proxy](#12-Proxy)
* [13. Symbol](#13-Symbol)
* [14. Promise](#14-Promise)
* [15. Math, Number, String, Array, Object API](#15-Math-Number-String-Array-Object-Api)
* [16. Binary and Octal Literals](#16-Binary-and-Octal-Literals)
* [17. Reflect api](#17-Reflect-api)
* [18. Tail Call](#18-Tail-Call)

# 10. Module
애플리케이션의 크기가 커지면서 파일을 여러개로 분리할 필요가 생긴다. 이 때 분리된 파일 각각을 모듈(Module)이라고 부른다.

모듈에 `export`, `import`를 사용하면 다른 모듈을 불러와 불러온 모듈에 있는 함수를 호출하는 것과 같은 기능 공유가 가능해진다.

## 10-1. export
`export`를 변수나 함수 앞에 붙이면 외부 모듈에서 해당 변수나 함수에 접근할 수 있다.(_모듈 내보내기_)

```js
// my-module1.js
function cube(x) {
	return x * x * x;
}
const foo = Math.PI + Math.SQRT2;
let graph = {
	options: {
    	color: 'white',
      	thickness: '2px',
    },
  	draw() {
    	console.log('From graph draw function');
    }
}

export { cube, foo, graph };
```

내보낼 것이 하나인 경우에는 `export default`를 사용해서 내보내기 한다.
```js
// my-module2.js
export default function cube(x) {
  return x * x * x;
}
```

## 10-2. import
`import`는 외부 모듈의 기능을 가져올 수 있다.(_모듈 가져오기_)

```js
import { cube, foo, graph } from './my-module1.js';
graph.options = {
    color:'blue',
    thickness:'3px'
};
graph.draw();
console.log(cube(3)); // 27
console.log(foo);    // 4.555806215962888
```

`export default`를 가져올 때에도 비슷하다.
```js
import cube from './my-module2.js';
console.log(cube(3));
// 27
```

module에 대한 더욱 자세한 설명은 여기 [MDN - import](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import), [MDN - export](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/export)

# 11. Map, Set, Weakmap, Weakset
## 11-1. Map
`Map`은 키가 있는 데이터를 저장한다는 점에서 객체와 비슷하지만 `Map`은 키에 다양한 자료형을 허용한다는 점에서 차이가 있다.

```js
let map = new Map(); // 맵을 만든다.
let obj = { name: 'shim' }; // map의 key로 object도 가능하다.

// map.set(key, value) key를 이용해 value를 저장
map.set('1', 'string'); // string key
map.set(1, 10); // num key
map.set(true, true); // boolean key
map.set(obj, 30); // object key
```

객체는 키를 문자형으로 변환하지만 `Map`은 키의 타입을 변환시키지 않는다.

```js
// map.get(key) key에 해당하는 값 반환
console.log(map.get('1'));
console.log(map.get(1));
console.log(map.get(true));
console.log(map.get(obj));
// string
// 10
// true
// 30
```

**`for..of` 로 `map` 순회**
```js
let myMap = new Map()
myMap.set(0, 'zero')
myMap.set(1, 'one')

for (let [key, value] of myMap) {
  console.log(key + ' = ' + value)
}
// 0 = zero
// 1 = one

for (let key of myMap.keys()) {
  console.log(key)
}
// 0
// 1

for (let value of myMap.values()) {
  console.log(value)
}
// zero
// one

for (let [key, value] of myMap.entries()) {
  console.log(key + ' = ' + value)
}
// 0 = zero
// 1 = one
```

## 11-2. Set
`Set`은 중복을 허용하지 않는 값을 모아놓은 컬렉션이다. 키가 없는 값이 저장된다.

```js
let mySet = new Set();

// set.add(value) set에 value를 추가하고 set 자신을 반환한다.
mySet.add(1); // Set { 1 }
mySet.add(5); // Set { 1, 5 }
mySet.add(5); // Set { 1, 5 }
mySet.add('some text'); // Set { 1, 5, 'some text' }
var o = {a: 1, b: 2};
mySet.add(o);

mySet.add({a: 1, b: 2}); // o와 다른 객체를 참조하므로 괜찮음

mySet.has(1); // true
mySet.has(3); // false, 3은 set에 추가되지 않았음
mySet.has(5);              // true
mySet.has(Math.sqrt(25));  // true
mySet.has('Some Text'.toLowerCase()); // true
mySet.has(o); // true

mySet.size; // 5

mySet.delete(5); // set에서 5 제거
mySet.has(5); // false

mySet.size; // 4
console.log(mySet);
// Set {1, "some text", Object {a: 1, b: 2}, Object {a: 1, b: 2}}
```

**`for..of` 로 `set` 순회**
```js
for(let item of mySet){
    console.log(item);
}
// 1
// "some text"
// { a: 1, b: 2 }
// { a: 1, b: 2 }

for(let item of mySet.keys()){
	console.log(item);
}
for(let item of mySet.values()){
	console.log(item);
}
for(let [key, value] of mySet.entries()){
	console.log(key);
}
// 1
// "some text"
// { a: 1, b: 2 }
// { a: 1, b: 2 }
```
* `set.keys()` 는 `set` 내의 모든 값을 포함하는 `iterable` 객체를 반환한다.
* `set.values()` 는 `set.keys()`와 동일한 작업을 한다. `Map`과의 호환성을 위해 만들어졌다.
* `set.entries()`는 `set` 내의 각 값을 이용해 만든 `[value, value]` 배열을 포함하는 `iterable` 객체를 반환한다. `Map`과의 호환성을 위해 만들어졌다.

`Set` 객체를 `Array` 객체로 변환할 수 있다. (`Array.from`으로)
```js
let myArr = Array.from(mySet); 
// [1, "some text", {"a": 1, "b": 2}]
```

## 11-3. WeakMap, WeakSet
추가 예정..

Map과 Set에 대한 더욱 자세한 설명은 여기 [MDN - Map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map), [MDN - Set](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Set)

# 12. Proxy
`Proxy` 는 특정 객체를 감싸 프로퍼티 읽기, 쓰기와 같은 객체에 가해지는 작업을 중간에 가로채는 객체이다. 가로채진 작업은 `Proxy` 자체에서 처리되기도 하고, 원래 객체가 처리하도록 그대로 전달되기도 한다.

```js
let proxy = new Proxy(target, handler);
```
* `target` : 감싸게 될 객체, 함수를 포함한 모든 객체가 가능하다.
* `handler` : 동작을 가로채는 메소드인 트랩(`trap`)이 담긴 객체로, 여기서 `proxy`를 설정한다.
  * `get` 트랩은 `target`의 프로퍼티를 읽을 때
  * `set` 트랩은 `target`의 프로퍼티를 쓸 때 활성화 된다.
  
`proxy`에 작업이 가해지고 `handler`에 트랩이 있으면 트랩이 실행되어 `Proxy`가 이 작업을 처리할 기회를 얻는다. 트랩이 없으면 `target`에 작업이 직접 수행된다.

트랩이 없는 경우를 보자
```js
let target = {};
let proxy = new Proxy(target, {});

proxy.test = 5; // proxy에 값을 쓴다.

// target에 새로운 프로퍼티가 생겼다.
console.log(target.test);
// 5

// proxy 를 사용해 값을 읽을 수 있다.
console.log(proxy.test);
// 5

for(let key in proxy){
	console.log(key);
}
// test
// target -> { test: 5 }
```

이 경우에는 트랩이 없기 때문에 `proxy`에 가해지는 작업은 `target`에 전달된다.
* `proxy.test=`를 이용해 값을 쓰면 `target`에 새로운 값이 설정된다.
* `proxy.text`를 이용해 값을 읽으면 `target`에서 값을 읽어온다.
* `proxy`를 대상으로 반복 작업을 하면 `target`에 저장된 값이 반환된다.

`proxy`는 `target`을 감싸는 투명한 래퍼가 된다.

트랩의 종류에는 여러가지가 있는데 대표적으로 `get`, `set`, `has`가 있다.

### `get` 트랩으로 프로퍼티 기본값 설정하기
`get` 트랩은 프로퍼티를 읽을 때 사용되는 트랩이다.
`get` 을 사용해 존재하지 않는 값을 읽을 때 기본값 `0` 을 반환해주도록 만들어보자

`get(target, property, receiver);`
* `target`: 동작을 전달할 객체
* `property`: 프로퍼티 이름
* `receiver`: 타겟 프로퍼티가 getter라면 `receiver`는 `getter`가 호출될 때 `this`이다. 대게 `proxy` 객체 자신이 `this` 가 된다. `proxy` 객체를 상속받은 객체가 있다면 해당 객체가 `this`가 된다.

```js
let numbers = [1, 2, 3];
let handler = {
	get: function(target, prop){
    	return prop in target ? target[prop] : 0;
    }
};

let proxy = new Proxy(numbers, handler);

console.log(proxy[1]);
// 2
console.log(propxy[100]);
// 해당하는 요소가 없기때문에 0
```

### `set` 트랩으로 프로퍼티 값 검증하기
`set` 트랩은 프로퍼티에 값을 쓰려고 할 때 이를 가로챈다.

`set(target, property, value, receiver);`
* `target`: 동작을 전달할 객체
* `property`: 프로퍼티 이름
* `value`: 프로퍼티 값
* `receiver`: `get` 트랩과 유사하게 동작하는 객체, `setter` 프로퍼티에만 관여한다.

```js
let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }
    // 기본
    obj[prop] = value;
  }
};

let proxy = new Proxy({}, validator);

proxy.age = 100;
console.log(proxy.age);
// 100
person.age = 'young'; 
// TypeError: The age is not an integer
person.age = 300; 
// RangeError: The age seems invalid
```

트랩의 종류는 다음과 같다.

**Property**

* get
* set
* has
* deleProperty

**Method**

* apply
* construct

**Object**

* getPrototypeOf
* setPrototypeOf
* isExtensible
* preventExtensions
* getOwnPropertyDescriptor
* defineProperty
* ownKeys

proxy에 대한 더욱 자세한 설명은 여기 [MDN - proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy), [모던 JavaScript - proxy](https://ko.javascript.info/proxy#ref-587)

# 13. Symbol
자바스크립트는 이전까지 6개의 타입만을 가지고 있었다.
* **원시 타입(Primitive data type)**
  * boolean
  * null
  * undefined
  * Number
  * String
* **객체 타입(Object type)**
  * Object
  
ES6에서 새롭게 추가된 Symbol은 주로 이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키를 만들기 위해 사용한다.

## 13-1. Symbol()

아래의 코드는 Symbol을 생성하는 코드이다. Symbol을 생성할 때에는 `new`키워드를 사용하지 않는다.

`Symbol()`함수를 사용해서 Symbol을 생성하는데 `Symbol()`함수의 인자로 문자열이 올 수 있다. 이 문자열은 생성에는 어떤 영향을 주지 않고 단지 Symbol에 대한 설명으로 디버깅 용도로만 사용된다.

```js
const sym1 = Symbol(); // Symbol()
const sym2 = Symbol('foo'); // Symbol(foo)
const sym3 = Symbol('foo'); // Symbol(foo)

console.log(sym2 === sym3);
// false

```
`.description` 으로 description값을 알아낼 수 있다.
```js
console.log(sym2.description);
// "foo"
```

객체의 프로퍼티 키로는 빈 문자열을 포함하는 모든 문자열 또는 Symbol이 올 수 있다.
```js
const obj = {};
obj.prop = 'myProp';
obj[123] = 123;
obj['prop' + 123] = false;

console.log(obj);
// {123: 123, prop: "myProp", prop123: false}
```

Symbol을 키로 갖는 프로퍼티는 다른 어떠한 프로퍼티와도 충돌하지 않는다.
```js
const obj = {};

const mySymbol = Symbol('mySymbol');
obj[mySymbol] = 123;

console.log(obj);
// {Symbol(mySymbol): 123}
console.log(obj[mySymbol]);
// 123
```

`Object.keys()`, `Object.values()`, `Object.entries()`, `for..in` 에서 Symbol로 만든 프로퍼티 키는 건너뛴다.
```js
const obj = {};
obj.name = 'shim';
obj.age = 20;

const id = Symbol('id');

obj[id] = 300
console.log(obj);
// {name: "shim", age: 20, Symbol(id): 300}
console.log(Object.keys(obj));
// (2) ["name", "age"]
console.log(Object.values(obj));
// (2) ["shim", 20]
console.log(Object.entries(obj));
// (2) [Array(2), Array(2)]
for(let key in obj){
	console.lob(key);
}
// name
// age
```

## 13-2. Symbol.for()
`Symbol.for()` 메소드는 인자로 전달받은 문자열을 키로 사용하여 Symbol값들이 저장되어 있는 전역 Symbol 레지스트리에서 해당 키와 일치하는 저장된 Symbol 값을 검색한다. 검색에 성공하면 검색된 Symbol 값을 반환하고, 실패하면 새로운 Symbol 값을 생성하여 해당 키로 전역 Symbol 레지스트리에 저장한 후 Symbol 값을 반환한다.

```js
const sym1 = Symbol.for('foo');
const sym2 = Symbol.for('foo');

console.log(sym1 === sym2);
// true
```
이를 **전역 심볼** 이라고 한다.

전역 심볼의 키를 가져오기 위해서는 `ketFor` 메소드를 사용한다.

```js
console.log(Symbol.keyFor(sym1));
// foo
```

Symbol에 대한 더욱 자세한 설명은 여기 [MDN - Symbol](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

# 14. Promise
ES6에서 비동기 처리를 위해 Promise를 도입했다. 

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(()=>{
  	resolve("Success!!");
  }, 2000)
  //reject(new Error("error"));
});

console.log("start");
promise.then(res => {
    console.log(res);
}).catch(err => {
    console.log(res);
}).finally(() => {
	console.log("end");
});

// start
// Success!! (1s)
// end
```

`new Promise` 가 반환하는 promise 객체는 state와 result를 프로퍼티로 가진다.

초기에는 `state: pending`, `result: undefined` 였다가 `resolve(value)` 가 호출되면(성공하면) `state: fulfilled`, `result: value` 가 된다.

`reject(err)` 가 호출되면(실패하면) `state: rejected`, `result: error` 가 된다.

## Promise Chaining
프로미스 후속 처리 메소드인 `then`, `catch`를 사용할 수 있는데 `then` 으로 프로미스를 연결한 것을 promise chaining 이라고 한다.

```js
const promise1 = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("첫 번째");
        }, 1000)
    })
};

const promise2 = (message) => {
    console.log(message);
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("두 번째");
        }, 1000)
    })
};

const promise3 = (message) => {
    console.log(message);
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("세 번째");
        }, 1000)
    })
};

console.log("start");

promise1()
.then(res => promise2(res))
.then(res => promise3(res))
.then(res => console.log(res))
.catch(err => console.log(err))
.finally(() => {
    console.log("end"); 
});

// start
// 첫 번째 (1s)
// 두 번째 (1s)
// 세 번째 (1s)
// end
```

promise에 대해 더욱 자세한 설명은 여기 [MDN - promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)

# 15. Math, Number, String, Array, Object API
ES6에서 core Math 라이브러리, Array 생성 helper, String helper, 복사를 위한 Object.assign 등 많은 라이브러리들이 추가되었다.

## 1. Number 

### 1-1. Number.EPSILON
javascript 에서 표현할 수 있는 가장 작은 수이다.
2.220446049250313e-16,  2^-52

#### 구문
```js
Number.EPSILON;
```
#### 예제
```js
Number.EPSILON;
```

### 1-2. Number.isInteger()
값이 정수인지 확인한다.
#### 구문
```js
Number.isInteger(value);
```
#### 예제
```js
Number.isInteger(Infinity); // false
```

### 1-3. Number.isNaN()
값이 NaN인지 확인한다.
#### 구문
```js
Number.isNaN(value);
```
#### 예제
```js
Number.isNaN(0 / 0); // true
```

## 2. Math

### 2-1. Math.acosh()
쌍곡선 아크 코사인 값을 반환한다.

#### 구문
```js
Math.acosh(x);
```
#### 예제
```js
Math.acosh(3); 
// 1.762747174039086
```

### 2-2. Math.hypot()
주어진 인수의 제곱의 합의 제곱근을 반환한다.

#### 구문
```js
Math.hypot(value0, value1, ... , valueN);
```
#### 예제
```js
Math.hypot(3, 4); 
// 5
```

### 2-3. Math.imul(a, b)
2개의 파라미터의 32bit 곱셈 결과를 반환한다.

#### 구문
```js
Math.imul(a, b);
```
#### 예제
```js
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2);
// 2
```

## 3. String

### 3-1. String.prototype.includes()
해당하는 문자열이 포함되는지 판별한다.

#### 구문
```js
str.includes(searchString[, position]);
```
#### 예제
```js
"abcde".includes("cd") 
// true
```

### 3-2. String.prototype.repeat()
문자열을 주어진 횟수만큼 반복해 붙인 새로운 문자열을 반환한다.

#### 구문
```js
str.repeat(count);
```
#### 예제
```js
"abc".repeat(3) 
// "abcabcabc"
```

## 4. Array

### 4-1. Array.from()
유사 배열 객체나 반복 가능한 객체를 얕게 복사해서 새로운 Array 객체를 생성한다.

#### 구문
```js
arr.from(arrayLike[, mapFn[, thisArg]]);
```
#### 예제
```js
Array.from("foo");
// ["f", "o", "o"]
```

### 4-2. Array.of()
새로운 Array 인스턴스를 생성한다.

#### 구문
```js
arr.of(element0[, element1[, ...[, elementN]]]);
```
#### 예제
```js
console.log(Array.of(1, 2, 3)); 
// [1, 2, 3]

Array.of(7); 
// [7]
Array(7); 
// [ , , , , , ,]
```

### 4-3. Array.prototype.fill()
배열의 시작 인덱스부터 끝 인덱스의 이전까지 값 하나로 채운다.

#### 구문
```js
arr.fill(value[, start[, end]])
```
#### 예제
```js
const arr = [0, 0, 0];

console.log(arr.fill(7, 1)); 
// [0,7,7]
```

### 4-4. Array.prototype.find()
주어진 판별 함수를 만족하는 첫 번째 요소의 값을 반환한다.

#### 구문
```js
arr.find(callback[, thisArg]);
```
#### 예제
```js
const arr = [1, 2, 3];

console.log(arr.find(x => x === 3));
// 3
```

### 4-5. Array.prototype.findIndex()
주어진 판별 함수를 만족하는 배열의 첫 번째 요소에 대한 인덱스를 반환한다. [thisArg]는 선택사항

#### 구문
```js
arr.findIndex(callback(element[, index[, array]])[, thisArg]);
```
#### 예제
```js
const arr = [1, 2, 3];

console.log(arr.findIndex(x => x === 2));
// 1
```

### 4-6. Array.prototype.copyWithin()
배열의 일부를 얕게 복사한 뒤, 동일한 배열의 다른 위치에 덮어쓰고 그 배열을 반환한다.

#### 구문
```js
arr.copyWithin(target[, start[, end]]);
```
#### 예제
```js
const arr = ['a', 'b', 'c', 'd', 'e'];

console.log(arr.copyWithin(0, 3, 4));
// ["d", "b", "c", "d", "e"]
```

### 4-7. Array.prototype.entries()
배열의 각 인덱스에 대한 키/값 쌍을 가지는 새로운 Array Iterator 객체를 반환한다.

#### 구문
```js
arr.entries()
```
#### 예제
```js
const arr = ['a', 'b', 'c'];

const iterator = arr.entries();

console.log(iterator.next().value);
// [0, "a"]

console.log(iterator.next().value);
// [1, "b"]
```

### 4-8. Array.prototype.keys()
배열의 각 인덱스를 키 값으로 가지는 새로운 Array Iterator 객체를 반환한다.

#### 구문
```js
arr.keys();
```
#### 예제
```js
const arr = ['a', 'b', 'c'];

const iterator = arr.keys();

for (const key of iterator) {
  console.log(key);
}

// 0
// 1
// 2
```

### 4-9. Array.prototype.values()
배열의 각 인데긋에 대한 값을 가지는 새로운 Array Iterator 객체를 반환한다.

#### 구문
```js
arr.values();
```
#### 예제
```js
const arr = ['a', 'b', 'c'];

const iterator = arr.values();

for (const value of iterator) {
  console.log(value);
}

// a
// b
// c
```

## 5. Object

### 5-1. Object.assign()
열거할 수 있는 하나 이상의 출처 객체로부터 대상 객체로 속성을 복사할 때 사용한다. 대상 객체를 반환한다.(source가 tartget에 병합)

#### 구문
```js
Object.assign(target, ...sources);
```
#### 예제
```js
const target = { a: 1, b: 2 };
const source = { b: 4, c: 5 };

const returnedTarget = Object.assign(target, source);

console.log(target);
// { a: 1, b: 4, c: 5 }

console.log(returnedTarget);
// { a: 1, b: 4, c: 5 }
```

더욱 자세한 내용은 여기 [MDN - Number](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number), [MDN - Math](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Math), [MDN - String](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String), [MDN - Array](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array). [MDN - Object.assign](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

# 16. Binary and Octal Literals
2진법(b), 8진법(o) numeric 리터럴 형식이 추가되었다.

## Binary
ES5에서는 2진수 리터럴을 나타내기 위해서 `parseInt()` 를 사용했었다.
```js
let a = parseInt('111',2);
console.log(a); 
// 7
```
ES6에서는 접두사 **0b** 를 사용해서 2진수를 나타낸다.
```js
let a = 0b111;
console.log(a); 
// 7
```
## Octal
ES5에서는 8진수 리터럴을 나타내기 위해 접두사 **0**를 사용했었다.
```js
const a = 051;
console.log(a); 
// 41
```

ES6버전에서는 접두사 **0o**를 사용한다.
```js
const a = 0o51;
console.log(a); 
// 41 
```

더욱 자세한 내용은 여기 [A Quick Look at Octal and Binary Literals in ES6
](https://www.javascripttutorial.net/es6/octal-and-binary-literals/)

# 17. Reflect api
proxy 공부

# 18. Tail Call
[ES6 Tail Call](https://infoscis.github.io/2018/01/24/ecmascript-6-functions/)...

# 참고
* https://github.com/lukehoban/es6features
* [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide)
* [생활코딩](https://www.youtube.com/watch?v=LF23zkHIODQ&list=PLuHgQVnccGMAMctarDlPyv6upFUUnpSO3&index=15)
* [모던 JavaScript 튜토리얼](https://ko.javascript.info/https://ko.javascript.info/)
* [코딩앙마](https://www.youtube.com/channel/UCxft4RZ8lrK_BdPNz8NOP7Q)
