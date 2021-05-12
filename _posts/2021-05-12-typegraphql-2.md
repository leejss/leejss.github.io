---
layout: post
title: TypeGraphQL[2] -Validation
date: 2021-05-12 12:57 +0900
tags: GraphQL
---

## What is Validation ?

[Data validation - wikipedia](https://en.wikipedia.org/wiki/Data_validation){:target="_blank"}  
[Data cleansing - wikipedia](https://en.wikipedia.org/wiki/Data_cleansing){:target="_blank"}

Data가 **correct** 하고 **useful** 한지 검증하는 과정을 data validation이라 한다.

검증하는 과정에서 사용하는 기준들을 **validation rules** 또는 **validation constraints** 이라 한다.

입력받은 `email` 필드가 알맞은 이메일 형식을 가졌는지, `firstName` 또는 `lastName` 필드가 정해진 길이 기준을 만족하는 지, **validation constraints**을 추가하는 작업이다.

## Add class-validator

[class-validator github](https://github.com/typestack/class-validator){:target="_blank"}

Validation을 위해 class-validator를 이용한다. 클래스에 **Property decorator**를 추가하여 간단하게 validation constraints을 추가할 수 있다.

### Edit Input Type

`RegisterInput`에 validation을 적용해보자.

각종 contraints를 import하여 사용할 수 있다.

```ts
import { IsEmail, Length } from "class-validator"; // import constraints
import { Field, InputType } from "type-graphql";

@InputType()
export class RegisterInput {
  @Field()
  @Length(1, 255) // apply constraints
  firstName: string;

  @Field()
  @Length(1, 255)
  lastName: string;

  @Field()
  @IsEmail()
  email: string;

  @Field()
  password: string;
}
```

## Create Custom Validator

직접 constraints를 만들어 적용할 수 있다.

### Create custom validation class

custom validation class는 다음과 같은 모습을 가지고 있다.

```ts
// isEmailAlreadyExists.ts
import { User } from "./../../../entity/User";
import {
  registerDecorator,
  ValidationOptions,
  ValidatorConstraint,
  ValidatorConstraintInterface,
} from "class-validator";

@ValidatorConstraint({ name:"UserAlreadyExist", async: true })
export class IsUserAlreadyExistConstraint implements ValidatorConstraintInterface {
  validate(userName: any, args: ValidationArguments) {
    return UserRepository.findOneByName(userName).then(user => {
      if (user) return false;
      return true;
    });
  }

    defaultMessage(args: ValidationArguments) {
    // here you can provide default error message if validation failed
        return 'Email alreay exists';
    }
}
```

### Create custom validation decorators

```ts
// isEmailAlreadyExists.ts
export function IsEmailAlreadyExist(validationOptions?: ValidationOptions) {
  return function (object: Object, propertyName: string) {
    registerDecorator({
      target: object.constructor,
      propertyName: propertyName,
      options: validationOptions,
      constraints: [],
      validator: IsEmailAlreadyExistConstraint, // 여기에 constraints 클래스를 할당해준다.
    });
  };
}

```

## 적용

```ts
  @Field()
  @IsEmailAlreadyExist() // custom validator
  @IsEmail()
  email: string;
```
