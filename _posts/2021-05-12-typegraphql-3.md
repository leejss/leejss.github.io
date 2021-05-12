---
layout: post
title: TypeGraphQL[3] - Login
date: 2021-05-12 15:59 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## What is session ?

[Session - wikiepedia](https://en.wikipedia.org/wiki/Session_(computer_science)){:target="_blank"}
[Cookie based session](https://poiemaweb.com/express-session-handling){:target="_blank"}

>temporary and interactive information interchange ...

Login 인증을 위해 session을 사용한다.

Login 시, cookie와 session store에 userID를 저장한다.

session data는 서버에 저장되고 그에 관한 id가 쿠키에 저장되는 방식.

## Session 세팅하기

session store로 [Redis](https://redis.io/){:target="_blank"}를 사용할 것이다.

따라서 Redis 서버를 먼저 설치하는 작업이 필요하다.

[WSL2 - Install Redis](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-database#install-redis){:target="_blank"}

```shell
yarn add express-session connect-redis ioredis cors
yarn add -D @types/express-session @types/connect-redis @types/ioredis @types/cors
```

- **express-session**: express 앱에 session을 사용하기 위해 설치
- **connect-redis**: connect-redis provides Redis session storage for Express.
- **ioredis**: A robust, performance-focused and full-featured Redis client for Node.js.
- **cors**: enable and configure Cross-origin resource sharing

### Import and mount session middleware

```ts
// index.ts
import connectRedis from 'connect-redis'
import sesion from 'express-session'
import cors from 'cors'
import redis from './redis'
// ... 생략


const main = async () => {
    // .. 생략

    const RedisStore = connectRedis(session);
    // cors
    app.use(cors({
        credentials: true, // Set to true to pass the header, otherwise it is omitted.
        origin: "http://localhost:3000", // only requests from "http://localhost:3000" will be allowed.
    }))

  app.use(
    session({
    // The session store instance, defaults to a new MemoryStore instance.
      store: new RedisStore({
        client: redis as any, // redis client
      }),
      name: "qid", // The name of the session ID cookie to set in the response (and read from in the request).
      secret: "helloworld",
      resave: false,
      saveUninitialized: false,
      cookie: {
        httpOnly: true,
        secure: process.env.NODE_ENV === "production",
        maxAge: 1000 * 60 * 60 * 24 * 7 * 365,
      },
    })
  );
}

main()

```

### Redis client

`ioredis`를 이용하여 redis client를 생성한다.

```ts
// redis.ts
import Redis from 'ioredis'

export const redis = new Redis()
```

## Session 사용하기

### ApolloServer Context 사용하기

session에 접근하기 위해서는 request 객체에 먼저 접근해야 한다.

resolver에서 req에 접근하기 위해 context를 이용한다.

### context란?

> The context argument is useful for passing things that any resolver might need, like authentication scope, database connections, and custom fetch functions.

>An object (or a function that creates an object) that's passed to every resolver that executes for a particular operation. This enables resolvers to share helpful context, such as a database connection.

```ts
const apolloServer = new ApolloServer({
    schema,
    context: ()=> {},
})
```

다음과 같이 context에 함수를 할당하여 context를 추가할 수 있다.

req객체를 context에 전달해보자.

```ts
  const apolloServer = new ApolloServer({
    schema,
    context: ({ req }: any) => ({
      req,
    }),
  });
```

이후 resolver에서 `@Ctx` 데코레이터를 이용하여 접근할 수 있다.

### Context Type 정의하기

```ts
import { Request } from "express";

export interface MyContext {
  req: Request; 
}

declare module "express-session" {
  interface SessionData {
    userId: number;
  }
}
```

### Login Resolver 정의하기

`email`과 `password`를 전달받아서 데이터베이스에서 먼저 `email`이 존재하는 지 확인하고 존재한다면 `password`를 비교하여 로그인을 수행한다. 그리고 session에 userId를 저장한다.

```ts
import { MyContext } from "./../../types/MyContext";
import bcryptjs from "bcryptjs";
import { User } from "../../entity/User";
import { Arg, Ctx, Mutation, Resolver } from "type-graphql";

@Resolver()
export class LoginResolver {

    @Mutation(() => User)
    async login(
        @Arg("email") email: string,
        @Arg("password") password: string,
        @Ctx() ctx: MyContext
    ): Promise<User | null> {
        const user = await User.findOne({where: {email}})
        if (!user) return null

        const valid = await bcryptjs.compare(password, user.password);

        if (!valid) {
          return null;
        }

        // ctx를 통해서 req의 session 객체에 접근한다.
        ctx.req.session!.userId = user.id;

        return user;       
    }

}

```

이 후 로그인을 요청하면 cookie에 `qid` 라는 이름으로 session id가 저장된 것을 볼 수 있다.

## Session id를 이용하여 현재 로그인한 유저 불러오기

Query 필드를 추가한다.

```ts
import { User } from "../../entity/User";
import { Ctx, Query, Resolver } from "type-graphql";
import { MyContext } from "./../../types/MyContext";

@Resolver()
export class MeResolver {
  @Query(() => User, { nullable: true })
  async me(@Ctx() ctx: MyContext): Promise<User | undefined> {
    if (!ctx.req.session.userId) {
      return undefined;
    }
    const user = await User.findOne(ctx.req.session.userId);
    return user;
  }
}
```

```graphql
{
    me {
        name
        email
    }
}
```

```graphql
{
  "data": {
    "me": {
      "name": "Jay Smith",
      "email": "Jay@jay.com"
    }
  }
}
```
