---
layout: post 
title: Redux Essentials, Part 1
tags: Redux
category: React
---

## What is Redux?

Redux: action 이벤트를 이용하여 애플리케이션 state(global state)를 업데이트하고 관리하는 라이브러리

## Redux와 함께 쓰이는 라이브러리

- react-redux
- redux-toolkit
- Redux DevTools Extension

## Concept

Redux store안에 global state를 보관하고 action을 통해서 state를 업데이트 한다.

one-way data flow: action -> state -> view

다수의 컴포넌트가 동일한 state를 공유해야 하는 경우, Redux를 이용한다.

## Immutability

Redux에서 일어나는 state의 업데이트는 기존 state를 바꾸지 않고 기존 state를 copy한 후 copy한 state를 업데이트하여 반환해야 한다.

>> Redux expects that all state updates are done immutably

## Terminology

### Action

`type` 필드을 가지고 있는 객체

어떤 action인지 알려주는 객체다.

```jsx
//example
{
    type: "counter/INCREMENT"
}

{
    type: "counter/INCREMENTBYAMOUNT",
    payload: 10 // extra information
}

```

### Action creator

action 객체를 반환하는 함수

```jsx

const increment = () => ({
    type: "counter/INCREMENT"
})

// dispatch(increment())

const incrementByAmount = (amount) => ({
    type: "counter/INCREMENTBYAMOUNT",
    payload: amount
})

// dispatch(incrementByAmount(10))

```

### Reducers

기존의 state와 action 객체를 받아서 새로운 state를 반환하는 함수

```jsx

const counterReducer(state = {value: 0}, action) {
    switch (action.type) {
        case "counter/INCREMENT":
            return {
                ...state, // rest
                value: state.value + 1
            }
        case "counter/INCREMENTBYAMOUNT":
            return {
                ...state, 
                value: state.value + action.payload
            }
        default:
            return state
    }
}

```

### store

Redux application state를 보관하는 객체

```jsx
// store 생성
import { configureStore } from '@reduxjs/toolkit'
const store = configureStore({reducer: counterReducer})

// store 가져오기
store.getStore()

```

### dispatch

store의 method

state의 update를 담당하는 함수. action creator와 함께 쓰인다.

```jsx
// store.dispatch(action객체)

store.dispatch(increment())

```

### selector

state객체에서 특정 값을 가져오는 함수

```jsx

const selectCounterValue = state => state.value

```
