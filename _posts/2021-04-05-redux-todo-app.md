---
layout: post
title: Redux를 이용하여 Todo 로직 만들기
date: 2021-04-05 10:31 +0900
tags: Redux React
---

## 라이브러리

- redux
- react-redux
- redux-actions

## todos 모듈 생성

액션 타입, 액션 생성 함수, 리듀서를 묶은 todos 모듈을 정의한다.

```jsx
// modules/todos.js
import {createAction, handleActions} from 'redux-actions'

// 액션 타입을 정의한다.
const CHANGE_INPUT = "todos/CHANGE_INPUT"
const INSERT = "todos/INSERT"
const TOGGLE = "todos/TOGGLE"
const REMOVE = "todos/REMOVE"

let id = ;

// 액션 생성함수. 컨테이너 컴포넌트에서 connect를 위해 export 한다.
// createAction(actonType, payloadCreator)
export const changeInput = createAction(CHANGE_INPUT, input => input)
export const toggle = createAction(TOGGLE, id=>id)
export const remove = createAction(REMOVE, id=>id)
export const insert = createAction(INSERT, text=> ({
    id: ++id,
    text,
    done: false
})

/*
    createAction으로 생성한 액션 생성함수는 다음과 같이 액션을 반환한다.
    insert("Something")
    {
        type: "todos/INSERT",
        payload: {
            id: 2,
            text: "Something",
            done: false
        }
    }

    createAction없이 액션생성함수를 작성하면 다음과 같다.
    export const insert = (text) => ({
        type: "todo/INSERT",
        payload: {
            id: ++id,
            text,
            done: false
        }
    })
*/

// initialState 정의
const initialState = {
    input: '',
    todos: []
}

// 리듀서 작성
// redux-actions의 handleActions를 이용한다.
// handleActions(reducerMap, initialState)
const todos = handleActions({
    [CHANGE_INPUT] : (state, action) => ({
        ...state,
        input: action.payload
    }),
    [INSERT] : (state, action) => ({
        ...state,
        todos: state.todos.concat(action.payload)
    }),
    [TOGGLE] : (state, action) => ({
        ...state,
        todos: state.todos.map(todo => todo.id === action.payload ? {...todo, done: !todo.done} : todo)
    }),
    [REMOVE] : (state, action) => ({
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload)
    })
}, initialState)

/*
    handleActions안에 action.payload가 동일하게 나타나서 payload의 내용을 예측하기 힘들다.
    객체 구조 분해 할당(destructuring assignment)을 이용하여 다음과 같이 작성할 수 있다.

    [INSERT]: (state, {payload: todo}) => ({
        ...state,
        todos: state.todos.concat(todo)
    })

    다음과 같이 작성하면 payload의 내용을 예측하기 쉬워진다.

    handleActions 없이 리듀서를 작성하면 다음과 같다.

    const todos = (state = initialState, action) => {
        switch(action.type) {
            case CHANGE_INPUT:
                return {
                    ...state,
                    input: action.payload
                }
            case INSERT:
                return {
                    ...state,
                    todos: state.todos.concat(action.payload)
                }
            //...
        }
    }
*/


export default todos
```

## 루트리듀서 작성

전체 앱에 리듀서가 하나라면 방금 작성한 리듀서를 `store`에 주입하면 된다.

하지만 리듀서가 늘어나면 루트리듀서로 합쳐서 `store`에 주입해야 한다.

```jsx
// modules/index.js

import {combineReducers} from 'redux'
import {todos} from' './todo'

const rootReducer = combineReducers({
    todos
})

export default rootReducer
/*
    {
        todos: {...}
    }
*/
```

## store 생성

작성한 리듀서를 바탕으로 `store`를 생성한다.

`react-redux`의 `Provider`를 통해서 컴포넌트에서 store에 접근 가능하도록 한다.

```jsx
// index.js
// 다른 import 생략
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import rootReducer from './module'

const store = createStore(rootReducer)

// Provider는 컴포넌트에서 store에 접근할 수 있게한다.
ReactDOM.render( 
    <Provider store={store}>
        <App />
    </Provider>,
document.getElementById('root')
)

```

## Container 컴포넌트 작성

Container 컴포넌트는 리덕스의 store와 연결하여 데이터를 가져와 view에 전달하는 역할을 한다.

store와 연결하기 위해 react-redux의 connect함수를 이용한다.

connect()는 store에서 데이터를 가져와 컴포넌트의 props에 전달한다.

```jsx
// container/TodosContainer.js
import {insert, toggle, remove, changeInput} from '../modules/todo'

const TodosContainer = ({todos, insert, remove, toggle, changeInput, input}) => {

    return (
        <Todos 
            todos={todos} 
            onInsert={insert} 
            onRemove={remove} 
            onToggle={toggle} 
            onChangeInput={changeInput} 
            input={input} 
        />
    )
}
// state 데이터를 props에 전달한다.
const mapStateToProps = (state) => ({
    input: state.todos.input,
    todos: state.todos.todos
})
/*
    객체 구조분해 할당을 이용하여 다음과 같이 작성할 수 있다.
    const mapStateToProps = ({todos}) => ({
        input: todos.input,
        todos: todos.todos
    })
*/

// 액션생성함수를 props에 전달한다.
const mapDispatchToProps = {
    insert,
    toggle,
    remove,
    changeInput
}

/*
    connect()는 인자에 dispatch를 전달하기 때문에 다음과 같이 작성할 수 있다.
    
    const mapDispatchToProps = (dispatch) => ({
        insert: (text) => {dispatch(insert(text))},
        toggle: (id) => {dispatch(toggle(id))},
        remove: (id) => {dispatch(remove(id))},
        changeInput: (input) => {dispatch(changeInput(input))},
    })

    액션생성함수를 객체 담아 전달하게 되면 위의 과정을 생략할 수 있다.

    'mapStateToProps', 'mapDispatchToProps'라는 이름은 정해져있는 것이 아니기 때문에 다음과 같이 작성할 수 있다.

    connect(
        (state) => ({
            input: state.todos.input,
            todos: state.todos.todos
        }),
        {
            insert,
            toggle,
            remove,
            changeInput
        }
    )(TodosContainer)
*/

export default connect(mapStateToProps,mapDispatchToProps)(TodosContainer)
```

## UI 작성

컨테이너 컴포넌트를 작성하고 나면 redux를 이용한 로직작성은 완료하였다.

가져온 데이터를 rendering하는 컴포넌트만 있으면 완성이다.
