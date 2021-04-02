---
layout: post
title: redux, react-redux 알아보기
date: 2021-04-02 17:54 +0900
tags: Redux
---

## Goal

- redux, react-redux를 react 앱에 적용하는 법을 배운다.
- Flux architecture에 대해 이해한다.

## Reference

해당 포스트는 **daveceddia**의 [redux-tutorial](https://daveceddia.com/redux-tutorial/) 포스트를 참고하여 작성했다.

## redux and react-redux

`redux`는 `store`라는, View에서 필요한 데이터를 담은 하나의 global object를 제공한다.

`store`와 `state`라는 단어가 겹쳐서 쓰이는 데, 비유하자면 `store`는 박스고 `state`는 내용물이다.

`redux` 라이브러리는 store와 dispatcher를 제공한다.

컴포넌트에서 `store`에 접근하기 위해서는 `react-redux` 라이브러리가 필요하다.

redux는 react에서만 쓸 수 있는 것이 아니다.

>redux knows nothing about React at all. - Dave Ceddia

`redux`를 react에서 쓰기 위해서는 react-```redux`가 필요하다.

## React App에 redux 추가하기

### store 생성하기

`redux`의 `store`을 생성하는 법은 간단하다.

`redux`의 `createStore()`을 호출하면 된다.

```jsx
import {createStore} from '`redux`'

const store = createStore()

```

위 코드를 작성하면 에러가 발생한다. 왜냐하면 createStore는 reducer function을 반드시 인자로 가져야 한다.

### reducer?

reducer는 기본적으로 아래와 같은 모습을 가진다.

```jsx
function reducer(state={}, action) {
    // what happens here?
    return newState
}
```

reducer는 기본적으로 함수다.

state와 action을 인자로 받아서 action에 따라서 새로운 state를 리턴한다.

reducer가 reducer라는 이름을 가진 이유는 Array의 `reduce()` 메소드가 인자로 받은 함수(reducer)와 같은 행동을 하기 때문이다.

### initial state

주의해야 할 점은 reducer에 전달하는 state는 undefined를 가져서는 안된다. 따라서 ES6의 defualt argument syntax를 이용하여 initialState를 부여해야 한다.

```jsx
const initialState = {
    // inital state
}

function reducer = (state =initialState, action) {
    // after state change
    return  newState
}

```

## state값 바꾸기 - dispatch actions to store

state를 바꾸는 유일한 방법은 store에 dispatch하는 것이다.

### Redux actions

action은 type 프로퍼티를 가지고 있는 plain object를 의미한다.

type 프로퍼티를 가진 객체라면 액션이 될 자격이 있다.

type의 내용에 따라서 state에 실제로 어떤 행동을 하는지 달라진다.

따라서, `dispatch(action)`는 store에 어떤 type에 기술된 행동을 이행하는 명령을 내리는 것과 같다.

### dispatch

`createStore()`를 통해 만든 store는 `dispatch()` 메소드를 제공한다.

```jsx
const store = createStore(reducer)
store.dispatch({type: "INCREMENT"})
store.dispatch({type: "DECREMENT"})
```

## Handle Actions

`dispatch(action)`를 호출할 때마다 store에 등록한 reducer를 호출한다.

reducer내 에서 action을 handle하는 로직을 추가해야 한다.

type에 따라서 다른 로직을 수행해야 하기 때문에 분기를 추가한다. `if`나 `switch`를 통해 분기할 수 있는데 `switch`가 깔끔하기 때문에 `switch`를 통해 분기한다.

```jsx

function (state = initialState, action) {
    switch (action.type) {
        case "INCREMENT": 
            return {
                count: state.count +1
            }
        case "DECREMENT":
            return {
                count: state.count - 1
            }
        default: 
            return state
    }
}

```

이제 actiont type에 따라서 state가 변한다.

## React에서 Redux사용하기 - react-redux 사용

React 컴포넌트에서 store를 사용하기 위해서 react-redux 라이브러리를 사용한다.

```jsx
import {Provider} from 'react-redux'

const App = () => (
  <Provider store={store}>
    <Counter/>
  </Provider>

```

컴포넌트 전체를 Provider로 감싼다음 store를 제공한다.

이제 컴포넌트는 store에 **연결할 수** 있다.

## How to connect to Store

컴포넌트 단에서 store에 접근하기 위해서 react-redux 라이브러리의 `connect()`를 이용해야 한다.

```jsx
import {connect} from 'react-redux'
const Counter = ({count}) => {

    return <h1>{count}</h1>
}

function mapStateToProps (state) {
    return {
        count: state.count
    }
}

export defualt connect(mapStateToProps)(Counter)
```

`connect()`를 통해 store와 연결되었다면, 값을 가져와서 컴포넌트에 props로 전달할 수 있다.

`mapStateToProps`가 그 역할을 하고 있다.

## 컴포넌트에서 액션 디스패치하기

`connect()`는 컴포넌트에 `dispatch` props로 제공한다.

props에서 dispatch를 받아서 액션을 디스패치할 수 있다.

```jsx

import {connect} from 'react-redux'
const Counter = ({count, dispatch}) => {

    const handleIncrement = () => {
        dispatch({type: "INCREMENT"})
    }

    const handleDecrement = () => {
        dispatch({type: "DECREMENT"})
    }
    

    return (    
        <div>
            <h1>{count}</h1>
            <button onClick={handleIncrement}>Increase</button>
            <button onClick={handleDecrement}>Decrease</button>            
        </div>
    )
}

function mapStateToProps (state) {
    return {
        count: state.count
    }
}

export defualt connect(mapStateToProps)(Counter)

```

## Summary

- action type에 따라서 state를 달리하는 reducer를 정의한다.
- redux의 `createStore(reducer)`를 이용하여 store를 생성했다.
- react-redux의 Provider를 이용하여 컴포넌트에서 store를 연결할 준비를 한다.
- 컴포넌트에서 react-redux의 connect를 이용하여 store와 연결한다.
