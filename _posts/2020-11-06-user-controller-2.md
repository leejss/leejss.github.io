---
layout: post 
title: 유저컨트롤러 만들기(Creating a User conroller) - 2
---

## 순서

1. CRUD 중 Read 구현하기
2. CRUD 중 Update 구현하기
3. Reference 사이트
  
### 1. CRUD 중 Read 구현하기

**Read**는 데이저베이스에서 User데이터를 가져와 view에 전달하는 action이다.

request시, paramter로 `objectId`를 전달한다.

mongodb에 저장될 때 자동으로 부여하는 `objectId`를 이용하여 `find` query를 수행한다.

Read action의 이름은 `show`로 정한다.

```javascript
// controllers/userController.js
// 필요한 패키지는 require했다고 가정

module.exports = {
    //...

    show: (req, res, next) => {
        // request로 id로 전해준 파라미터값을 받는다.
        let userId = req.params.id
        // mongoose가 제공하는 findById라는 query method를 수행한다.
        User.findById(userId)
            .then(user => {
                res.locals.uesr = user
                next()
            }).catch(error => {
                console.log(`Error fetching user by ID: ${error.message}`);
                next(error);
            })
    }
}
```

id를 파라미터로 전하기 위해서는 `router`에서 path를 `/users/:id`로 설정해야 한다.

그리고 당시 `req`객체는 `user` 객체를 가지고 있어야 한다.

### 2. CRUD 중 Update 구현하기

**Update**는 데이터베이스의 데이터를 수정하는 action에 해당한다.

`mongoose` 가 제공하는 `findByIdAndUpdate()` 를 이용하여 구현한다.

```javascript
module.exports = {
    // ...

    update: (req, res, next) => {
        let userId = req.params.id
        let userParams = {
            name: {
                first: req.body.first,
                last: req.body.last
            },
            password: req.body.password,
            zipCode: req.body.zipCode
        }
        User.findByIdAndUpdate(userId, {
            $set: userParams
        })
            .then(user => {
                // update 성공 시, user 정보 페이지로 redirect
                res.locals.redirect = `/users/${user._id}`
                res.locals.user = user
                next()
            })
            .catch(error => {
                console.log(`Error updating user by ID: ${error.message}`)
                next(error)
            })
    }
}
```

`Model.findByIdAndUpdate()` 는 `_id`와 바뀐 데이터가 반영된 객체를 매개변수로 넘긴다.

### 3. Reference 사이트

[Model.findByIdAndUpdate](https://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate){:target="_blank"}
