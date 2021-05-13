---
layout: post
title: TypeGraphQL[5] - Confirmation Email 
date: 2021-05-13 22:41 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## Confirmation via Email

이메일을 통해서 User를 confirm하는 과정을 구현할 것이다.

Confirmation은 어떤 과정을 거칠까?

1. 유저에게 토큰과 함께 confirmation 이메일을 보낸다.
2. 유저가 confirm 버튼을 누르면 token이 서버로 전달된다.
3. 서버에서 토큰을 확인하여 해당 유저를 confirm한다.

해당 프로세스를 구현하기 위해 **nodemailer**라는 라이브러리를 사용할 것이다.

## nodemailer

### Install

```shell
yarn add nodemailer uuid
yarn add -D @types/nodemailer @types/uuid
```

- Nodemailer: Nodemailer is a module for Node.js applications to allow easy as cake email sending.
- uuid: random uuid 생성

### Email을 전송하는 함수 생성

```ts
// src/modules/utils/sendEmail.ts
import nodemailer from "nodemailer";

export async function sendEmail(email: string, url: string) {
    // test account
    const testAccount = await nodemailer.createTestAccount();

    // transporter
    const transporter = await nodemailer.createTransport({
        host: "smtp.ethereal.email",
        port: 587,
        secure: false, // true for 465, false for other ports
        auth: {
            user: testAccount.user, // generated ethereal user
            pass: testAccount.pass, // generated ethereal password
        },
    });

    // send email
    const info = await transporter.sendMail({
        from: '"Fred Foo 👻" <foo@example.com>', // sender address
        to: email, // list of receivers
        subject: "Hello ✔", // Subject line
        text: "Hello world?", // plain text body
        html: `<a href=${url}>${url}</a>`, // html body        
    })
    console.log("Message sent: %s", info.messageId);
    // Message sent: <b658f8ca-6296-ccf4-8306-87d57a0b4321@example.com>

    // Preview only available when sending through an Ethereal account
    console.log("Preview URL: %s", nodemailer.getTestMessageUrl(info));
    // Preview URL: https://ethereal.email/message/WaQKMgKddxQDoou...    
}

```

`email`과 confirm `url`을 인자로 넣어 함수를 호출할 수 있도록 한다.

### How to create confirmation url ?

`utils` 폴더에 `createConfirmationUrl.ts` 파일을 생성한다.

`uuid`로 생성한 `token`을 이용해서 confirmation url을 생성한다.

uesrId를 인자로 받고 생성한 `token`과 함께 `redis`에 저장한다.

이 후 resolver를 통해서 redis에서 userId를 가져와 `confirmed` 값을 true로 변경할 것이다.

```ts
import { redis } from "./../../redis";
import { v4 } from "uuid";

export const createConfirmationUrl = async (userId: number) => {
  // create token
  const token = v4();
  await redis.set(token, userId, "ex", 60 * 60 * 24);
  // url을 반환한다.
  // localhost:3000은 client의 주소이다.
  return `http://localhost:3000/user/confirm/${token}`;
};
```

### Register시, 이메일을 전송할 수 있도록 설정

`RegisterResolver`로 이동하여 다음과 같이 코드를 수정

```ts
@Resolver()
export class RegisterResolver {

    // 생략..

  @Mutation(() => User)
  async register(
    @Arg("data") { firstName, lastName, email, password }: RegisterInput
  ): Promise<User> {
    const hasedPassword = await bcryptjs.hash(password, 12);

    const user = await User.create({
      firstName,
      lastName,
      email,
      password: hasedPassword,
    }).save();

    // 데이터베이스에 세이브하고 해당 유저에게 confirm 이메일을 전송한다.
    await sendEmail(email, await createConfirmationUrl(user.id));
    return user;
  }
}
```

이제 `register`를 호출하면 유저에게 이메일을 전송한다.

## Confirm Resolver

유저로부터 토큰을 받아 해당 유저가 맞으면 confirmed를 true로 바꾸는 resolver다.

```ts
import { User } from "./../../entity/User";
import { redis } from "./../../redis";
import { Arg, Mutation, Resolver } from "type-graphql";

@Resolver()
export class ConfirmUserResolver {
  @Mutation(() => Boolean)
  async confirm(@Arg("token") token: string): Promise<boolean> {
    const userId = await redis.get(token);
    if (!userId) {
      return false;
    }

    await User.update({ id: parseInt(userId, 10) }, { confirmed: true });
    await redis.del(token);
    return true;
  }
}
```

confirmation 이메일을 클릭하면 client쪽에서 다음 쿼리를 호출할 수 있도록 한다.

```graphql
mutation {
    confirm(token: "token value")
}

```
