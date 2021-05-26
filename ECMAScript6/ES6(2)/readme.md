# ES6 문법 정리(2)
[ES6 문법 정리 1편](./../ES6(1)/readme.md)

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

# 11. Map, Set, Weakmap, Weaset
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
# 14. subclassable built-ins
# 15. Promise
# 16. math, number, string, array, object API
# 17. binary and octal literals
# 19. Reflect api
proxy 공부
# 20. tail calls
# 참고
* https://github.com/lukehoban/es6features
* [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide)
* [생활코딩](https://www.youtube.com/watch?v=LF23zkHIODQ&list=PLuHgQVnccGMAMctarDlPyv6upFUUnpSO3&index=15)
* [모던 JavaScript 튜토리얼](https://ko.javascript.info/https://ko.javascript.info/)
