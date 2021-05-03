---
layout: post
title: React Blog (3)
date: 2021-04-30 14:51 +0900
tags: React
categories: React
---

## 컴포넌트 구성

react component의 전체적인 구성을 살펴본다.

```bash

src
  - pages
  - components
    - auth
    - post
    - write
    - common
    - posts
  - containers
  - modules
  - lib
    - style
    - api
  - index.js
  - app.js

```

### pages

`<Rotue />`에 의해 렌더링되는 상위 컴포넌트를 담은 폴더.

`<~Container />`를 자식 컴포넌트로 삼는다.

### containers

리덕스 스토어와 연결된 컴포넌트를 담는다.

`component`에 데이터를 넘겨주는 역할을 한다.

화면을 이루는 컴포넌트를 자식 컴포넌트로 삼는다.

### components

화면의 UI를 담당하는 컴포넌트를 담는다.

`route`에 따라서 세부 디렉토리를 가진다.

### lib

helper 함수, style에 관련된 변수, api request와 관련된 모듈을 담는다.

### modules

액션타입, 액션 생성 함수, 리듀서, saga를 정의한 모듈을 담는다.

### index.js

스토어를 생성하고, 미들웨어를 등록하고, 라우터를 부여한다.

### App.js

`<Route />`를 등록한다.

## Route 구성

먼저 어떤 화면으로 웹을 구성할 것인지 설정한다.

그 화면을 path로 구분하여  `<Route />`로 만든다.

```js
<Route path="/" component={HomePage} exact />
<Route path="/write" component={WritePage} />
<Route path="/login" component={LoginPage} />
<Route path="/join" component={JoinPage} />
```

이렇게 큰 그림을 먼저 잡고 화면을 구성한다.

## Page 구성

`Page` 컴포넌트는 path별로 구분되는 각각의 전체 화면을 담당한다.  
`Page` 컴포넌트는 `Container` 컴포넌트를 자식 컴포넌트로 가지고 있다.  

```jsx

const HomePage = () => {
    return (
        <>
            <HeaderContainer />
            <ContentContainer />
            <FooterContainer />
        <>
    )
}

```

## Container 구성

Container 컴포넌트의 역할은 redux store에 액션을 dispatch하거나 데이터를 가져오는 것이다

### Redux store와 컴포넌트의 연결

store와 컴포넌트가 연결되기 위해서는 `react-redux` 패키지가 필요하다.  
`redux`는 store와 dispatcher만 제공할 뿐 컴포넌트에서 store에 연결할 수 있는 방법을 제공하지는 않기 때문이다.

`react-redux`는 store에 접근할 수 있게 크게 두 가지 방법을 제시한다.

#### connect()

[connect(mapStateToProps, mapDispatchToProps)](https://react-redux.js.org/api/connect){:target="_blank"}

`mapStateToProps`는 함수고 `mapDispatchToProps`는 객체다.

#### hook

[useSelector(state => ({}))](https://react-redux.js.org/api/hooks#useselector){:target="_blank"} : store에서 데이터를 select하여 반환한다.

[useDispatch()](https://react-redux.js.org/api/hooks#usedispatch){:target="_blank"} : store에 액션을 dispatch한다.

## components 구성

실제 UI를 담당하는 컴포넌트다.

