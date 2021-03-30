---
layout: post
title: React Design pattern
tags: React
date: 2021-03-30 16:21 +0900
---

## keyword

- reusable component
- HoC

## Children

children prop을 이용하여 컴포넌트를 렌더링한다.

```jsx
const AwesomeBtn = ({children}) => {
    return <button className='awesome'>{children}</button>
}

// 사용
<AwesomeBtn>Click me</AwesomeBtn>
```

## Container and Presentational Pattern

리엑트 컴포넌트는 logic과 presentation을 함께 내포할 수 있는데,

두 요소를 분리함으로써 재사용가능한 컴포넌트를 만들 수 있다.

container 컴포넌트는 데이터를 다루는 컴포넌트이고 presentation 컴포넌트는 UI를 다루는 컴포넌트이다.

### Example

```jsx
// presentation
const User = ({name, email}) => {
    return (
        <>
        <h1>User Profile</h1>
        <div>name: {name}</div>
        <div>email: {email}</div>
        <>
    )
}
```

```jsx
// container
const UserContainer = () => {
    const [user, setUser] = userState({
        name: "",
        email: ""
    })

    useEffect(async () => {
        // fetch user data by API call
        const data = read(userId)
        setUser(data)
    })
    // Render presentation component
    return <User {...user} />
}
```

## Higher order components (HoC)

Higher order component는 Higher order function의 개념에 기초한다.

Higher order function은 function을 인자로 받아서 새로운 동작을 추가한 뒤 새로운 함수를 반환하는 함수다.

이와 비슷하게 HoC는 컴포넌트를 인자로 받아서 새로운 기능을 추가한 뒤 새로운 컴포넌트를 반환하는 함수다.

```jsx
const HoC = Component  => EnhancedComponent;
```

기존의 컴포넌트를 바꾸는 것이 아니라 기능이 추가된 새로운 컴포넌트를 반환하는 것이다.

```jsx
const withClass = Component => props => (
    <Component {...props} className="my-class" />
)
```

## FunctionAsChild

child를 전달할 때 컴포넌트 형태로 전달하는 것이 아니라 함수 형태로 전달한다.

```jsx
// children으로 함수가 전달된다.
const MyComponent = ({children}) => children()

// 사용
<MyComponent>
    {() => <div>Hello</div>}
</MyComponet>
```

Children으로 함수를 전달하여 얻을 수 있는 이점은 런타임에 변수를 직접 컴포넌트에 전달할 수 있다는 점이다.

```jsx

const Hello = ({children}) => children("world")

// 사용

<Hello>
    {(text) => <h1>Hello {text}</h1>}
</Hello>

```
