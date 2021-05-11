---
layout: post
title: TypeGraphQL[1] - TypeORM, Resigter Resolver
date: 2021-05-12 02:47 +0900
tags: GraphQL
---

해당 포스트는 [Ben Awad의 TypeGraphQL Tutorial](https://www.youtube.com/playlist?list=PLN3n1USn4xlma1bBu3Tloe4NyYn9Ko8Gs){:target="_blank"}을 기반으로 작성하였습니다.

## Add TypeORM

### TyepORM이란?

[TypeORM docs](https://typeorm.io/#/)

>Its goal is to always support the latest JavaScript features and provide additional features that **help you to develop any kind of application that uses databases** - from small applications with a few tables to large scale enterprise applications with multiple databases.

데이터베이스와 연결, 쿼리, 수정 등을 용이하게 해주는 라이브러리다.

[ActiveRecord](https://en.wikipedia.org/wiki/Active_record_pattern){:target="_blank"}와 [Data Mapper](https://en.wikipedia.org/wiki/Data_mapper_pattern){:target="_blank"} 두 가지 패턴을 지원한다.

TypeORM을 통해서 두 가지 패턴을 구현하는 방법은 [공식 홈페이지](https://typeorm.io/#/active-record-data-mapper/what-is-the-active-record-pattern){:target="_blank"}에 나와 있다.

### Install TypeORM

```shell
yarn add pg typeorm
```

## Connect to database

connection 옵션을 명시한 configuration file을 만들고 `typeorm`의 `createConnection` 함수를 호출하여 연결할 것이다.

### ormconfig.json

connection options를 명시한 파일.

다양한 옵션에 대한 설명은 공식 홈페이지에서 전부 확인할 수 있다.

[Connection Options](https://typeorm.io/#/connection-options/common-connection-options){:target="_blank"}

```json
{
    "name": "default",
    "type": "postgres",
    "host": "localhost",
    "port": 5432,
    "username": "leejss",
    "password": "1234",
    "database": "typegraphqlexmaple",
    "synchronize": true,
    "logging": true,
    "entities": ["src/entity/*.*"]
 }
```

### connect

```ts
import {createConnection} from "typeorm";

// createConnection method will automatically read connection options
// from your ormconfig file or environment variables

const main = async () => {
    await createConnection();
} 
main()
```

## Define Entity

### Entity란?

>Entity is a class that maps to a database table (or collection when using MongoDB). You can create an entity by defining a new class and mark it with @Entity()

데이터베이스의 table을 클래스 문법으로 구현한 것이 TypeORM의 entity.

`class`위에 `@Entity` 데코레이터를 추가하여 만든다.

```ts
@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    firstName: string;

    // ...
}
```

`src`폴더에 `entity`폴더를 추가하고 `User.ts`를 생성한다.

### User.ts

User는 Entity인 동시에 GraphQL의 type에 해당한다. 따라서 클래스 정의 위에 두 가지 데코레이터를 함께 사용해야 한다.

`@ObjectType()` 과 `@Entity()`

또 하나 주목해야할 것은 Active Record pattern을 사용할 것이기 때문에 Entity는 BaseEntity를 상속받아야 한다.

> Simply said, the Active Record pattern is an approach to access your database within your models.

Active Record pattern란 간단히 말해서 모델을 통해서 데이터베이스에 접근하는 방식을 말한다.

예를 들어, `User.findOne({where: {email}})` 이런 식으로 하는 것이 Active Record 패턴이다.

```ts
import { Field, ID, ObjectType, Root } from "type-graphql";
import { Entity, PrimaryGeneratedColumn, Column, BaseEntity } from "typeorm";

@ObjectType() // for graphql API
@Entity() // for database
export class User extends BaseEntity {
  @Field(() => ID)
  @PrimaryGeneratedColumn()
  id: number;

  @Field()
  @Column()
  firstName: string;

  @Field()
  @Column()
  lastName: string;

  @Field()
  name(@Root() user: User): string {
    return `${user.firstName} ${user.lastName}`;
  }

  @Field()
  @Column("text", { unique: true })
  email: string;

  @Column()
  password: string;
}

```

다른 라이브러리의 데코레이터들이 하나의 클래스에 동시에 사용되고 있기 때문에 헷갈리지 않도록 주의해야 한다.

[Type GraphQL의 Types and Fields](https://typegraphql.com/docs/types-and-fields.html){:target="_blank"}  
[TypeORM의 Entity](https://typeorm.io/#/entities){:target="_blank"}

## Define ResigerResolver

`Entity`와 `GraphQL Type`을 정의했으니 해당 데이터와 관련된 연산을 수행할 Resolver를 정의할 차례다.

그 중 `User`를 생성할 수 있는 `RegisterResolver`를 정의할 것이다.

`TestResolver`를 작성했던 것처럼 `@Resolver()` 데코레이터를 이용하여 만든다.

TypeGraphQL의 resolver는 [공식 Documentation](https://typegraphql.com/docs/resolvers.html){:target="_blank"}에서 자세하 살펴볼 수 있다.

간단히 말하자면 `@Query`, `@Mutation`데코레이터를 이용하여 GraphQL의 `Query`와 `Mutation` 타입을 정의할 수 있다.

Register는 User를 생성하는 것이니, `Mutation` 타입을 만들어야 한다.

```ts

@Resolver()
export class RegisterResolver {
    @Mutation(()=> User)
    async register (): Promise<User> {
        // do here
    }
}

```

데코레이터의 인자로 함수를 넣어주는데, 함수가 return type을 결정하는 역할을 한다. User 타입을 반환할 것이므로 `() => User`를 인자로 넣어준다.

### Arguments

register API는 유저 이메일, 이름과 비밀번호를 입력받아 새로운 유저를 생성한다.

즉, 인자를 받아야 하는데 이 역시도 데코레이터(parameter decorator)를 이용하여 구현한다. `@Arg`를 이용한다.

```ts
@Resolver()
export class RegisterResolver {
    @Mutation(()=> User)
    async register (
        @Arg("email") email: string,
        @Arg("name") name: string,
        @Arg("password") password: string
    ): Promise<User> {
        // create user and save user
    }
}

/*
type Mutation {
  register(
    password: String!
    email: String!
    lastName: String!
    firstName: String!
  ): User!
}
*/
```

이렇게 해도 문제 없지만 이러한 방식의 문제점은 argument의 수가 늘어났을 때 인자의 이름이 계속 반복된다는 것이다.

이러한 repeating을 줄이기 위해 `Input` 이라는 타입을 이용할 수 있다.

### Input Types

`RegisterInput.ts`라는 새로운 파일을 만들어 거기에 ResigerResolver의 Input을 나타내는 클래스를 만든다. 클래스를 만들 때, `@InputType()` 데코레이터를 이용한다.

```ts
@InputType()
export class RegisterInput {
  @Field()
  firstName: string;

  @Field()
  lastName: string;

  @Field()
  email: string;

  @Field()
  password: string;
}
```

### Argument 수정

Input Type을 이용하기 위해 Resolver의 인자를 다음과 같이 수정한다.

```ts

    async register(
        @Arg("data") {firstName, lastName, email, password } : RegisterInput
    ): Promise<User> {

    }

```

### Create User

비밀번호를 암호화하기 위해 다음 라이브러리를 설치한다.

```shell
yarn add bcryptjs
yarn add -D @types/bcryptjss
```

```ts
@Resolver()
export class RegisterResolver {
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
    return user;
  }
}
```

`User.create({})`가 가능한 이유는 User Entity에서 BaseEntity를 상속받았기 때문이다. => Active Record 패턴.

```ts
// BaseEntity.d.ts

    /**
     * Saves current entity in the database.
     * If entity does not exist in the database then inserts, otherwise updates.
     */
    save(options?: SaveOptions): Promise<this>;
```

마지막에 `save()`를 호출해야 데이터베이스에 반영된다.

## Field Resolver

mutation이 아니라 field에 대한 resolver를 추가할 수 있다.

firstName과 lastName을 합친 `name`에 대한 resolver를 추가해보자.

`Field Resolver`를 `Register Resolver`에 아니면 `User` entity에 추가할 수 있는데 `User`에 추가해보겠다.

`User`엔티티에 다음 field을 추가한다.

```ts
  @Field()
  name(@Root() user: User): string {
    return `${user.firstName} ${user.lastName}`;
  }
```

`@Root()`라는 parameter decorator를 이용한다. User 객체를 주입하는 것이다.

## GraphQL request example

```graphql

{
    mutation {
        register(data : {
            firstName: "lee", 
            lastName:"jss", 
            email: "lee@lee.com", 
            password: "1234"
        }) {
            id
            name
            email
        }
    }
}
```