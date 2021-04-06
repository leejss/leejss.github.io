---
layout: post
title: thunk에 대해서 알아보자
date: 2021-04-06 17:46 +0900
tags: thunk
---

## thunk?

>A thunk is a function that wraps an expression to delay its evaluation.

다음 코드를 보자

```javascript
function wrapper_function(param) {
  // this one is a "thunk" because it defers work for later:
  return function thunk() {   // it can be named, or anonymous
    console.log('do stuff with param now');
  };
}

// Arrow function으로 작성한 모습
const wrapper = (param) => () => {
    // do here
}
```

어떤 함수가 함수를 반환할 때, 반환된 함수를 thunk라고 한다.

즉, thunk는 기본적으로 함수를 말한다.

그렇다면 왜 이런 형태의 함수를 사용하는 것일까?

```jsx

function greeting(name) {
    msg = `Hello ${name}!`
    return msg
}

const message = greeting("James") 
console.log(message) // Hello James!
```

위와 같은 함수는 호출 이후 바로 연산한다.

연산의 결과를 사용하고 싶은 경우, 위와 같이 `speak()`를 바로 호출하면 된다.

연산의 결과를 나중에 사용하고 싶을 때, thunk를 작성하면 된다.

```jsx
function greeting(name) {
    msg = `Hello ${name}!`
    return msg
}

function thunkedGreeting(name) {
    return function () {
        greeting(name)
    }
}

// Arrow version
const thunkedGreeting = name => () => {
    return greeting(name)
}

```

연산작업을 뒤로 미룬다는 것이 어떤 것인지 알아보자.

```jsx
const thunk = thunkedGreeting('James') // `Hello James!`를 리턴하는 함수를 리턴한다.
console.log(typeof thunk); // function

thunk() // `Hello James!`

/*
    실제 연산은 thunkedGreeting('James')을 호출할 때가 아니라,
    thunk()를 호출할 때 발생한다.
*/

```

연산이 들어있는 함수를 wrapper함수로 감싸고 wrapper함수를 호출하면

연산 결과를 바로 리턴하는 것이 아니라 thunk함수를 리턴하게되고 이후 thunk를 호출할 때 연산을 한다.

이런 식으로 연산작업을 미룰 수 있다.
