---
layout: post 
title: react-router-dom 맛보기 1
tags: React
---

## Static Routing vs Dynamic Routing

### Static Routing

URL을 통해서 요청하면 서버 어딘가에 저장되어 있는 static file(html, ejs ,...)로 응답하여 UI를 렌더링하는 방식

예시) express.js의 routing

~~~javascript

app.get("/", ()=> {
    res.render('home') // rendering home.html
})

app.get("/profile", ()=> {
    res.render('profile') // rendering profile.html
})

~~~

### Dynamic Routing in React

URL을 통해서 요청을 하면 특정 파일로 응답하는 것이 아니라 특정 컴포넌트를 렌더링하는 방식

예시) `react-router-dom`의 `Router` 컴포넌트

~~~javascript
<Route path="/" component={Home} exact />
<Route path="/profile" component={Profile} />
~~~

`/profile`을 요청하면 `Profile`컴포넌트를 화면에 렌더링한다.

## 설치

~~~bash
yarn add react-router-dom
~~~

## Router component

Dynamic routing을 위해서 Router컴포넌트가 먼저 필요하다.

react-router-dom은 두 가지 Rotuer 컴포넌트를 제공한다.

    <BrowserRotuer>
    <HashRouter>

일반적인 URL 경로를 다룰 때는 `<BrowserRouter>`를 이용한다.

`App`컴포넌트에서 `Route`를 이용하기 위해서 `<App />`를 `<BrowserRouter>`로 감싼다.

~~~javascript
// index.js
import {BrowserRouter} from 'react-router-dom'

//...
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById("root")
);
~~~

## Route component, Switch component

router를 등록하고 나면 routing을 위해서 `react-router-dom`은 **route matcher**라고 불리는 컴포넌트를 제공한다.

    <Route />
    <Switch />

### Route component

`<Route />`는 특정 URL을 요청했을 때 화면에 렌더링할 컴포넌트를 정의한다.

~~~javascript
// App.js

// ... 생략

const App = () => {
    return (
        <div>
            <Route path='/' component = {Home} />
            <Route path='/profile' component ={Profile}/>    
        </div>
    )
}
~~~

`path`에 경로를 정의하고 `component`에는 해당 경로에 접근했을 때 렌더링할 컴포넌트를 정의한다.

다음과 같은 표현도 있다.

~~~javascript
<Route path='/profile'>
    <Profile />
</Route>

~~~

`Route`컴포넌트의 특징은, 경로가 일치하는 `Route` 컴포넌트를 찾았다 하더라도 거기서 멈추는 것이 아니라 경로가 일치하는 다른 `Route` 컴포넌트를 찾는다.

~~~javascript
// App.js

// ... 생략

const App = () => {
    return (
        <div>
            <Route path='/' component = {Home} />
            <Route path='/profile' component ={Profile}/>
        </div>
    )
}
~~~

이게 무슨 뜻이냐면 `/profile` 을 요청했을 경우, `Home` 컴포넌트와 `Profile` 컴포넌트 둘 다 렌더링한다는 의미다. 모든 URL 경로는 `/`로 시작하기 때문이다

따라서 올바르게 routing하기 위해서 specific한 `Route` 컴포넌트를 위에 위치하고 general한 컴포넌트를 아래에 위치시킨다.

위 예제를 다음과 같이 수정해야 한다.

~~~javascript
// App.js

// ... 생략

const App = () => {
    return (
        <div>
            <Route path='/profile' component ={Profile}/>
            <Route path='/' component = {Home} />  
        </div>
    )
}
~~~

순서와 상관없이 routing을 하기 위해서 `Route` 컴포넌트는 `exact`라는 props을 제공한다.

~~~javascript
// App.js

// ... 생략

const App = () => {
    return (
        <div>
            <Route path='/' component = {Home} exact />  
            <Route path='/profile' component ={Profile}/>
        </div>
    )
}
~~~

해당 prop을 주면 `path`와 정확히 일치해야 렌더링이 일어난다.

### Switch component

여러 개의 `Route` 컴포넌트 중 일치하는 `path`하나만 렌더링 하고 싶은 경우 `Switch`컴포넌트를 사용하면 된다.

사용법은 `Route` 컴포넌트를 `Switch` 컴포넌트로 감싸면 된다.

~~~javascript
<Switch>
    <Route ... />
    <Route ... />
    <Route ... />

</Switch>
~~~

예시

~~~javascript
<div>
<Route path="/about">
    <About />
</Route>
<Route path="/:user">
    <User />
</Route>
<Route>
    <NoMatch />
</Route>
</div>
~~~

위와 같은 내용에서 `/about` 을 요청했을 경우 `<About />`, `<Usee />`, `<NoMatch />`가 전부 렌더링된다.

`<About />`만 렌더링하기 위해서 `Switch`컴포넌트로 감싸면 된다.

~~~javascript
<Switch>
    <Route exact path="/">
        <Home />
    </Route>
    <Route path="/about">
        <About />
    </Route>
    <Route path="/:user">
        <User />
    </Route>
    <Route>
        <NoMatch />
    </Route>
</Switch>
~~~
