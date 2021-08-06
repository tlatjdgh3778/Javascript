이벤트 버블링과 이벤트 캡처링에 대해서 알아보자.

## 1. 이벤트 버블링(Event Bubbling)

이벤트 버블링이란 한 요소에 이벤트가 발생하면 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작하고 최상단의 부모 요소를 만날 때까지 반복되면서 핸들러가 동작하는 현상을 말한다.

<p align="center"><img src="https://joshua1988.github.io/images/posts/web/javascript/event/event-bubble.png" /></p>

버블링 예제 코드를 보자.

```js
 <body>
    <div class="DIV1">
      DIV1
      <div class="DIV2">
        DIV2
        <div class="DIV3">DIV3</div>
      </div>
    </div>
  </body>
```

```js
const divs = document.querySelectorAll("div");

const clickEvent = (e) => {
  console.log(e.currentTarget.className);
};

divs.forEach((div) => {
  div.addEventListener("click", clickEvent);
});
```

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/701c6e40-8294-4c99-9b2b-2a0e0ad39b5f/image.png" /></p>

div를 클릭하면 해당하는 클래스의 이름이 콘솔로 출력되는 코드이다. 자바스크립트는 기본적으로 버블링이 발생하기 때문에 `<div class="DIV3">DIV3</div>`를 클릭한다면 콘솔에는 DIV3, DIV2, DIV1이 순서대로 출력이 될 것이다.

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/10c06185-a1ac-435b-9f53-7dea1cdf6b60/image.png" /></p>

`<div class="DIV3">DIV3</div>` 를 클릭하면 할당되어 있는 이벤트가 발생하고 다음에는 바깥의 div 태그에 할당된 이벤트가 발생하고 document 객체를 만날 때까지 이벤트가 전파된다.

## 2. 이벤트 캡처링(Event Capturing)
캡처링은 버블링과는 반대로 최상위 태그에서 해당 태그를 찾아 내려간다.

<p align="center"><img src="https://joshua1988.github.io/images/posts/web/javascript/event/event-capture.png" /></p>

캡처링은 잘 사용되지는 않지만 어떻게 코드로 구현할 수 있는지 보자.

```js
const divs = document.querySelectorAll("div");

const clickEvent = (e) => {
  console.log(e.currentTarget.className);
};

divs.forEach((div) => {
  div.addEventListener("click", clickEvent, { capture: true });
});
```

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/701c6e40-8294-4c99-9b2b-2a0e0ad39b5f/image.png" /></p>

`addEventListener` 의 옵션 객체에 `{ capture: true }` 또는 `true` 를 설정해주면 캡처링을 구현할 수 있다.

`<div class="DIV3">DIV3</div>`를 클릭한다면 위에서부터 찾아 내려오기 때문에 콘솔에는 DIV1, DIV2, DIV3이 순서대로 찍힐 것이다.

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/49fe0a56-f915-4188-8634-bc050ee6c1ac/image.png" /></p>

## 3. 이벤트 전파 막기
버블링은 해당 타깃에서 document 객체를 만날 때까지 핸들러가 모두 호출되는데 코드를 작성하다보면 원하는 타깃에서만 이벤트를 발생하게 하고 싶을때가 있다.

그럴 때에는 `event.stopPropagation()` 을 사용하면 되는데 버블링의 경우에는 클릭한 타깃의 이벤트만 발생하고 상위 요소로 이벤트가 전파되는 것을 막을 수 있다.

버블링에서 작성된 코드에 `event.stopPropagation()` 을 추가하고 실행해보자.
```js
const clickEvent = (e) => {
  e.stopPropagation();
  console.log(e.currentTarget.className);
};
```

아까는 DIV3을 클릭했을 때 이벤트가 전파되어 DIV3, DIV2, DIV1 이 출력이 됐지만 이번에는 클릭한 타깃의 이벤트만 발생하는 것을 알 수 있다.

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/5d6023bb-86e4-45bd-9829-caeb4448e416/image.png" /></p>

## 4. 참고
* [캡틴판교 - 버블링과 캡처링](https://joshua1988.github.io/web-development/javascript/event-propagation-delegation/)
* [javascript.info - 버블링과 캡처링](https://ko.javascript.info/bubbling-and-capturing)
