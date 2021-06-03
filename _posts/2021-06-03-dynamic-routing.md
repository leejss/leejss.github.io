---
layout: post
title: React의 Dynamic Routing
date: 2021-06-03 17:50 +0900
tags: React
categories:
  - React
---

## Basic Routing

```jsx
<BrowserRouter>
  <Route path="/" exact component={Home} />
  <Route path="/profile/:id" component={Profile} />
  <Route path="product" component={Product} />
</BrowserRouter>

// path="/profile/:id" 를 통해서 이름이 id인 url parameter 선언
```

## URL Parameters

`Profile` 컴포넌트에서 URL 파라미터를 전달받아 사용할 수 있다.

### 방법 1. match 객체 이용

```jsx
export default function Profile({match} {
    const {id} = match.params
    // match의 params객체에서 URL 파라미터 값을 가져올 수 있다.
    // path에서 설정한 이름(id)으로 키가 설정된다.
    return (
        <h1>{id && id}</h1>
    )
})

```

### 방법 2. useParams() 훅 이용

`react-router-dom`에서 `useParams`훅을 제공한다.

```js
// ...
export default function Profile() {
  // match 객체에 접근할 필요 없이 바로 훅을 사용하면 된다.
  const { id } = useParams(); // params객체를 반환한다.
  // ...
}
```

## Query string

url로 `/product?category=top`로 주어진 경우 `category=top`이 query string이다.

### 방법 1. query-string 라이브러리 이용

```jsx
import qs from "query-string";
export default function Product({ location }) {
  // location의 search 키를 이용하여 쿼리 스트링을 얻을 수 있다.
  const searchParams = location.search;
  // query-string 라이브러리 도구를 이용하여 parsing한다.
  const qs = qs.parse(searchParams);
  // ?category=top
  // parsing 결과: {category : "top"}
  return <h1>{qs.category && qs.category}</h1>;
}
```
