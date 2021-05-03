---
layout: post 
title: Redux Essentials, Part 2
tags: Redux
categories: React
---

## Create Basic Counter React + Redux app

```shell
npx create-react-app basic-counter --template redux
```

## App structure

- /src
  - index.js : starting point of App
  - App.js: the top-level React component
  - /app
    - store.js: Redux store instance
  - /features
    - /counter
      - Coutner.js: React component that shows the UI for the counter feature
      - coutnerSlice.js: the Redux logic for the counter feature

## Creating the Redux Store

Redux toolkit의 `configureStore()`를 이용하여 store 생성

```jsx
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

인자로 reducer를 받음.

앱 내, 다양한 reducers를 객체에 담아 전달한다.

reduer 객체의 key 이름은 state객체 내 key 이름으로 설정된다.

```jsx
export default confiugreStore({
    reducer: {
        coutner: counterReducer, 
        todo: todoReducer,
        // ...
    }
})

/*
    state.counter,
    state.todo 
    로 설정된다.

*/
```

`counterReducer`가 `state.counter`의 업데이트를 담당하게 된다.

## Redux slices

>> A "slice" is a collection of Redux reducer logic and actions for a single feature in your app,

reducer와 action을 정의한 object

store는 여러 개의 'slices' of state로 이루어져 있다.

```jsx
export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
```

`users`, `posts`, `comments`는 각각 Redux store의 slice다.

slice에 대응하는 reducer를 slice reducer라 한다.

### combineReducer()

`store`는 하나의 `rootReduer`가 필요하다.

각각의 slice reducers를 하나의 rootReducer에 담는 과정이 필요하다.

```jsx
const rootReducer = combineReducers({
  users: usersReducer, // slice: sliceReducer
  posts: postsReducer,
  comments: commentsReducer
})

const store = configureStore({
    reducer: rootReducer
})
```

`combineReducer()`를 이용하여 rootReducer를 생성한다.

### configureStore()

`configureStore()`에 slice reducer를 각각 넘겨도 내부에서 combine해준다.

따라서 코드를 줄일 수 있다.

```jsx

const store = configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})

```

## Creating Slice Reducers and Actions

각각의 slice를 담당하는 모듈을 만든다.

```shell
postSlice.js
useSlice.js
commentSlice.js
...
```

### Create slice

Redux toolkit의 `createSlice()`함수를 이용하여 slice를 생성한다.

```jsx
export const counterSlice = createSlice({
  name: 'counter', // slice name
  initialState: { // 초기값
    value: 0
  },
  // slice reducers
  reducers: {
    increment: state => {
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})
```

`createSlice()`에 slice정보를 담은 객체를 전달하면 action, action creator, reducer를 생성한다.

```jsx
// action
{
    type: "counter/increment",
},
{
    type: "counter/decrement",
},
{
    type: "counter/incrementByAmount",
    paylaod: amount
}
// action creator
increment() // reutrn {type: "counter/increment"}
decrement() // return {type: "counter/decrement"}
incrementByAmount(amount) // return {type: "counter/incrementByAmount", paylaod: amount}

// reducer
const counterReducer = (state={value: 0}, action) => {
    //...
}
```

`createSlice()`에서 reducer를 작성할 때 내부에서 불변성을 유지하기 때문에 직접 state를 바꾸는 코딩이 가능하다.

>> createSlice uses a library called Immer inside.

## useSelector and useDispatch

state 안 값을 가져오기 위해 `useSelector()`를 이용한다.

>> the useSelector hook lets our component extract whatever pieces of data it needs from the Redux store state.

```jsx
// Counter.js

const count = useSelector(state=> state.counter.value)
```

store에 dispatch하기 위해 `useDispatch()`를 이용한다.

```jsx
const dispatch = useDispatch()
```

## 참고

[Redux Essential](https://ko.redux.js.org/tutorials/essentials/part-2-app-structure)