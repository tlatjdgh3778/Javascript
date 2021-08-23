# 자바스크립트 비동기 처리 방법

## 1. 비동기 처리

비동기 처리란 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 것을 말한다.

Javascript 는 싱글 스레드(Single-Thread) 언어이다. 즉 호출한 함수들이 쌓이는 콜 스택(Call stack)이 하나인 것을 의미하는데, 이 말은 Javascript 는 한 번에 한 가지만 수행할 수 있다는 말이다. 이러한 특징들을 가지고 있는 Javacript 가 어떻게 비동기 처리를 할 수 있을까?

### **Call Stack**

**Javascript Engine** 은 **Memory Heap** 과 **Call Stack** 으로 이루어져 있다.
Memory Heap : JS를 사용해 만든 함수, 배열, 객체 등의 저장 공간  
Call Stack : 함수 실행 호출이 쌓이는 곳. 호출이 끝나면 사라진다.
![](https://joshua1988.github.io/images/posts/web/translation/how-js-works/js-engine-structure.png)

자바스크립트는 콜 스택을 하나만 가지고 있으므로 한 번에 하나의 함수만 처리할 수 있다.

```js
function a() {
  b();
  console.log("a");
}

function b() {
  console.log("b");
}
a();
```

콜 스택에서 어떻게 실행되는지 그림으로 보면 이해가 편할 듯하다.
![Call stack](https://images.velog.io/images/tlatjdgh3778/post/723b4b64-927d-4e6f-a07c-3191dc4e48ab/callsatck.png)

이처럼 자바스크립트 엔진에서는 하나의 작업만 실행 할 수 있지만 자바스크립트는 자바스크립트 엔진으로만 돌아가는 것이 아니다.

### **Runtime**

![Runtime](https://hudi.blog/static/a146f81daa6551ebe0c29e7b361d7653/ca1dc/03.png)

구동 순서  
Call Stack -> Web API -> Callback Queue -> Event Loop 동작 -> Call Stack  
위의 그림처럼 자바스크립트 엔진 밖에서도 자바스크립트 실행에 관여하는 요소들이 존재한다. **Web API**, **Task Queue**, **Event Loop** 가 있다. 각각에 대해 조금 살펴보면

- **Web API**  
  `Web API` 는 브라우저에서 제공되는 `API`이며, `AJAX`나 `Timeout` 등의 비동기 작업을 실행한다. 자바스크립트에서 `setTimeout` 과 같은 함수를 실행하면, 자바스크립트 엔진은 `Web API`에 `setTimeout`을 요청하고 동시에 `setTimeout`에 넣어준 `Callback` 까지 전달한다. `Call stack` 에서는 `Web API` 요청 이후 `setTimeout` 작업이 완료되어 제거된다.  
  `Web API`는 방금 받은 `setTimeout`을 완료하고, 동시에 전달받은 `Callback` 을 `Task Queue`라는 곳에 넘겨준다.

- **Task Queue 와 Event Loop**
  `TaskQueue` 는 `Callback Queue` 라고도 한다. 큐 형태로 `Web API` 에서 넘겨받은 `Callback` 함수를 저장한다. 이 `Callback` 함수들은 자바스크립트 엔진의 `Call stack` 의 모든 작업이 완료되면 순서대로 `Call stack` 에 추가된다. 이 때 `Call stack` 에 실행중인 작업이 존재하는지와 `Task Queue` 에 `Task` 가 존재하는지를 판단하고 `Task Queue` 의 작업을 `Call stack` 에 옮기는 일을 `Event Loop` 가 작업한다.

**동작 순서**
```js
setTimeout(function() {
    console.log("Hello World");
}, 5000);
```
1. 코드가 실행되고 `setTimeout` 함수가 `Call stack` 에는 `setTimeout` 함수가 추가된다.
![](https://hudi.blog/static/41d867cee5a981c47fc9d0121ca2fb4c/ca1dc/04.png)

2. `setTimeout` 함수는 자바스크립트 엔진이 처리하지 않고, `Web API` 가 처리해서 `setTimeout` 에 담긴 콜백 함수를 전달함과 동시에 `setTimeout` 작업을 요청한다.  
![](https://hudi.blog/static/88722528845a704df3fb3da4e2f824dd/ca1dc/05.png)  

3. `Call stack` 에서는 모든 작업이 완료되었으므로 `setTimeout` 작업이 제거된다.
4. `Web API` 는 `setTimeout` 작업이 실행된다. 5000ms 를 기다리고 `Task Queue` 로 콜백 함수를 전달한다.
![](https://hudi.blog/static/ce68d72ce94c26783897686497c2cfd9/ca1dc/06.png)

![](https://hudi.blog/static/67a9ed8ee6baf912491581984d8f292f/ca1dc/07.png)

5. `Event Loop`는 항상 `Call stack`이 비어있는지, `Task Queue` 에 작업이 있는지 검사하고 있는데, 지금은 `Call stack`이 비어있고, `Task Queue` 에 수행할 작업이 추가되어 있다.  
![](https://hudi.blog/static/e8cfcacc1006e64f05f2030e219f6b9c/ca1dc/08.png) 

6. `Task Queue`에서 대기하던 콜백 함수 하나를 `Call stack` 으로 보낸다
![](https://hudi.blog/static/78c07905d18c659fb65fea9d676fc092/ca1dc/09.png)

7. 콜백 함수의 작업도 전부 완료되어 `Pop`되고 프로그램이 종료된다.  
![](https://hudi.blog/static/c30a99d6aaf7b252715bee93062639e1/ca1dc/10.png)  

<http://latentflip.com/loupe> 이 사이트에 방문해서 코드를 실행해보면 어떤식으로 동작하는지 직관적으로 보는데 도움이 많이 될 것이다.

## 2. 참고

* [https://hudi.blog/async-javascript/](https://hudi.blog/async-javascript/)
* https://velog.io/@yejinh/Event-Loop-d4k4llote8
