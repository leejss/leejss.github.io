---
layout: post 
title: React - Context API
---

## context 이용

context를 이용하여 컴포넌트 트리 전체에 데이터를 제공한다.

## createContext

~~~jsx
import {createContext} from 'react' 

const SampleContext = createContext({
    color: 'black'
})
~~~

context객체를 생성한다.

매개변수로 default context 객체를 받는다.

Provider에 의해 value를 전달받지 못한 경우 default value가 쓰인다.

## Context.Provider

`value`를 통해 context객체에 값을 채운다.

~~~jsx

<SampleContext.Provider value={
    {color: 'orange'}}>
    {/*이안의 컴포넌트가 SampleContext에 접근할 때 color의 값은 orange다.*/}
</SampleContext.Provider>

~~~

함수를 전달하여 context 객체의 값을 동적으로 바꿀 수 있다.

~~~jsx
// import 생략

const SampleProvider = ({children}) => {
    // context와 동일한 state정의
    const [color, setColor] = useState('black')
    const value ={
        state: {color},
        actions: {setColor}
    }
    return (
        <SampleContext.Provider value={value}>{children}</SampleContext.Provider>
    )
}
// Provider로 감싸인 컴포넌트는 color를 업데이트하는 setColor에 접근할 수 있다. 
// export 생략
~~~

이제 `<SampleContext.Provider>` 대신 `<SampleProvider>`를 사용하면 된다.

~~~jsx
<SampleProvider>

    {/*context를 이용하는 컴포넌트*/} 

</SampleProvider>
~~~

## Context.Consumer

context 객체의 값에 접근하기 위해 사용한다.

~~~jsx
<SampleContext.Consumer>

    {/* context객체의 값을 가지고 무엇을 할 것인지 정의하는 함수가 들어가야 한다.*/}

</SampleContext.Consumer>

~~~

`Consumer`사이에는 어떻게 context객체를 이용할 것인지 정하는 함수가 들어가야 한다.

~~~jsx
// example
<SampleContext.Consumer>
    {({state}) => (
        <div style={
            {color: state.color}}>
            sample
        </div>
    )}
</SampleContext.Consumer>

~~~

## useContext

함수형 컴포넌트에서 context를 보다 쉽게 사용할 수 있는 hook을 제공한다.

`Consumer`대신 `useContext`를 사용하면 보다 깔끔해진다.

~~~jsx

const {state} = useContext(SampleContext)

// 이 아래부터 context의 state를 사용할 수 있다.
return (
    <div style={
        {color: state.color}
    }>
        sample
    </div>
)

~~~
