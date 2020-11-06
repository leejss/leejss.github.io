---
layout: post 
title: 유저컨트롤러 만들기(Creating a User conroller) - 1
categories: NodeJS
tags: 
- TIL
- express.js
- Node.js
- mogodb
- Mongoose
- passport.js
---

## 순서

1. 필요한 패키지와 모듈 불러오기
2. CRUD 중 `create` action 구현하기
3. Reference 사이트
  
### 1. 필요한 패키지와 모듈 불러오기

User 모델과 로그인/로그아웃 처리를 위해 `passport.js`를 불러온다

```javascript
// controllers/userController.js
const User = require("../models/user"),
    Passport = require("passport")
```

### 2. CRUD actions 구현하기

C: Create

R: Read

U: Update

D: Delete

`User` 모델에게 행해져야 하는 기본적인 actions를 정의하는 단계

```javascript
// controllers/userController.js
module.exports = {

    create: (req, res, next) => {
        if (req.ski) {
            next()
        }

        let newUser = {
            name: {
                first: req.body.first,
                last: req.body.last
            },
            email: req.body.email,
            password: req.body.password,
            zipCode: req.body.zipCode
        }

        // userSchema의 plugin인 passport-local-mongoose를 이용하여 
        // mongodb에 저장한다.
        User.register(newUser, req.body.password, (error, user) => {
            if (user) {
                // user가 성공정으로 저장되었을 경우 flash message를 띄운다.
                req.flash("success", `${user.fullName}이 저장되었습니다.`)
                // 저장되었을 경우, 로그인 페이지 이동
                res.locals.redirect = "/users/login"
                next()
            } else {
                req.flash("error", `User를 생성하지 못했습니다: ${error.message}`)
                res.locals.redirect = "/users/new"
                next()
            }
        })

    }

}
```

`User` 모델을 통해서 `register()`를 호출할 수 있는 이유는 `models/user.js`에서

```javascript
userSchema.plugin(passportLocalMongoose, {
  usernameField: "email",
});
```

plugin으로 `passport-local-mongose`를 설정했기 때문이다.

**register(user, password, cb) Convenience method to register a new user instance with a given password. Checks if username is unique.**

### 3. Reference 사이트

[passport-local-mongoose](https://www.npmjs.com/package/passport-local-mongoose){:target="_blank"}
