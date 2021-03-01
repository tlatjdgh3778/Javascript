# 프로미스(Promise)에 대한 정리

자바스크립트는 비동기 처리를 위한 패턴으로 콜백 함수를 사용한다. 하지만 일반적인 콜백 함수 패턴은 콜백 지옥으로 인해서 가독성이 나쁘고 여러 개의 비동기 처리를 한 번에 처리하는 데도 한계가 있다.

ES6에서는 비동기 처리를 위한 프로미스(Promise)를 도입했다.

## 1. 프로미스의 기본 구조

```js
new Promise(executor);
```

실행함수 `executor` 는 `Promise` 생성자가 생성한 객체를 반환하기도 전에 호출되는데 `Promise`가 만들어지는 순간에 `executor` 함수가 실행된다는 말이다.

`Promise` 객체는 `new` 키워드와 생성자를 사용해 만든다. 생성자는 매개변수로 `executor` 라는 콜백 함수를 받는데 이 콜백 함수는 인자로 `resolve` 와 `reject` 함수를 인자로 받는다.

```js
const myFirstPromise = new Promise((resolve, reject) => {
  // do something asynchronous which eventually calls either:
  //
  //   resolve(someValue)        // fulfilled
  // or
  //   reject("failure reason")  // rejected
});
```

`resolve` 함수는 비동기 작업을 성공적으로 완료해 결과를 값으로 반환할 때 호출하고 `reject` 함수는 작업이 실패하여 오류의 원인을 반환할 때 호출하면 된다.

함수에 프로미스 기능을 추가하려면 프로미스를 반환하면 된다.

```js
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
}
```

## 2. 프로미스의 3가지 상태

상태란 프로미스의 처리 과정을 의미한다. `new Promise()` 로 프로미스를 생성하고 종료될 때까지 3가지 상태를 갖는다.

- Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

### Pending(대기)

`new Promise()` 메소드를 호출하면 Pending(대기) 상태가 된다.

```js
new Promise();
```

```js
new Promise(function (resolve, reject) {
  // ...
});
```

### Fulfilled(이행)

콜백 함수의 인자 `resolve` 를 실행하면 Fulfilled(이행) 상태가 된다.

```js
new Promise(function (resolve, reject) {
  resolve();
});
```

이행 상태가 되면 후속 처리 메소드 `then()` 을 이용하여 처리 결과 값을 받을 수 있다.

### Rejected(실패)

`reject` 를 호출하면 Reject(실패) 상태가 된다.

```js
new Promise(function (resolve, reject) {
  reject();
});
```

실패 상태가 되면 실패 처리 결과 값을 후속 처리 메소드 `catch()` 로 받을 수 있다.

![MDN](https://mdn.mozillademos.org/files/8633/promises.png)

[출처 - MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise#content)

## 3. 프로미스의 후속 처리 메소드

Promise 객체의 상태에 따라 후속 처리 메소드를 호출하게 된다.

```js
promise
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.log(error);
  })
  .finally(() => {
    console.log("finally");
  });
```

### `then()`

then 메소드는 두 개의 콜백 함수를 인자로 받는다. 첫 번째 콜백 함수는 Fulfilled(이행, resolve 함수가 호출된 상태) 시 호출되고 두 번째 함수는 Reject(실패, reject 함수가 호출된 상태) 시 호출된다. then 메소드는 _Promise를 반환한다._

```js
.then(onFulfilled, onRejected);
```

```js
function getData() {
  return new Promise(function (resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

// resolve()의 결과 값 data를 resolvedData로 받음
getData().then(function (resolvedData) {
  console.log(resolvedData); // 100
});
```

### `catch()`

예외(비동기 처리 또는 then 메소드에서 발생한 에러)가 발생하면 호출된다. catch 메소드는 Promise를 반환한다.

```js
.catch(onRejected);
```

```js
function getData() {
  return new Promise(function (resolve, reject) {
    reject(new Error("Request is failed"));
  });
}

// reject()의 결과 값 Error를 err에 받음
getData()
  .then()
  .catch(function (err) {
    console.log(err); // Error: Request is failed
  });
```

### `finally()`

Promise 가 처리되면 상태에 상관없이 지정된 콜백 함수가 실행된다.

```js
.finally(onFinally);

.finally(function() {
   // settled (fulfilled or rejected)
});
```

IE에서는 제공하지 않는다.

## 4. 프로미스 체이닝(Promise Chaining)

Promise 객체를 반환한 비동기 함수는 프로미스 후속 처리 메소드인 `then` 이나 `catch` 메소드를 사용할 수 있다. `then` 메소드가 Promise 객체를 반환하도록 하면(기본적으로 Promise 객체 반환) 여러 개의 프로미스를 연결할 수 있다.

```js
function getData() {
  return new Promise({
    // ...
  });
}

// then() 으로 여러 개의 프로미스를 연결한 형식
getData()
  .then(function (data) {
    // ...
  })
  .then(function () {
    // ...
  })
  .then(function () {
    // ...
  });
```

## 5. 프로미스 에러 핸들링(Error Handling)

에러 핸들링에는 2가지 방법이 있다.

- `then()` 의 두 번째 인자로 처리하는 방법

```js
getData().then(handleSuccess, handleError);
```

- `catch()` 를 이용하는 방법

```js
getData().then().catch();
```

`catch` 를 호출하면 내부적으로 `then(undefined, onRejected)` 를 호출한다.

```js
getData().catch(function (err) {
  console.log(err);
});

getData().then(undefined, function (err) {
  console.log(err);
});
```

그래서 `then()` 을 사용하는 것이 좋을까 `catch()` 를 사용하는 것이 좋을까 ?

`then()` 을 사용해도 되긴 하지만 가급적이면 `catch()` 를 사용해서 에러를 처리하는 것이 더 효율적이다.

아래의 코드를 보자.

```js
// then()의 두 번째 인자로는 감지하지 못하는 오류
function getData() {
  return new Promise(function (resolve, reject) {
    resolve("hi");
  });
}

getData().then(
  function (result) {
    console.log(result);
    throw new Error("Error in then()"); // Uncaught (in promise) Error: Error in then()
  },
  function (err) {
    console.log("then error : ", err);
  }
);
```

`resolve()` 메소드를 호출하여 정상적으로 로직을 처리했지만, `then()` 의 첫 번째 콜백 함수 내부에서 오류가 나는 경우 오류를 제대로 잡아내지 못한다.

```js
Uncaught (in promise) Error: Error in then()
```

하지만 `catch()` 로 처리하게 되면 다른 결과가 나온다.

```js
getData()
  .then(function (result) {
    console.log(result); // hi
    throw new Error("Error in then()");
  })
  .catch(function (err) {
    console.log("then error : ", err); // then error :  Error: Error in then()
  });
```

`catch` 메소드를 모든 `then` 메소드를 호출한 이후에 호출하면 비동기 처리에서 발생한 에러(reject 함수가 호출된 상태)뿐만 아니라 `then` 메소드 안에서 발생한 에러까지 모두 캐치할 수 있다.

따라서 가독성도 좋고 더 많은 예외 처리 상황을 위해 `catch()` 로 에러 처리를 해주는 것이 좋다.

## 6. 참고

<https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise#content>
<https://poiemaweb.com/es6-promise>
<https://joshua1988.github.io/web-development/javascript/promise-for-beginners/>
