---
layout: post 
title: 유저모델 만들기(Creating a User model)
categories: Post
tags: 
- TIL
- express.js
- Node.js
- mogodb
- Mongoose
---

## 순서

1. mongoose.Schema 정의하기
2. SchemaTypes 설정하기
3. Validating
4. virtual property 설정하기
5. pre hook 설정하기
6. plugin 설정하기
7. export
8. reference 사이트

### 1. `mongoose.Schema` 정의하기

Schema는 Mongodb의 collection에 mapping되며 Schema 정의는 documents의 shape를 설정하는 단계다.

```javascript
const mongoose = require("mongoose")
const passportLocalMongoose = require("passport-local-mongoose")

const userSchema = new mongoose.Schema({
    // property 내용이 들어간다.
})
```

### 2. `SchemaTypes` 설정하기

property의 data type을 설정하는 단계.

```javascript
const userSchema = new mongoose.Schema({
    name: String,
    email: String,
    password: String,
    zipCode: Number,
    // 다른 schema와 association 설정. one to many 관계
    product: [{type: mongoose.Schema.Types.ObjectId, ref: "Product"}]
})
```

### 3. Validating

document에 들어온 데이터를 검증하는 단계를 추가한다. 의도하지 않은 데이터가 저장되는 것을 방지한다.

```javascript
const userSchema = new mongoose.Schema({
    name: {
        first: {
            type: String,
            trim: true
        },
        last: {
            type: String, 
            trim: true
        }
    },
    email: {
        type: String,
        unique: true,
        lowercase: true,
        required: true
    },

    password: {
        type: String,
        required: true,
    },

    zipCode: {
        type: Number,
        min: [10000, "유효하지 않은 우편번호입니다."],
        max: 99999
    },
    // 다른 schema와 association 설정. one to many 관계
    product: [{type: mongoose.Schema.Types.ObjectId, ref: "Product"}]
    },
    // one to one 관계
    subscribedAccount: { type: Schema.Types.ObjectId, ref: "Subscriber" },

timestamps: true,)
```

mongoose가 정의한 validation이며 document가 mongodb에 저장되기 전에 내부적으로 `Document.prototype.validate()`를 호출하여 검증한다.

### 4. `virtual` property 설정하기

`virtual`은 mongodb에 저장되지 않으나 설정하고 활용할 수 있는 document properties다.

`virtual`을 설정하기 위해서는 `Schema.prototype.virtual()`을 이용해야 한다.

```javascript
userSchema.virtual("fullName").get(function () {
  return `${this.name.first}${this.name.last}`;
});
// userSchema.virtual("fullName") create VirtualType with name 'fullName'
// fullName이라는 property를 활용할 수 있다.
```

### 5. `pre` hook 설정하기

`Schema.prototype.pre()`을 이용해 hook을 설정한다.

```javascript
// 만약 동일한 email을 가진 subscriber document가 존재하다면 
// user와 subscriber를 associate한다. 
// pre()의 첫번째 인자로 method name이 string 타입(또는 정규표현식)으로 들어간다.
userSchema.pre("save", function (next) {
  let user = this;
  if (user.subscribedAccount === undefined) {
    Subscriber.findOne({
      email: user.email,
    })
      .then((subscriber) => {
        user.subscribedAccount = subscriber;
        next();
      })
      .catch((error) => {
        console.log(`Error in connecting subscriber: ${error.message}`);
        next(error);
      });
  } else {
    next();
  }
});
```

mongodb에 save전에 Subscriber 모델에 동일한 이메일을 가진 document가 있으면 해당 document와 서로 association한다.

이 것이 가능한 이유는 User의 모델의 schema type으로 subscribedAccount를 정의했기 때문이다.

`subscribedAccount: { type: Schema.Types.ObjectId, ref: "Subscriber" }`

### 6. `plugin` 설정하기

`Passport.js` 와 `passport-local-mongoose`를 사용하기 위해서 `plugin()`을 호출한다.

```javascript
userSchema.plugin(passportLocalMongoose, {
  usernameField: "email",
});
```

### 7. export

```javascript
module.exports = mongoose.model("User", userSchema);
```

### 8. reference

- [mongoose.Schema](https://mongoosejs.com/docs/guide.html){:target="_blank"}
- [SchemaTypes](https://mongoosejs.com/docs/schematypes.html){:target="_blank"}
- [Validating](https://mongoosejs.com/docs/documents.html#validating){:target="_blank"}
- [virtual()](https://mongoosejs.com/docs/api.html#schema_Schema-virtual){:target="_blank"}
- [pre()](https://mongoosejs.com/docs/api.html#schema_Schema-pre){:target="_blank"}
- [plugin](https://mongoosejs.com/docs/plugins.html#global){:target="_blank"}
- [plugin search](https://plugins.mongoosejs.io/){:target="_blank"}
