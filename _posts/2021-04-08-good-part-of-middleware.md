---
layout: post
title: middleware를 통한 코드의 중복 제거
date: 2021-04-08 15:30 +0900
tags: Javascript
---

## Middleware

middle이라는 단어에서 알 수 있듯이 어느 사이 중간에 위치하여 특정 기능을 수행하는 역할을 한다.

어떤 서비스마다 반복적으로 수행하는 동작이 있다면 미들웨어로 만들어 서비스 내 코드의 중복을 제거할 수 있다.

## express middleware

요청과 응답 주기 사이에 위치하여 특정 기능을 수행한다.

따라서 앱의 사이클은 **요청 - 미들웨어 스택 - 응답**으로 이루어졌다.

[express가 지원하는 미들웨어](https://expressjs.com/ko/guide/using-middleware.html#middleware.error-handling)

## 코드의 중복 제거

다음 코드를 보자

```javascript
app.get("/posts/:id", postsCtrl.read)
```

```javascript
// posts.ctrl.js
export const read = async (req, res) => {
    const {id} = req.params
    if (!ObjectId.isValid(id)) {
        return res.status(400).end()
    }
    // read code here...
}
```

파라미터로 `id`를 전달하고 컨트롤러에서 id를 검증하고 받는 평범한 코드다.

`id를` 검증하는 컨트롤러 함수가 적다면 상관없지만 그 수가 늘어나면 반복되는 코드도 늘어난다.

따라서 `id를` 검증하는 코드를 미들웨어로 등록하면 코드의 중복을 줄일 수 있다.

```javascript
// 미들웨어 작성
export const checkObjectId = (req, res, next) => {
    const {id} = req.params
    if (!ObjectId.isValid(id)){
        return res.status(400).end()
    }
    return next()
}

```

```javascript
// 미들웨어 등록
app.get('/:id', postCtrl.checkObjectId, postCtrl.read)
```
