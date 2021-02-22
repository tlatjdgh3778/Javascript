# 클로저(Closure)

클로저는 함수와 함수가 선언된 `어휘적 환경(Lexical environment)`의 조합이다. 클로저를 이해하려면 자바스크립트가 어떻게 변수의 유효범위를 지정하는지를 먼저 이해해야 한다.

## 1. 렉시컬 스코프(Lexical Scoping)

함수가 중첩되어 있을 때, 스코프 체인 때문에 내부 함수에 찾는 식별자가 없다면 상위 스코프에서 식별자를 찾아 나간다. 아래 코드의 결과를 예측해보자.

```js
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

`bar()` 에는 `x`가 정의되어 있지 않기 때문에 스코프 체인에 의해서 상위 스코프로 이동하면서 `x`가 어디에 선언되어 있는지 확인한다.
`bar()` 의 상위 스코프가 `foo()`라면 `x`는 `10`이 될 것이고, 상위 스코프가 전역이라면 `x`는 `1`이 될 것이다.

`bar()` 함수가 어떤식으로 상위 스코프를 결정할까 ?  
두 가지 패턴이 있다.

1. 함수를 어디서 **호출**하였는지에 따라 상위 스코프를 결정하는 방식
2. 함수를 어디서 **선언**하였는지에 따라 상위 스코프를 결정하는 방식

첫 번째 방식을 `동적 스코프(Dynamic scope)`라고 하고, 두 번째 방식을 `렉시컬 스코프(Lexical scope)` 또는 `정적 스코프(Static scope)`라고 한다. 자바스크립트와 대부분의 언어는 렉시컬 스코프를 따른다.  
즉 어디서 선언하였는지에 따라 결정이 되므로 `bar()`의 상위 스코프는 전역 스코프이고 `x`의 값은 `1`이 된다.

## 2. 클로저(Closure)

클로저란

> 함수가 속한 렉시컬 스코프를 기억하여 함수가 렉시컬 스코프 밖에서 실행될 때에도 이 스코프에 접근할 수 있는데, 이 때 함수가 접근할 수 있는 렉시컬 스코프를 클로저라고 부른다.

라고 말할 수 있겠다.

아래 코드를 보자.

```js
function outter() {
  var title = "coding everybody";
  return function () {
    console.log(title);
  };
}
var inner = outter();
inner();
```

`outter()` 라는 외부함수를 실행한 결과(`return`) 는 `inner` 변수에 담긴다. `inner` 변수에 담겨있는 함수 `function() { alert(title); }` 를 호출하면 `coding everybody` 가 출력된다.  
여기서 이상한 점은 `inner` 변수에 담긴 함수를 호출할 때 `outter()` 함수는 이미 생을 마감하고 종료되었는데도 불구하고 `inner` 가 외부함수 `outter()` 에 존재하는 `title` 에 접근하고 있다는 것이다.

## 2-1. 클로저의 활용

### 1. 정보 은닉

```js
function factory_movie(title) {
  return {
    get_title: function () {
      return title;
    },
    set_title: function (_title) {
      title = _title;
    },
  };
}
ghost = factory_movie("Ghost in the shell");
matrix = factory_movie("Matrix");

console.log(ghost.get_title());
console.log(matrix.get_title());

ghost.set_title("공각기동대");

console.log(ghost.get_title());
console.log(matrix.get_title());
```

실행결과는 아래와 같을 것이다.

```
Ghost in the shell
Matrix
공각기동대
Matrix
```

위의 코드를 통해 알 수 있는 사실들이 몇 가지 있다.

1. 클로저는 객체의 메소드에서도 사용할 수 있다. 위의 코드는 `get_title` 과 `set_title` 메소드를 가진 객체를 반환하고 있다. 이 메소드들은 외부함수인 `factory_movie`의 인자값으로 전달된 지역변수 `title` 을 사용하고 있다.
2. 동일한 외부함수 안에서 만들어진 내부함수나 메소드는 외부함수의 지역변수를 공유한다. `ghost.set_title('공각기동대');` 의 코드로 지역변수 `title` 을 `공각기동대` 로 변경했다.
   두 번째 `console.log(ghost.get_title());` 의 출력 값이 `공각기동대` 인 이유는 `set_title` 과 `get_title` 메소드가 지역변수 `title` 의 값을 공유하고 있다는 뜻이다.
3. 똑같은 외부함수 `factory_movie` 를 공유하고 있는 `ghost` 와 `matrix` 의 `get_title` 값이 다른 이유는 외부함수가 실행될 때마다 새로운 지역변수를 포함하는 클로저가 생성되기 때문에 `ghost` 와 `matrix` 는 서로 완전히 독립된 개체가 되기 때문이다.
4. `factory_movie` 의 지역변수 `title` 의 값을 읽고 수정 할 수 있는 것은 `factory_movie` 메소드를 통해서 만들어진 객체 뿐이다.
5. `factory_movie` 가 어떠한 값을 리턴했을 때 그 함수는 생이 마감되었기 때문에 지역변수인 `title`은 `factory_movie` 의 내부함수인 `get_title` 과 `set_title` 을 통해서만 접근할 수 있는 `private` 한 변수가 되는 것이다.

### 2. 자주하는 실수

아래의 코드를 보자

```js
var arr = [];

for (var i = 0; i < 5; i++) {
  arr[i] = function () {
    return i;
  };
}

for (var j = 0; j < arr.length; j++) {
  console.log(arr[j]());
}
```

결과 값으로 0 1 2 3 4 가 나올 것 같지만 5 5 5 5 5 가 나온다. `for` 문을 풀어서 보면

```js
arr[i] = function () { return i; };
i = i + 1; // i = 1
arr[i] = function () { return i; };
i = i + 1; // i = 2
arr[i] = function () { return i; };
i = i + 1; // i = 3
arr[i] = function () { return i; };
i = i + 1; // i = 4
arr[i] = function () { return i; };
i = i + 1; // i = 5
```

`i = 5` 가 된다. 두 번째 `for` 문도 풀어보면

```js
console.log(arr[0]());
console.log(arr[1]());
console.log(arr[2]());
console.log(arr[3]());
console.log(arr[4]());
```

이 코드에서 각각 함수를 호출할 때 `function() {return i}` 에서 `i` 는 전역변수 `var i` 를 참조하기 때문에 5가 5번 출력되는 것이다.

다음과 같이 코드를 변경하게 되면 정상적으로 출력이 된다.

```js
var arr = [];
for (var i = 0; i < 5; i++) {
  arr[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}
for (var index in arr) {
  console.log(arr[index]());
}
```

- 배열 arr에는 즉시실행함수(IIFE)에 의해 함수가 반환된다.
- 이 때 즉시실행함수는 `i` 를 인자로 전달받고 매개변수 `id` 에 할당한 후 내부 함수를 반환하고 life-cycle이 종료된다. 매개변수 `id` 는 자유변수가 된다.
- 배열 `arr` 에 할당된 함수는 `id` 를 반환한다. 이 때 `id` 는 상위 스코프의 자유변수이므로 그 값이 유지된다.

`for` 문을 풀어서 보면 아래와 같을 것이다.

```js
arr[i] = (function (id) { return function () { return id; };})(i); 
// var id는 0
i = i + 1;
arr[i] = (function (id) { return function () { return id; };})(i); 
// var id는 1
i = i + 1;
arr[i] = (function (id) { return function () { return id; };})(i); 
// var id는 2
i = i + 1;
arr[i] = (function (id) { return function () { return id; };})(i); 
// var id는 3
i = i + 1;
arr[i] = (function (id) { return function () { return id; };})(i); 
// var id는 4
i = i + 1;
```

`arr[index]()` 함수를 호출하면 전역변수인 `i` 를 리턴하는 함수가 아니라 지역변수 `id` 를 리턴하기 때문에 0 1 2 3 4 가 출력이 된다.

## 3. 참고

<https://poiemaweb.com/js-closure>
<https://opentutorials.org/course/743/6544>
<https://hyunseob.github.io/2016/08/30/javascript-closure/>

여러가지 사이트를 참고하면서 정리는 했지만 아직 완벽하게 클로저에 대해서 이해를 한 것인지는 잘 모르겠다.. 일단은 이 정도까지만 정리를 해두고 다음에 더 깊게 공부를 해봐야겠다.
