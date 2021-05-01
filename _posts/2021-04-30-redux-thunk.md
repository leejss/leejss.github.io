---
layout: post
title: redux-thunk에 대해 알아보자
date: 2021-04-30 16:46 +0900
tags: Redux redux-thunk
---

[Redux Thunk](https://github.com/reduxjs/redux-thunk){:taget="_blank"}

## thunk란

evaluation을 delay시키기 위한 함수를 말한다.

## redux thunk란

delay되는 `dispatch()`를 사용하기 위한 미들웨어 
주로 async 요청을 할 때 사용한다.

## middlware 등록

```js
import thunk from 'redux-thunk'
const store = createStore(rootReducer, applymiddleware(thunk))
```

미들웨어를 등록하면 다음과 같이 dispatch가 가능해진다.

```js

store.dispatch({type: "todo/ADD", text: "sync ADD"}) // 원래 dispatch 방식

store.dispatch(function (dispatch) {
    dispatch({type: "loading/START"})
    setTimeout(() => {
        dispatch({type: "tood/ADD", text: "async ADD"})
    }, 5000) // async call
    dispatch({type: "loading/FINISH"})
}) // thunk 방식

// redux-thunk를 미들웨어로 등록하면 dispatch()는 함수를 인자로 받고,
// 이 함수는 dispatch를 인자로 받는다.

```

## thunk action creator함수 작성

액션 생성 함수는 보통 다음과 같이 작성한다.

```js

const increment = () => ({
    type: "INCREMENT"
})

```

비동기 요청을 사용한다면 `redux-thunk` 미들웨어가 사용할 수 있게 다음과 같이 액션생성함수를 작성한다.

```js

const incrementAsync = () => {
    return (dispatch) => {
        setTimeout(() => {
            dispatch(increment())
        }, 5000)
    }
}

```

작성한 함수를 connect된 컴포넌트에서 다음과 같이 dispatch한다.

```js

const onClick = () => {
    dispatch(incrementAsync())
}

```

## 요약

비동기요청으로 받아온 결과를 store에 dispatch하기 위해 사용
