---
layout: post
title: TypeGraphQL[0] - Setup
date: 2021-05-11 22:05 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## TypeGraphQL이란?

>TypeGraphQL is a library that makes this process enjoyable by defining the schema using only classes and a bit of decorator magic.

>Create GraphQL schema and resolvers with TypeScript, using classes and decorators!

TypeGraphQL은 타입스크립트에서 사용할 수 있는 라이브러리이고, GraphQL의 Schema를 클래스와 데코레이터 문법을 사용하여 만들 수 있게 해주는 라이브러리다.

## Install

### Install TypeGraphQL

```shell
yarn add graphql reflect-metadata type-graphql
```

### Install ApolloServer and Express

```shell
yarn add apollo-server-express express
```

### Install @types

```shell
yarn add -D @types/express @types/graphql @types/node
```

### Install devDependencies

```shell
yarn add -D nodemon ts-node ts-node-dev typescript
```

## Add tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "lib": ["dom", "es6", "es2017", "esnext.asynciterable"],
    "sourceMap": true,
    "outDir": "./dist",
    "moduleResolution": "node",
    "declaration": false,

    "composite": false,
    "removeComments": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "emitDecoratorMetadata": true, // allow to use decorator
    "experimentalDecorators": true, // allow to use decorator
    "skipLibCheck": true,
    "baseUrl": ".",
    "rootDir": "src"
  },
  "exclude": ["node_modules"],
  "include": ["./src/**/*.tsx", "./src/**/*.ts"],
  "references": [{ "path": "../common" }]
}
```

## Add entry point

```ts
// src/index.ts

const main = async () => {
    // all the connection, app, server instance, ...
}

main()
```

## Add scripts

```json
"scripts": {
    "start:dev" : "ts-node-dev --respawn src/index.ts"
}
```

## Build Schema

[TypeGraphQL - Getting Started](https://typegraphql.com/docs/getting-started.html){:target="_blank"}

TypeGraphQL은 타입스크립트의 class와 decorator 문법을 이용하여 GraphQL의 SDL을 사용할 수 있게 해준다. 

TypeGraphQL의 `buildSchema` 함수를 이용하여 생성

```ts
import { buildSchema } from "type-graphql";

const main = async () => {
    const schema = await buildSchema({
        resolvers: [/*resolver를 여기에 등록해준다.*/]
    })
}

```

### Define Test Resolver

`class` 문법과 `@Resolver()` 데코레이터를 이용하여 resolver를 만들어준다.

```ts
import {Resolver, Query} from 'type-graphql'

@Resolver()
class TestResolver {
    @Query(() => String)
    async test() {
        return "Hello"
    }
}
```

### Add Test Resolver

```ts
    const schema = await buildSchema({
        resolvers: [TestResolver]
    })
```

## Create server instance

`ApolloServer` 생성자를 통해서 서버 인스턴스를 만든다.

```ts
import ApolloServer from 'apollo-server-express'
import express from 'express'
//...

const main = async () => {
    const schema = await buildSchema({
        resolvers: [TestResolver]
    })

    const app = express()

    const server = new ApolloServer({
        schema
    })

    apolloServer.applyMiddleware({ app });

    app.listen(4000, () => {
        console.log(`Server listen http://localhost:4000/graphql`);
    });
}

```

## Start

```shell
yarn start
```

### Request test

GraphQL Playground에 들어가서 GraphQL 요쳥을 해본다.

```graphql

{
    query {
        test
    }
}

```
