---
layout: post
title: React의 Error Boundary component
date: 2021-08-16 00:34 +0900
tags:
  - React
---

## Concept

리엑트는 렌더링 과정에서 자바스크립트 코드에서 에러가 발생하면 component tree를 전부 unmounting하게 된다.

모든 컴포넌트를 unmount하는 것 대신에 fallback UI를 보여주면서 다른 컴포넌트의 기능을 그대로 유지할 수는 없을까?

## Error Boundary component

Error Boundary 컴포넌트로 다른 컴포넌트를 감싸서 에러가 발생하면 Error Boundary 컴포넌트가 에러를 보여주도록 설정한다.

Error Boundary는 에러 처리를 위해 두 가지 생명주기 메서드를 활용할 수 있다.

### static getDerivedStateFromError()

```js
static getDerivedStateFromError(error) {
    // Error가 발생하면 state 업데이트
    return {
        hasError: true
    }
}
```

### componentDidCatch()

```js
componentDidCatch(error, info) {
    // log error
    loggger(error, info)
}

```

### 활용

에러를 캐치하고자 하는 컴포넌트의 상위 컴포넌트로 두면 된다.

```js
<ErrorBoundary>
  <App />
</ErrorBoundary>
```
