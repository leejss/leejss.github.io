---
layout: post
title: useReducer 사용하기(with Typescript)
date: 2021-08-17 11:44 +0900
tags:
  - React
  - Typescript
---

## useReducer 살펴보기

### 언제 사용할까?

함수형 컴포넌트의 상태(state)를 다루기 위해서 `useState`를 사용한다. `useReducer`도 이와 비슷하게 컴포넌트의 상태를 다루기 위해 사용한다. 대신, 상태의 구조가 복잡한 경우 `useReducer`를 사용하면 유리하다.

예를 들어, 서버로부터 데이터를 가져오는 경우 어떤 이유로 인해서 실패할 수 있고 또한 좋은 유저 경험을 위해 데이터를 가져오는 중이라는 것을 유저에게 보여 줄 필요가 있다.

에러가 발생하는 경우를 생각해보자.

에러를 나타내기 위해서는 두 가지 상태값이 필요하다. 하나는 에러가 발생했는지 나타내는 `boolean` 타입 변수, 다른 하나는 어떤 에러가 발생했는지 보여주는 `string` 타입 변수.

물론 이를 `useState`로도 타나낼 수 있지만, 코드가 길어지고 다른 이름의 `setState`함수가 늘어나게 되는 단점이 있다.

이 때 `useReducer`를 사용하면 좀 더 깔끔하게 로직을 나타낼 수 있다.

### Function signature

`useReducer`의 function signature는 총 5가지가 있다. 그 중 대표적인 한 가지를 살펴보겠다.

```ts
function useReducer<R extends Reducer<any, any>>(
  reducer: R,
  initialState: ReducerState<R>
): [ReducerState<R>, Dispatch<ReducerAction<R>>];

type Reducer<S, A> = (prevState: S, action: A) => S;
```

`useReducer`의 첫번째 파라미터는 `Reducer`타입의 변수다.




