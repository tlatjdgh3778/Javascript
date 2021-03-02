# 프로토타입(Prototype)에 대한 정리

자바스크립트는 흔히 프로토타입 기반 언어(prototype-based language)라 불린다. 일반적으로 프로토타입은 **원형**이라는 뜻을 가진다.

자바스크립트의 모든 객체는 자신의 부모 역할을 담당하는 객체와 연결되어 있다. 이것은 마치 객체 지향의 상속 개념과 같이 부모 객체의 프로퍼티 또는 메소드를 상속받아 사용할 수 있게 한다. 이러한 부모 객체를 **Prototype 객체**, **Prototype** 이라고 한다.

---

자바스크립트에서 함수는 객체이기 때문에 아래의 두 코드는 같은 의미를 가진다.

```js
function Person() {}
```

```js
var Person = new Function();
```

객체는 언제나 함수로 생성이 되는데

```js
function Person() {}
var personObject = new Person(); // 함수로 객체를 생성
```

`personObject` 객체는 `Person` 이라는 함수로 생성된 객체이다.

```js
var obj = {};
```

이런 객체 생성 코드도 사실은 아래의 코드와 동일하다.

```js
var obj = new Object();
```

`Object` 는 자바스크립트에서 기본적으로 제공하는 함수이다. `Function`, `Array` 도 모두 함수로 정의되어 있다.

## 1. 프로토타입 예제

프로토타입을 어디에 쓸까 ? 아래 코드를 보자

```js
function Person(name, first, second) {
  this.name = name;
  this.first = first;
  this.second = second;
  this.sum = function () {
    return this.first + this.second;
  };
}
var kim = new Person("kim", 10, 20);
var lee = new Person("lee", 10, 10);
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

`sum` 이라는 함수는 `Person` 함수가 생성자로 동작할 때마다 새로 만들어지고 있다. 여기서는 `kim` 과 `lee` 밖에 없지만 만약 100개, 1000개 있다고 생각하면 엄청난 메모리 낭비이다. 이런 문제를 프로토 타입으로 해결할 수 있다.

```js
function Person(name, first, second ){
    this.name = name;
    this.first = first;
    this.second = second;
    }
}
Person.prototype.sum = function(){
    return this.first + this.second;
}
var kim = new Person('kim', 10, 20);
var lee = new Person('lee', 10, 10);
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

`Person.prototype` 이라는 `Object` 가 존재하고 `Person` 함수로 만든 객체(`kim`,`lee`)들은 이 `Object` 에 들어있는 값들을 사용할 수 있다.

## 2. Prototype Object

함수가 정의될 때 어떤 일이 일어나는지 보자.

`Person` 함수를 정의하면 함수만 생기는 것이 아니라 내부적으로 `Person` 의 Prototype Object 도 같이 생성이 된다.

![](https://miro.medium.com/max/700/1*PZe_YnLftVZwT1dNs1Iu0A.png)  
[이미지 참조](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)  
`Person` 함수는 `prototype` 이라는 프로퍼티로 `Person` 의 Prototype Object는 `constructor` 라는 프로퍼티로 서로 상호참조를 하게된다.
Prototype Object 는 `constructor` 와 `__proto__` 프로퍼티를 가지고 있다
`constructor` 는 Prototype Object 와 같이 생성된 함수 `Person` 을 가르키고
`__proto__` 는 Prototype Link 라고도 한다. 자신을 생성한 부모의 Prototype Object 을 가르킨다.

```js
function Person(name, first, second ){
    this.name = name;
    this.first = first;
    this.second = second;
    }
}
Person.prototype.sum = function(){
    return this.first + this.second;
}
var kim = new Person('kim', 10, 20);
var lee = new Person('lee', 10, 10);
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

이 코드에서 `kim` 과 `lee` 의 `__proto__` 는 `Person` 의 Prototype Object 를 가르키기 때문에 `Person.prototype` 을 참조할 수 있게 되는 것이다. `kim` 과 `lee` 객체 자신이 `sum` 이라는 함수를 가지고 있지 않으면 생성자인 `Person` 의 Prototype Object 에 `sum` 이 정의되어 있는지 찾아본다.
<figure>
  <img
  src="https://images.velog.io/images/tlatjdgh3778/post/babb2b0f-2f46-471e-b7b7-bee9fe3657b1/1.png"
  alt="출처- 생활코딩">
  <figcaption style="text-align:center">이미지 출처 - 생활코딩</figcaption>
</figure>

## 3. Prototype Object의 특징

`Person` 의 Prototype Object 는 **생성 당시**의 정보를 복제한다. 아래 코드를 보자.

```js
var person = function () {
  this.hp = function () {
    console.log("100");
  };
};

person.hp = function () {
  console.log("50");
};

var p1 = new person();
p1.hp(); // 100
```

결과가 100이 나오는 이유는 Prototype Object 가 생성 당시의 정보를 복제하기 때문인데 즉 `Person` 의 생성 당시 hp 는 100이기 때문이다.

만약에 50을 출력하고 싶다면 어떻게 하면 될까 ?

```js
var person = function () {};

person.hp = function () {
  console.log("100");
};

person.prototype.hp = function () {
  console.log("50");
};
var p1 = new person();
p1.hp(); // 50
```

정답은 Prototype Object에 직접 접근하면 된다. 생성 당시의 Prototype Object는 값이 없었고 `person.prototype.hp = function() {console.log("50");}` 코드 후에 50으로 변경되었기 때문에 출력값이 50이 된다.

요약하자면

- `ptorotype` : 자신의 Prototype Object 를 가르킨다.
- `constructor` : 생성자로써, Prototype Object와 같이 생성되었던 함수를 가리키고 있습니다.
- `__proto__` : 자신을 생성한 부모의 Prototype Object 을 가르킨다. 모든 객체가 기본적으로 가지고 있다.

## 4. 참고

<https://mygumi.tistory.com/312>  
<https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67>
