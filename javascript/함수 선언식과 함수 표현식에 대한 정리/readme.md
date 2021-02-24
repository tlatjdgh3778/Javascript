# 함수 선언식과 함수 표현식

자바스크립트에서 함수를 정의하는 방법에는 함수 선언식(Function Declarations)과 함수 표현식함수 표현식(Function Expressions)이 있다.

## 1. 함수 선언식(Function Declarations)

일반적인 프로그래밍 언어에서의 함수 선언과 비슷한 형식이다. `function` 키워드를 사용하여 정의하면 된다.

```js
function print(a, b) {
  return a + b;
}
print(10, 20); // 30
```

## 2. 함수 표현식(Function Expressions)

자바스크립트 함수는 표현식을 사용하여 정의 될 수 있고, 함수 표현식은 변수로 저장될 수 있다.

```js
let print = function (a, b) {
  return a + b;
};
console.log(print(10, 20)); // 30
```

함수 표현식으로 함수를 정의하면 함수명은 생략이 가능하다. 이러한 함수를 **익명 함수(anonymous function)**라고 한다. 함수 표현식에서는 익명 함수를 사용하는 것이 일반적이다.

```js
// 익명 함수 표현식(anonymous function)
let print = function (a, b) {
  return a + b;
};
// 기명 함수 표현식(named function)
let value = function sum(a, b) {
  return a + b;
};
// 출력
console.log(print(10, 20)); // 30
console.log(sum(10, 20)); // Uncaught ReferenceError: sum is not defined
```

함수는 객체이기 때문에 함수를 담고있는 변수는 함수명이 아니라 함수를 가리키는 참조값을 저장하게 된다. 따라서 함수를 호출할 때에는 함수명이 아니라 함수를 가리키는 변수명을 사용하여야 한다.

```js
console.log(value(10, 20)); // 30
```

## 3. 함수 호이스팅

자바스크립트는 ES6의 let, const를 포함하여 모든 선언(var, let, const, function, function\*, class)을 호이스팅한다.
호이스팅은 var 선언문이나 function 선언문 등 모든 선언문이 해당 스코프의 최상단으로 옮겨진 것처럼 동작하는 특성이다.

### 3-1. 함수 선언식

함수 선언식으로 선언한 함수는 호이스팅이 되며 함수 자체가 호이스팅 된다.

```js
sum(10, 20);
function sum(a, b) {
  return a + b;
}
```

위의 코드는 사실상

```js
function sum(a, b) {
  return a + b;
}
sum(10, 20);
```

이 코드와 같은 뜻이다.

### 3-2. 함수 표현식

그렇다면 함수 표현식에서는 어떻게 될까  
함수 표현식의 경우는 함수 호이스팅이 아니라 변수 호이스팅이 발생한다.

```js
let sum;
sum(10, 20);
sum = function (a, b) {
  return a + b;
};
```

실제로는 이렇게 동작한다.

```js
sum(10, 20);
let sum = function (a, b) {
  return a + b;
};
```

따라서 `let` 변수 에 대해서 호이스팅을 하게 되는데, `let`은 `TDZ`의 영향을 받는 구문이다. 따라서 호이스팅은 수행하지만 초기화되기 전에 참조를 해서 `ReferenceError`가 출력된다.  
[참고 : var, let, const 정리](https://velog.io/@tlatjdgh3778/var-let-const%EC%97%90-%EB%8C%80%ED%95%9C-%EC%A0%95%EB%A6%AC-1hu7a9ar)

그럼 `let`이 아니라 `var` 키워드 일때는 어떻게 될까

```js
sum(10, 20);
var sum = function (a, b) {
  return a + b;
};
// Uncaught TypeError: sum is not a function
```

실제로는 이렇게 동작한다.

```js
var sum;
sum(10, 20);
sum = function (a, b) {
  return a + b;
};
```

변수 `sum`을 선언하고 `sum(10,20)`을 호출했는데 `sum`은 이 상태에서는 `undefined`이기 때문에 `sum is not a function`이 뜨는 것이다.

## 4. 함수 표현식의 장점

함수 표현식은 호이스팅의 영향을 받지 않는 특징 이외에 함수 선언식보다 유용하게 쓰이는 경우가 있다.

- 클로저로 사용
- 콜백으로 사용 (다른 함수의 인자로 넘길 수 있음)

### 4-1. 클로저로 사용

**함수 표현식으로 클로저 생성하기**
클로저는 함수를 실행하기 전에 해당 함수에 변수를 넘기고 싶을 때 사용된다. 아래 코드를 보자

```js
function tabsHandler(index) {
  return function tabClickEvent(event) {
    // 바깥 함수인 tabsHandler() 의 index 인자를 여기서 접근할 수 있다.
    console.log(index); // 탭을 클릭할 때 마다 해당 탭의 index 값을 표시
  };
}

var tabs = document.querySelectorAll(".tab");
var i;

for (i = 0; i < tabs.length; i++) {
  tabs[i].onclick = tabsHandler(i);
}
```

이 코드는 모든 `.tab` 요소에 클릭 이벤트를 추가하는 코드이다. 클로저를 이용해 `tabClickEvent()` 에서 바깥 함수 `tabsHandler()` 의 인자 값 `index` 를 접근했다는 점에 주목해보자.

`for` 반목문의 실행이 끝난 후, 사용자가 `tab` 을 클릭했을 때 `tabClickEvent()` 가 실행이 된다. 만약 클로저를 사용하지 않았다면 모든 `tab` 의 `index` 값이 `tabs.length` 값이 되었을 것이다.

```js
for (i = 0; i < tabs.length; i++) {
  tabs[i].onclick = tabsHandler(i);
}
```

클로저를 사용하지 않은 예제를 보자.

```js
var tabs = document.querySelectorAll(".tab");
var i;

for (i = 0; i < tabs.length; i++) {
  tabs[i].onclick = function (event) {
    console.log(i); // 어느 탭을 클릭해도 항상 tabs.length (i 의 최종 값) 이 출력
  };
}
```

이 코드에서는 어느 탭을 클릭해도 `i` 는 `for` 반목문의 최종 값인 `tabs.length` 가 찍히게 된다.


<!-- <iframe src="https://codesandbox.io/embed/charming-nobel-gwnfc?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="charming-nobel-gwnfc"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>-->

보기 쉽도록 `for` 문 안의 `function()` 을 밖으로 꺼내서 선언해보면

```js
var tabs = document.querySelectorAll(".tab");
var i;
var logIndex = function (event) {
  console.log(i); // 3
};

for (i = 0; i < tabs.length; i += 1) {
  tabs[i].onclick = logIndex;
}
```

`logIndex` 가 실행되는 시점은 이미 `for` 문의 실행이 모두 끝난 시점이기 때문에 어느 탭을 눌러도 `for` 문의 최종 값인 `tabs.length` 가 찍힌다.

이 문제점을 해결하기 위해서 클로저를 적용한 코드를 보면

```js
function tabsHandler(index) {
  return function tabClickEvent(event) {
    // 바깥 함수인 tabsHandler 의 index 인자를 여기서 접근할 수 있다.
    console.log(index); // 탭을 클릭할 때 마다 해당 탭의 index 값을 표시
  };
}

var tabs = document.querySelectorAll(".tab");
var i;

for (i = 0; i < tabs.length; i += 1) {
  tabs[i].onclick = tabsHandler(i);
}
```


<!--<iframe src="https://codesandbox.io/embed/fervent-wave-lvzl9?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="fervent-wave-lvzl9"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>-->

`for` 반복문이 수행될 때 `i` 값을 `tabsHandler()` 에 넘기고, 클로저인 `tabClickEvent()` 에서 `tabsHandler()` 의 인자 값 `index` 에 접근할 수 있게 된다. 따라서 우리가 원하는 각 탭의 `index` 에 접근할 수 있는 것이다.

### 4-2. 콜백으로 사용

다른 함수의 인자로 넘길 수 있다.  
함수 표현식은 일반적으로 임시 변수에 저장하여 사용한다.

```js
var value = function () {
  // ...
};
```

함수 표현식을 임시 변수에 넣지 않고도 다음과 같이 콜백 함수로 사용할 수 있다.

```js
$(document).ready(function () {
  console.log("An anonymous function"); // 'An anonymous function'
});
```

위 코드와 아래의 코드는 같은 뜻이다.

```js
var logMessage = function () {
  console.log("An anonymous function");
};

$(document).ready(logMessage); // 'An anonymous function'
```

자바스크립트 내장 API인 `forEach()` 를 사용할 때도 콜백 함수를 사용할 수 있다.

```js
var arr = ["a", "b", "c"];
arr.forEach(function () {
  // ...
});
```

## 5. 참고

<https://poiemaweb.com/js-function/>  
<https://joshua1988.github.io/web-development/javascript/function-expressions-vs-declarations/>
