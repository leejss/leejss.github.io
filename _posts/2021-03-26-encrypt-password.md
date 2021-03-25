---
layout: post 
title: 회원가입 구현 시, 패스워드 암호화
tags: express
---

## 회원가입 로직

회원가입 시, 이메일과 비밀번호를 입력받는다.

입력받은 비밀번호를 데이터베이스에 그냥 저장하면 보안 상 위험이 있다.

따라서 입력받은 비밀번호를 적절하게 암호화하여 데이터베이스에 저장해야 한다.

## dependencies

- cryto: 비밀번호 암호화에 사용
- express: 서버 구성에 사용
- mongoose: 몽고디비 데이터베이스에 사용

## 유저 모델 스키마 구성

mongoose의 Schema로 모델 스키마를 정의한다.

```javascript
import mongoose from "mongoose";
const UserSchema = new mongoose.Schema({ ... }) 

```

```javascript
    // Schema 내부,
    // 암호화된 패스워드를 받는다.
    hashed_password: {
        type: String,
        requied: "Password is required"
    },
    salt: String

```

## 유저 모델 virtual field 설정

UserSchema의 virtual field로 password를 설정한다.

(password를 입력받는 필드)

입력받은 패스워드를 암호화하여 document의 hashed_password로 설정한다.

```javascript

UserSchema.virtual("password").set(function (password) {
    this._password = password
    this.salt = makeSalt() // salt를 생성하는 스키마 메소드
    this.hashed_password = encryptPassword(password) // 암호화하는 스키마 메소드
}).get(function() {
    return this._password
})

```

## 암호화를 담당하는 스키마 메소드

두 개의 Schema 메소드를 정의한다.

- `makeSalt()`
- `encryptPassword()`

crypto 모듈을 이용하여 입력받은 plain password를 암호화 한다.

```javascript

UserSchema.methods = {

    makeSalt: function(){}, // 랜덤하게 salt를 생성하는 함수
    encryptPassword: function (password) {
        if (!password) return 
        try {
            return crypto..createHmac("sha1", this.salt).
                    .update(password)
                    ..digest("hex");
        } catch (err) {
            return
        }
    }
}

```

## 패스워드 검증하기 - authenticate

패스워드를 검증하는 authenticate 스키마 메소드를 생성한다.

```javascript
UserSchema.methods = {
    // UserSchema.methods 내부
    authenticate: function(password) {
        return this.hashed_password === this.encryptPassword(password)
    }
}
```
