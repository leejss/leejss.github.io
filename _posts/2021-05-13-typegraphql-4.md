---
layout: post
title: TypeGraphQL[4] - Authorization 
date: 2021-05-13 03:02 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## Authorization이란 ?

[Authorization From Wikipedia](https://en.wikipedia.org/wiki/Authorization)

>In contrast with identification, the act of indicating a person or thing's identity, authentication is the process of verifying that identity.

어떤 리소스에 access가 가능한지를 검증하는 기능을 말한다.

web에서 authrization은 예를 들어서, 로그인을 해야 접근할 수 있는 경로가 있다면, 그 경로는 Authorization이 필요하다.

어떤 경로의 접근을 제한하고 특정 자격을 지닌 유저만 접근 가능하도록 하는 것이 Authorization이다.

## TypeGraphQL로 Authorization 구현하기

### 방법 1. @Authorized() 데코레이터와 authChecker 함수

[TypeGraphQL - Authorization](https://typegraphql.com/docs/authorization.html)

#### @Authorized()

먼저 Auth가 필요한 곳에 `@Authorized()` 데코레이터를 추가해준다. Object type이나 resolver의 query나 mutation에 적용 가능하다.

```ts
// Object type에 적용
@ObjectType()
class MyObject {
  @Field()
  publicField: string;

  @Authorized()
  @Field()
  authorizedField: string;

  @Authorized("ADMIN") // 인자를 통해 Role 추가
  @Field()
  adminField: string;

  @Authorized(["ADMIN", "MODERATOR"])
  @Field({ nullable: true })
  hiddenField?: string;
}
```

Authorized user는 `publicField`와 `authorizedField`를 읽을 수 있다. 하지만 `adminField`와 `hiddenField`는 읽을 수 없다.

```ts
// resolver에 적용

@Resolver()
export class RegisterResolver {
  @Authorized() // auth가 필요한 query임을 알린다.
  @Query(() => String)
  async hello() {
    return "hello";
  }
  
  // 생략 ..
}
```

#### schema에 authChecker함수 추가

`authChecker` 함수는 실제로 Authorization로직을 구현하는 함수다. `buildSchema`를 다음과 같이 수정하자.

```ts
// index.ts

const schema = await buildSchma({
    resolvers: [MeResolver, RegisterResolver, LoginResolver]
    authChecker: ({context: {req}}) => {
        // session에 userId값이 있다면 access 가능
        if (req.session.userId) {
            return true
        }
        return false
    }
})
```

이제 로그인하지 않고 `hello` 쿼리를 요청하면 error가 발생한다.

```graphql
{
    hello
}
```

### 방법 2. 미들웨어

[TypeGraphQL - middleware](https://typegraphql.com/docs/middlewares.html)

미들웨어를 만들고 등록하여 특정 필드나 쿼리에 접근하기 전에 Authorization을 수행할 수 있다.

#### Creating Middleware

미들웨어는 기본적으로 함수이며 다음 두 가지 파라미터를 가진다.

- resolverData: `{root, args, context, info}`
- next: used to control the execution of the next middleware and the resolver to which it is attached

미들웨어를 따로 관리하기 위해 `middlewares` 폴더를 먼저 생성한다.

##### logger middleware

resolverData를 logging하는 간단한 미들웨어를 만든다.

```ts
// src/modules/middlewares/logger.ts

export const logger: MiddlewareFn = async (data, next) => {
    console.log(data)
    return next()
}
```

`MiddlewareFn` 타입을 이용하여 미들웨어 함수를 만든다.

#### isAuth middleware

Authorization을 수행하는 미들웨어를 만든다.

```ts
// src/modules/middlewares/isAuth.ts

// req에 접근하기 위해 제네릭타입을 MyContext로 설정한다.
export const isAtuh: MiddlewareFn<MyContext> = async ({context}, next) => {
    if (!context.req.session.userId) {
        throw new Error("Not authorized");
    }
    return next()
}
```

#### 미들웨어 등록하기

`@Authrized()` 를 지우고 `@UseMiddleware()`를 추가한다.

```ts

    @UserMiddleware(logger, isAuth)
    @Query(() => String)
    async hello() {
        return "hello"
    }

```

로그인하지 않고 `hello`를 쿼리할 경우 `Not authorized` 메세지와 함께 에러가 발생할 것이다.
