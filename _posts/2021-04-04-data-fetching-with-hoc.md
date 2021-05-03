---
layout: post
title: Data fetching with HOC
date: 2021-04-04 21:07 +0900
tags: React
categories: React
---

## Data fetching

컴포넌트를 렌더링할 때 로컬 데이터나 API를 통해서 fetching 하여 View에 주입한다.

mount => fetching => view(re-render) 흐름으로 가야하기 때문에

클래스형 컴포넌트의 `componentDidMount()` 메소드나 함수형 컴포넌트의 `useEffect()` 훅 안에서

data fetching을 해야 한다.

### fetching example

```jsx
// 클래스형
class MyComponent extends React.Component {
    constructor() {
        //
    }

    async componentDidMount() {
    // data fetching
    }
}

// 함수형
const MyComponent = () => {

    useEffect(() => {
        async dataFetching() {
            // data fetching
        }
        dataFetching()
    })

    return (
        //
    )
}
```

## HOC

data fetching이 필요한 컴포넌트마다 fetching logic을 담게 되면 재사용가능한 컴포넌트를 만들 수 없게 된다.

따라서 fetching logic을 따로 분리하고 fetching이 필요한 컴포넌트가 있으면 해당 logic을 사용할 수 있게 하는 것이 좋은데

Higher Order Component를 사용하여 이를 수행할 수 있다.

Higher Order Component(HOC)는 컴포넌트를 반환하는 함수를 말한다.

## Creating HOC

HOC의 모습은 다음과 같다.

```jsx
// 클래스형 컴포넌트 반환
function withDataFetching(Component) {
    return class extends React.Component {
        constructor(props) {
            super(props)
            // set state
        }
        // other life cycle methods

        render() {
            <Component />
        }
    }
}

// 함수형 컴포넌트 반환
function withDataFetching(Component) {

    // fetching data

    return <Component />
}
```

컴포넌트를 반환하는 함수를 작성하면 된다.

함수 내에서 data fetching logic을 작성하면 된다.

## fetching with HOC

```jsx
// withDataFetching.js
export default function withDataFetching(Component) {

    return class extends React.Component {
        constructor(props) {
            super(props)
            this.state = {
                data: [],
                loading: true, 
                error: ''
            }
        }

        async componentDidMount() {
            try{
                const response = await fetch(this.props.url)
                const dataJSON = await response.json()

                if (dataJSON) {
                    this.setState({
                        data: dataJSON,
                        loading: false
                    })
                }
            } catch(err) {
                this.setState({
                    loading: false, 
                    error: err.message
                })
            }
        }

        render() {

            const {data ,loading, error} = this.state

            return <Component data={data} loading={loading} error={error} {...this.props} />
        }
    }
}

```

fetching한 데이터를 컴포넌트에 props로 전달한다.

## Using HOC

정의한 HOC를 fetching이 필요한 컴포넌트에 사용한다.

```jsx

const MyComponent = ({data, loading, error}) => {

    return (
        <ul>
        {
            data.map(item => <li key={item.id}>{item.body}</li>)
        }
        </ul>
    )
}

export default withDataFetching(MyComponent)

```
