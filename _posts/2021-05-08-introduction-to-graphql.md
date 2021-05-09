---
layout: post
title: First Step to GraphQL
date: 2021-05-08 21:35 +0900
tags: GraphQL Nodejs
---

[How to Get Started with GraphQL and Node.js](https://www.freecodecamp.org/news/get-started-with-graphql-and-nodejs/){:target="_blank"}

## GraphQL

>GraphQL is a query language for your API, and a server-side runtime for executing queries using a type system you define for your data.

REST는 URI를 통해 데이터에 접근하고 제공하지만 GraphQL은 **query**를 통해 원하는 데이터에 접근하거나 제공한다.

또 한가지 특징은 type system를 사용한다는 것이다.

**참고** - [GraphQL - Schemas and Types](https://graphql.org/learn/schema/){:target="_blank"}

```graphql

type Book {
    id: ID!
    author: String!
    title: String!
    price: Int!
    tags: [String!]!
}

```

타입을 지정하는 것은 간단하다. 프로그래밍 언어의 객체 문법을 사용해서 타입을 정의할 수 있다.

`type` 키워드를 통해서 type name을 정하고, `{}`(square brackets)안에 해당 타입이 가지는 `fields`를 정의하면 된다.

`field`를 정의할 때, 타입도 같이 정의한다.

`field`를 정의할 때 사용한 느낌표(Exclamation mark)는 해당 `field`가 non-nullable이라는 뜻이다. null값을 허용하지 않겠다는 뜻이다.

`[String!]`은 문자열을 담은 배열 타입을 값으로 받겠다는 뜻이다.

### Query Type

>Every GraphQL service has a query type and may or may not have a mutation type. These types are the same as a regular object type, but they are special because they define the entry point of every GraphQL query.

query라는 단어가 의미하듯이 데이터를 요구하는 역할을 맡는다.

#### Server

서버 쪽에서 다음과 같이 `Query` 타입을 정의했다.

```graphql
type User {
    id: ID!
    name: String!
    age: Int!
}

type Query {
    user(id: ID!): User!
    users: [User!]!
}
```

#### Client

그리고 클라이언트 쪽에서 다음과 같이 query한다.

```graphql

query {
    user(id: 1) {
        name
        age
    }
}

```

#### Argument

`field`를 정의할 때, 위와 같이 argument도 설정할 수 있다. 주의해야할 것은 argument의 name과 같이 전달해야 한다는 것이다.

여기까지 GraphQL에 대한 간단한 인트로였고 실제로 GraphQL을 project에 추가해보겠다.

## Install GraphQL ans Apollo Server

[Apollo Server](https://www.apollographql.com/docs/apollo-server/){:target="_blank"}

아폴로 서버는 graphql의 서버를 담당한다.

```shell
yarn add graphql apollo-server
```

### Import and Add Server

```js
const { gql, ApolloServer } = require("apollo-server");

// The ApolloServer constructor requires two parameters: your schema
// definition and your set of resolvers.
const server = new ApolloServer({ typeDefs, resolvers });
```

`typeDefs`와 `resolvers` 정의가 필요해 보인다.

## Schema and Resolvers

```js
// Schema를 정의한다.
const typeDefs = gql`
    type Query {
        greeting: String
    }
`

const resolvers = {
  Query: {
    greeting: () => "Hello World!",
  },
};
```

>Resolvers tell Apollo Server how to fetch the data associated with a particular type.

## server.listen

```js
server
  .listen({ port: 9000 })
  .then((serverInfo) => console.log(`Server running at ${serverInfo.url}`));
```

## Query data from client

>The thing is that with GraphQL we always make POST requests where we pass the query in the payload (body).

```js
const GRAPHQL_URL = "http://localhost:9000/";

async function fetchGreeting() {
  const response = await fetch(GRAPHQL_URL, {
    method: "POST",
    headers: {
      "content-type": "application/json",
    },
    // query string을 body에 담아 post request
    body: JSON.stringify({
      query: `
            query {
                greeting
            }
        `,
    }),
  });
  const {data} = await response.json();
  return data
}

fetchGreeting();
```
