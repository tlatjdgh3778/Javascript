# 즉시 실행 함수 (IIFE, Immediately Invoked Function Expression)

## 1. IIFE

즉시 실행 함수 정의되자마자 즉시 실행되는 Javascript Function 을 말한다.
`IIFE` 의 기본 형태는 아래 코드와 같다.

```js
(function () {
  // 실행 문장
})();
```

크게 두 부분으로 나눌 수 있는데, 첫 번째는 괄호(`()`)로 둘러싸인 익명함수이다. 이 부분은 전역 스코프에 불필요한 변수를 추가해서 오염시키는 것을 방지할 수 있을 뿐 아니라 `IIFE` 내부안으로 다른 변수들이 접근하는 것을 막아준다. 두 번째 부분은 즉시 실행 함수 생성하는 괄호 `()` 이다. 이를 통해 자바스크립트 엔진은 함수를 즉시 해석해서 실행한다.

## 2. 예제

### **`private` 한 변수를 만들 수 있다.**

```js
(function () {
  var i = "hello world";
})();
console.log(i);
// Uncaught ReferenceError: i is not defined
```

`IIFE` 내부에서 정의된 변수는 외부 범위에서 접근이 불가능하다.

### **변수에 즉시 실행 함수를 저장할 수 있다.**

변수에 즉시 실행 함수의 리턴 값 저장도 가능하다.

```js
var mySquare = (function (x) {
  return x * x;
})(2);
console.log(mySquare); 
// 4
```

그리고 즉시 실행 함수도 함수이기 때문에 변수에 즉시 실행 함수를 저장할 수 있다.

```js
(mySquare = function (x) {
  console.log(x * x);
})(2);
mySquare(3);
// 4
// 9
```

함수를 `mySquare` 에 저장하고 이 함수를 바로 실행하게 된다. `mySquare` 는 즉시 실행 함수 저장하고 있기 때문에 재호출이 가능하다.

두 가지 예제는 형태가 비슷하지만 괄호의 위치에 따라 기능이 다르므로 괄호의 위치에 주의할 필요가 있을 것 같다.

## 3. 즉시 실행 함수를 사용하는 이유
### 초기화
즉시 실행 함수는 한 번의 실행만 필요로 하는 초기화 코드 부분에 많이 사용된다.
그 이유는 **변수를 전역으로 선언하는 것을 피하기 위해서** 이다. 전역에 변수를 추가하지 않아도 되기 때문에 코드 충돌 없이 구현할 수 있어서 플러그인이나 라이브러리 등을 만들 때 많이 사용된다.
```js
var initText;
(function (number) {
    var textList = ["is Odd Text", "is Even Text"];
    if (number % 2 == 0) {
        initText = textList[1];
    } else {
        initText = textList[0];
    }
})(5);
console.log(initText);
console.log(textList);
// is Odd Text
// Uncaught ReferenceError: textList is not defined
```

전역에 `textList` 가 저장되지 않고, `initText` 만 초기화가 된 것을 확인할 수 있다. 또 `textList` 는 지역 변수로, 전역 변수와 충돌 없이 초기화할 수 있게 된다.

### 라이브러리 전역 변수의 충돌
jQuery나 Prototype 라이브러리는 동일한 `$` 라는 전역 변수를 사용하는데 만약 이 두 개의 라이브러리를 같이 사용한다면 `$` 변수 충돌이 생기게 된다.

```js
(function ($) {
    // $ 는 jQuery object
})(jQuery);
```
즉시 실행 함수 안에서 `$` 는 전역 변수가 아닌 jQuery object의 지역 변수가 되어, Prototype 라이브러리의 `$` 와 충돌 없이 사용할 수 있다.

## 4. 참고
<https://beomy.tistory.com/9>   
<https://developer.mozilla.org/ko/docs/Glossary/IIFE>
