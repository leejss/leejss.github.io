---
layout: post 
title: React - Basic Hooks - 1
tags: React
category: React
---

## Hooks

함수형 컴포넌트에서 state, 라이프 사이클 기능 등을 사용할 수 있게 하는 함수

## useState

함수형 컴포넌트에 state를 사용할 수 있게한다.

`useState`는 배열을 반환하는데 배열의 첫번째 요소는 state변수, 두번째 요소는 해당 변수의 setter함수를 반환한다.

반환하는 요소를 직관적으로 사용하기 위해서 destructing 할당 문법을 사용한다.

`useState`의 파라미터는 state변수의 초기값을 받는다.

```jsx
import React, {useState} from 'react'

const MyComponent = () => {
    const [name, setName] = useState('') 
    const [count, setCount] = useState(0)
    return (
        // code here
        // name, count 변수를 사용가능
    )
}
```

클래스형 컴포넌트에서 state는 객체로서, 객체의 key가 state변수로 기능했다면 함수형 컴포넌트에서는 `useState`를 사용하여 여러 state변수를 선언하여 사용한다.

### state값 업데이트하기

클래스형 컴포넌트에서는 `setState`함수를 이용하여 state값을 업데이트했다면 함수형 컴포넌트에서는 `useState`에서 반환한 setter함수를 이용하여 state값을 업데이트한다.

```jsx
// import 생략

const CountComponent = () => {
    const [count, setCount] = useState(0)
    return (
        <div>
            <p>{count}</p>
            <button onClick={()=> {
                setCount(count + 1)
            }}>증가</button>
        </div>
    )
}
```

## useEffect

리엑트에서는 렌더링 과정에서 일어날 수 없는 특정 동작을 side effect 또는 effect라고 한다.

클래스형 컴포넌트의 라이프 사이클 함수인 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`를 하나의 API로 기능을 제공하는 것이 `useEffect`이다.

마운트 이후, 업데이트 이후, 언마운트 이후에 특정 동작을 수행할 때, `useEffect`를 사용한다.

`useEffect` 훅은 컴포넌트 내부에서 호출한다. 컴포넌트 내부에서 호출하기 때문에 state와 props를 사용할 수 있다.

`useEffect`의 첫번째 인자로 함수를 받는데 이 함수가 effect다.

```jsx
// import 생략

const MyComponent = () => {
    useEffect(() => {
        // 마운트 또는 업데이트 이후 실행
    })
    return (
        // code here
    )
}
```

### 오직 마운트 이 후에만 실행

`useEffect`함수의 두번째 인자에 빈 배열을 주면 마운트 이후에만 effect를 실행한다. (초기 렌더링 이 후에만 실행하고 업데이트 시 실행하지 않는다.)

```jsx
useEffect(() => {
    // code here
}, [])
```

### 특정 값이 바뀔 때만 실행

`useEffect`함수 두번째 인자에 특정 변수(state또는 props)를 배열 형태로 준다면 해당 값이 바뀔 때만 effect를 실행한다.

```jsx
useEffect(()=> {
    //code here
}, [변수])
```

### clean-up 함수

언마운트 되기 전, 업데이트 되기 전에 특정 기능을 수행하고 싶은 경우 `useEffect`에 clean-up함수를 정한다. 방법은 `useEffect`함수에 `return` 구문을 이용한다.

```jsx

useEffect(()=> {
    // code here

    return () => {
        // clean up here
    }
})

```
