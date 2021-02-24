# 값으로서의 함수에 대한 정리

## 1. 값으로서의 함수
Javascript 에서는 함수도 객체다. 즉 함수도 일종의 값이라는 말이다.
```js
function a() {}
```
이 코드와 아래의 코드는 같은 뜻이다.
```js
var a = function() {}
```
아래의 코드가 가능한 이유는 함수가 값이기 때문이다.

**함수는 값이기 때문에 다른 함수의 인자로 전달 될수도 있다.**  
다음 코드를 보자
```js
function cal(func, num){
    return func(num)
}
function increase(num){
    return num+1
}
function decrease(num){
    return num-1
}
console.log(cal(increase, 1)); // 2
console.log(cal(decrease, 1)); // 0
```
`console.log(cal(increase, 1));` 을 실행하면 함수 `increase` 와 값 `1` 이 함수 `cal` 의 인자로 전달된다. 함수 `cal` 은 첫 번째 인자로 전달된 `increase` 를 실행하는데 이 때 두 번째 인자의 값으로 `1` 을 인자로 전달한다. 함수 `increase` 는 계산된 결과를 리턴하고 `cal` 은 다시 그 값을 리턴한다.

**함수는 함수의 리턴 값으로도 사용할 수 있다.**
```js
function cal(mode){
    var funcs = {
        'plus' : function(left, right){return left + right;},
        'minus' : function(left, right){return left - right;}
    }
    return funcs[mode];
}
console.log(cal('plus')(2,1)); // 3
console.log(cal('minus')(2,1)); // 1 
```
`return funcs[mode]` 는 `function(left, right) {return left + right;}` 를 리턴해주는 역할을 한다.

**배열의 값으로도 함수를 사용할 수 있다.**
```js
var process = [
    function(input){ return input + 10;},
    function(input){ return input * input;},
    function(input){ return input / 2;}
];
var input = 1;
for(var i = 0; i < process.length; i++){
    input = process[i](input);
}
console.log(input); // 60.5
```
함수는 값이기 때문에 값을 저장하는 컨테이너인 배열에도 저장할 수 있는 것이다.  
변수, 매개변수, 리턴값으로 사용될 수 있는 데이터를 **first-class citizen, first-class object, 일급 객체** 라고 한다. Javascript 에서 함수가 여기에 해당된다.

## 2. 참고
<https://opentutorials.org/module/532/6508>
