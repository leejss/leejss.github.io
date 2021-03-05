---
layout: post 
title: 유저컨트롤러 만들기(Creating a User conroller) - 3
---

## 순서

1. Login 구현하기
2. Logout 구현하기

## 1. Login 구현하기

로그인과 관련된 action은 두 가지가 있다.

- 로그인 화면 보여주기
- authenticate()

### 로그인 화면 보여주기

```javascript
module.exports = {
    // ....

    login: (req, res) => {
        res.render("users/login")
    }
}
```

### authenticate

입력한 데이터가 저장된 데이터와 일치하는지 증명하는 액션.

```javascript
module.exports = {
    // ....
    authenticate: passport.authenticate("local", {
        failureRedirect: "/users/login",
        successRedirect: "/",
        failureFlash:"로그인에 실패했습니다.",
        successFlash:"로그인하였습니다",
    })
}
```

[Passport.js의 authenticate](http://www.passportjs.org/docs/authenticate/){:target="_blank"}를 이용한다.

## 2. Logout 구현하기

[passport.js의 logout()](http://www.passportjs.org/docs/logout/){:target="_blank"}을 이용한다.

```javascript
module.exports = {
    // ....

    logout: (req, res, next) => {
        req.logout()
        req.flash("success", "로그아웃완료")
        res.locals.redirect = "/";
        next()
    }

}
```
