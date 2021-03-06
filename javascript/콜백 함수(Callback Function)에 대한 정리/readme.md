# 콜백 함수(Callback Function)에 대한 정리

자바스크립트에서 비동기 처리를 하기 위한 방법인 콜백 함수(Callback Function)에 대하여 알아보자.

## 1. 비동기 처리

비동기 처리란 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 것을 말한다.

## 2. 비동기 처리의 첫 번째 사례

```js
function getData() {
  var tableData;
  $.get("https://domain.com/products/1", function (response) {
    tableData = response;
  });
  return tableData;
}

console.log(getData()); // undefined
```

`$.get()` 부분이 ajax 통신을 하는 부분인데, `https://domain.com` 에다가 요청을 날려 1번 상품 정보를 요청하는 코드이다.

그렇게 받아온 데이터는 `response` 인자에 담긴다. 그리고 `tableData = response;` 코드로 받아온 데이터를 `tableData` 변수에 저장한다. 그 다음 `console.log(getData())` 를 실행하면 받아온 데이터가 출력이 되어야 하지만 `undefined` 가 출력되는 것을 볼 수 있다.

그 이유는 `$.get()` 로 데이터를 요청하고 받아올 때까지 기다려주지 않고 다음 코드인 `tableData = response` 가 실행되기 때문에 `undefined` 가 출력되는 것이다.

이러한 문제점이 있음에도 비동기 처리가 필요한 이유는 화면에서 서버로 데이터를 요청했을 때 서버가 언제 그 요청에 대한 응답을 줄지도 모르는데 계속 기다릴 수는 없기 때문이다.

## 3. 비동기 처리의 두 번째 사례

두 번째 사례로는 `setTimeout()` 이 있다. 코드를 바로 실행하지 않고 지정한 시간만큼 기다렸다가 코드를 실행한다.

```js
console.log("Hello");
setTimeout(function () {
  console.log("Bye");
}, 3000);
console.log("Hello Again");
```

결과가 어떻게 나올까 ?  
Hello -> 3초 후 Bye -> Hello Again 순서로 출력될 것 같지만  
Hello -> Hello Again -> 3초 후 Bye 순서로 출력이 된다. 그 이유는 무엇일까

`setTimeout()` 은 콜백 함수를 인자로 받고 비동기 방식으로 실행된다. 따라서 `Hello` 를 출력 후 3초를 기다렸다가 `Bye` 를 출력하는 것이 아니라 `Hello` 를 출력 후 일단 `setTimeout()` 실행하고 `Hello Again` 까지 출력하고 3초 후 `Bye` 를 출력한다.  
([비동기 처리방식](https://velog.io/@tlatjdgh3778/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC-%EB%B0%A9%EB%B2%95) 참고)

## 4. **콜백 함수**로 비동기 처리 방식 문제점 해결

첫 번째 사례에서 비동기 처리 방식으로 인해 생기는 문제를 봤는데 이를 해결할 수 있는 것이 바로 **콜백 함수**를 이용하는 것이다.  
콜백 함수는 자바스크립트의 비동기 처리 방식의 문제점을 해결해주기 위해 특정 시점에서 호출이 되도록 사용하는 함수이다.

```js
function getData(callbackFunc) {
  $.get("https://domain.com/products/1", function (response) {
    callbackFunc(response); // 서버에서 받은 데이터 response를 callbackFunc() 함수에 넘겨줌
  });
}

getData(function (tableData) {
  console.log(tableData); // $.get()의 response 값이 tableData에 전달됨
});
```

이렇게 콜백 함수를 사용하면 특정 로직이 끝났을 때 원하는 동작을 실행시킬 수 있다.

## 5. 콜백 지옥(Callback hell)

콜백 함수를 익명함수로 전달 하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상을 말한다.
**예시**

```js
setTimeout(
  function (name) {
    let coffeeList = name;
    console.log(name);

    setTimeout(
      function (name) {
        coffeeList += ", " + name;
        console.log(name);

        setTimeout(
          function (name) {
            coffeeList += ", " + name;
            console.log(name);

            setTimeout(
              function (name) {
                coffeeList += ", " + name;
                console.log(name);
              },
              500,
              "카페라떼"
            );
          },
          500,
          "카페모카"
        );
      },
      500,
      "아메리카노"
    );
  },
  500,
  "에스프레소"
);
// 에스프레소
// 에스프레소, 아메리카노
// 에스프레소, 아메리카노, 카페모카
// 에스프레소, 아메리카노, 카페모카, 카페라떼
```

이 코드는 들여쓰기 수준이 과도하게 깊어지고 값이 아래에서 위로 전달되어 가독성이 떨어진다.

이러한 콜백 지옥을 해결하기 위한 여러가지 방법들이 있는데 기명 함수로 표현하는 방법, ES6에서 도입된 `Promise` 와 ES8에서 도입된 `async/await` 이 있다. 여기에서는 기명 함수로 표현하는 방법에 대해서만 알아본다.

**기명 함수로 콜백 지옥 해결하기**

```js
let coffeeList = "";

const addEspresso = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, "아메리카노");
};

const addAmericano = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
  setTimeout(addMocha, 500, "카페모카");
};

const addMocha = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
  setTimeout(addLatte, 500, "카페라떼");
};

const addLatte = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
};

setTimeout(addEspresso, 500, "에스프레소");
// 에스프레소
// 에스프레소, 아메리카노
// 에스프레소, 아메리카노, 카페모카
// 에스프레소, 아메리카노, 카페모카, 카페라떼
```

익명의 콜백 함수를 기명 함수로 변경하면 가독성도 좋아지고 들여쓰기도 없어진다. 하지만 커피가 만약에 100 종류가 있다면 100개의 함수를 만드는 것은 부담스럽다.

## 6. 참고

<https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/#%EC%BD%9C%EB%B0%B1-%EC%A7%80%EC%98%A5-callback-hell>  
<https://velog.io/@yujo/JS%EC%BD%9C%EB%B0%B1-%EC%A7%80%EC%98%A5%EA%B3%BC-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%A0%9C%EC%96%B4>

나중에 **promise** 와 **async / await** 도 따로 정리해야겠다.
