---
layout: post
title: 자바스크립트 기본기 - 불변객체(Immutable object)
date: 2021-08-07 23:09 +0900
---

### Data type

데이터 타입은 두 가지로 나뉜다.

1. Primitive type
   - Number
   - String
   - Boolean
   - null
   - undefined
   - Symbol
2. Reference type
   - Object

모든 Primitive Type은 불변성을 가진다.

### 불변성(Immutable)

한 번 생성한 값 메모리 상에서 바꿀 수 없다.

```js
var a = 1; // 메모리 내에서 1을 생성
a = 2; // 메모리 내에서 2를 생성. 1은 이 후 가비지 콜렉팅 대상이 된다.
```

### Reference Type

객체는 메모리 상에서 변수 영역을 가지고 있다.

이 변수 영역의 값이 바뀔 수 있기 때문에 가변적이다. (프로퍼티가 가리키고 있는 데이터는 Primitive이기 때문에 불변이다.)

```js
// 메모리내 변수영역의 빈 공간을 확보, 그 주소이름을 obj라 한다.
// obj는 별도의 변수 영역을 가지고 있다.
var obj = {
  // p1과 p2는 각각 데이터 영역의 주소를 포인팅한다.
  p1: "String", // 메모리의 데이터 영역에 "String"을 생성
  p2: 123, // 메모리의 데이터 영역에 123을 생성
};

obj.p1 = "Changed"; // 메모리내 obj는 그대로 이면서 메모리 내에서 "Changed"를 생성,
// p1이 해당 데이터를 가리킨다.
```

### 객체 복사

```js
var obj1 = {
  c: "A",
  d: 1,
};

var obj2 = obj1;

obj2.c = "B";

console.log(obj1); // { c: 'B', d: 1 }

// obj2는 obj1의 변수 영역에 대한 주소를 복사. 즉 두 식별자 모두 같은 주소를 가리키고 있다.
// 객체의 프로퍼티는 가변적이다.
```

### 불변객체

```js
var obj1 = {
  c: "A",
  d: 1,
};

var obj2 = {
  c: "A",
  d: 1,
};

console.log(obj1 === obj2); // false

// obj2는 새로운 변수 영역을 할당받았기 때문에 false
```

원본 객체를 유지하면서 객체의 프로퍼티를 바꾸기 위해서는 새로운 객체를 만들어 할당해야 한다.

### 얕은복사와 깊은복사

얕은 복사는 바로 아래 단계의 값만 복사한다. 따라서 객체의 프로퍼티가 만약 객체라면 해당 객체의 주소를 가지게 된다. 따라서 불변성이 깨지게 된다.

깊은 복사는 이와 다르게 객체의 프로퍼티가 객체라도 불변성을 유지한채 값을 복사한다.

### JSON을 이용한 깊은복사

```js
const obj1 = {
  b: {
    c: {
      d: {
        e: {
          number: 1,
        },
      },
    },
  },
};

function deepCopy(obj) {
  return JSON.parse(JSON.stringify(obj));
}

const obj2 = deepCopy(obj1);

console.log(obj1 === obj2); // false
```
