---
layout: post
title: redux-saga를 이용한 Counter 만들기
date: 2021-05-24 18:10 +0900
tags: React
categories:
    - React
---

## Goal

redux와 redux-saga를 이용하여 간단한 Counter 웹을 만들어 봄으로써 redux-saga에 대한 이해를 높인다.

## redux-saga는 무엇이고 언제 사용할까?

[redux-saga](https://redux-saga.js.org/){:target="_blank"}
[redux-saga repo](https://github.com/redux-saga/redux-saga){:target="_blank"}

- redux-saga는 redux 미들웨어 중 하나. (redux와 함께 쓰인다.)
- 리액트가 API 요청과 같은 side effect를 효율적으로 관리할 수 있도록 도와주는 라이브러리.
- ES6 feature인 Generator를 사용

>The mental model is that a saga is like a separate thread in your application that's **solely responsible for side effects.**

## Counter 만들기

### reducer 먼저 만들기

- 액션을 디스패치 했을 때, 액션 타입에 따라 state를 업데이트 하는 함수

```js
// reducer.js
export function reducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
}
```

### redux와 redux-saga를 프로젝트에 셋업하기

- 먼저 라이브러리를 설치한다.

```shell
yarn add redux redux-saga
```

- `sagaMiddleware`와 `store`를 생성한다.

```js
// index.js
// ...
import createSagaMiddleware from "redux-saga";
import { applyMiddleware, createStore } from "redux";
import { reducer } from "./reducer";

const sagaMiddleware = createSagaMiddleware()
const store = createStore(reducer, applyMiddleware(sagaMiddleware))

// applyMiddleware 이후 run()을 해야 한다.
sagaMiddleware.run(rootSaga);

const rootElement = document.getElementById("root");
function render() {
  ReactDOM.render(
    <App />,
    rootElement
  );
}

render();
// store의 상태가 변화할 때마다 render를 호출한다.
store.subscribe(render);
```

### Counter 컴포넌트 작성하기

- 스타일이 없는 간단한 Counter 컴포넌트를 작성한다.

```jsx
// Counter.jsx

const Counter = ({value, onIncrementAsync, onDecrementAsync}) => {

    return (
        <div>
            <h1>{value}</h1>
            <button onClick={onIncrementAsync}>INCREMENT ASYNC</button>
            <button onClick={onDecrementAsync}>DECREMENT ASYNC</button>
        </div>
    )
}

export default Counter

```

### Saga 작성하기

- saga를 작성하기 전에 `effect`에 대해서 먼저 이해해야 한다.
- redux에서 `type` 속성을 가진 자바스크립트 객체를 `action`이라고 부르는 것처럼 `effect`또한 redux-saga에서 명시한 plain javascript object에 불과하다.
- redux-saga가 지원하는 다양한 함수들(구체적으로는 `redux-saga/effect`에서 import하는 함수들)은 Effect creators로서 `effect` 객체를 `yield`한다. (제너레이터 함수이기 때문)
- 미들웨어는 yield된 객체(즉, `effect`)를 전달받아 관련 기능을 수행하는 것이다.

>You can view Effects like instructions to the middleware to perform some operation (e.g., invoke some asynchronous function, dispatch an action to the store, etc.).

- 1초 후 1을 증가시키는 `saga`를 작성한다.
- `decrementAsync`는 생략한다.

```js
// sagas.js
import { put, takeEvery, all } from "redux-saga/effects";

const delay = (ms) => new Promise(res => setTimeout(res, ms))

function* incrementAsync() {
    // 미들웨어에 Promise를 전달하면, 미들웨어는 프로미스가 resolve될 때까지 기다린다.
    yield delay(1000)
    // put(action): action을 store에 디스패치하는 effect를 미들웨어에 전달한다.
    yield put({type: "INCREMENT"})
}
```

- 아직 끝이 아니다. `INCREMENT_ASYNC` 액선이 디스패치 되었을 때, 방금 정의한 `incrementAsync`를 호출할 수 있어야 한다.
- 아래 코드를 `sagas.js`에 추가한다.

```js
// Our watcher Saga: spawn a new incrementAsync task on each INCREMENT_ASYNC
// INCREMENT_ASYNC이 디스패치되었을 때, incrementAsync을 'spawn'한다.
function* watchIncrementAsync() {
  yield takeEvery("INCREMENT_ASYNC", incrementAsync);
}
```

- saga가 하나라면 `watchIncrementAsync`를 export한다음 `run()`의 인자로 넣어주면 된다.
- 하지만 saga가 여러 개라면 rootSagas를 만들어 여러 saga를 등록해야 한다.
- 마지막으로 rootSagas를 만들어 export한다.

```js
export default function* rootSaga() {

    // Creates an Effect description that instructs the middleware to run multiple Effects 
    // in parallel and wait for all of them to complete.
  yield all([
      // 제너레이터 함수를 호출하여 effect를 등록한다.
    watchIncrementAsync()
  ])
}
```

- rootSaga를 등록한다.

```js
// index.js
// ...
import rootSaga from './sagas'
sagaMiddleware.run(rootSaga);
```

### 디스패치 함수를 작성하여 Props로 전달한다

- saga와 관련된 로직은 작성을 완료했다.
- 액션을 디스패치하는 함수를 작성하여 Counter컴포넌트에 props로 전달해야 한다.

```js
// index.js
const incrementAsync = () => {
  store.dispatch({
    type: "INCREMENT_ASYNC"
  })
}

function render() {
  ReactDOM.render(
    <Counter
      value={store.getState()}
      onIncrementAsync={incrementAsync}
    />,
    rootElement
  );
}

render();
store.subscribe(render);

```

[완성된 코드](https://codesandbox.io/s/happy-field-oydsk?file=/src/index.js){:target="_blank"}
