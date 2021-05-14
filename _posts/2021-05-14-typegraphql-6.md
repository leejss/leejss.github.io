---
layout: post
title: TypeGraphQL[6] - Forgot Password
date: 2021-05-14 13:11 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## ForgotPassword resolver

다음 형태의 `mutation` type을 작성하고자 한다.

```graphql
type Mutation {
    forgotPassword(email: String!): Boolean!
}
```

이메일을 인자로 전달하면 해당 유저에게 토큰과 함꼐 `change-password` URL로 이동할 수 있는 링크를 전달한다.

```ts
// ForgotPassword.ts

@Resolver()
export class ForgotPasswordResolver {
    @Mutation(() => Boolean)
    async forgotPassword(@Arg("email") email: string): Promise<boolean> {
        // 이메일에 해당하는 유저를 찾는다.
        const user = await User.findOne({where: {email}})
        
        if (!user) {
            return false
        }

        // 토큰을 생성한다
        const token = v4()
        // redis store에 토큰-userId 쌍을 저장한다.
        // 토큰을 구별하기 위해 prefix을 설정한다.
        await redis.set(forgotPasswordPrefix + token, user.id, "ex", 60 * 60 * 24)

        // 이메일을 전송한다.
        await sendEmail(email, `http://localhost:3000/user/change-password/${token}`)

        return true
        
    }
}
```

```graphql
mutation {
    forgotPassword(email: "lee@lee.com")
}
```

토큰과 함꼐 링크가 전송된다. 링크를 클릭하면 유저가 토큰과 함께 changePassword를 요청하게 된다.

## ChangePassword Resolver

비밀번호를 잊어버리면 새롭게 비밀번호를 설정하도록 한다.

다음의 Mutation Schema를 작성하고자한다.

```graphql
type Mutation {
     changePassword(data: ChangePasswordInput!): User
}

```

먼저 InputType을 설정한다

```ts
// ChangePasswordInput.ts

@InputType()
export class ChangePasswordInput {

    @Field()
    token: string;

    @Field()
    password: string;
    
}

```

```ts
@Resolver()
export class ChangePasswordResolver {
    @Mutation(()=> User)
    async changePassword(@Arg("data") {token, password}: ChangePasswordInput): Promise<User | null> {
        // redis store에서 userId를 가져온다.
        const userId = await redis.get(forgotPasswordPrefix + token)

        if (!userId) {
            return null
        }
        // fetch user
        const user = await User.findOne(userId)

        if (!user) {
            return null
        }

        // redis store의 데이터를 삭제한다.
        await redis.del(forgotPasswordPrefix + token)

        // password를 새로 설정한 후 데이터베이스에 반영한다.
        user.password = await bcrpytjs.hash(passoword, 12)
        await user.save()

        return user
    }
}
```

graphql 쿼리를 작성하여 테스트 해본다.

```graphql
mutation {
    changePassword(token: "forgot password로부터 전달받은 토큰", password: "새롭게 설정할 패스워드") "{
        name
        email
    }
}
```
