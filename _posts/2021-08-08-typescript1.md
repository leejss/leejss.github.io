---
layout: post
title: Typescript - type alias vs interface
date: 2021-08-08 13:20 +0900
tags:
  - Typescript
---

타입별칭(type alias)와 인터페이스(interface)는 거의 모든 부분에서 같은 기능을 한다.

하지만 분명 미묘한 차이는 있다.

### 차이 1. 타입별칭이 조금 더 포괄적이다.

타입 별칭은 어느 타입이든지 대상이 된다.

```ts
type Color = string;
type A = number | boolean;
type B = Color | A;
```

### 차이 2. extends vs intersection

```ts
interface Operation {
  add: (x: number, y: number) => number;
}

// compile time error
interface Calculator extends Operation {
  add: (x: number | string, y: number | string) => number | string;
}

/*
Interface 'Calculator' incorrectly extends interface 'Operation'.
  The types returned by 'add(...)' are incompatible between these types.
    Type 'string | number' is not assignable to type 'number'.
      Type 'string' is not assignable to type 'number'.ts(2430)

*/
```

`interface`와 `extends` 사용시, assignability를 체크한다.

```ts
type Operation = {
  add: (x: number, y: number) => number;
};

// No error. overload가 이루어 진다.
type Calculator = {
  add: (x: number | string, y: number | string) => number | string;
} & Operation;
```

### 차이 3. Declaration Merging

동일한 이름의 인터페이스 정의시, merging이 이루어 진다.

```ts
interface Product {
  price: number;
}

interface Product {
  name: string;
}

const p: Product = {
  price: 5000,
  name: "coffee",
};
```

반면 타입 별칭은 동일한 이름을 허락하지 않는다.

```ts
// Error
type Product = {
  name: string;
};

type Product = {
  price: number;
};

// Duplicate identifier 'Product'.
```
