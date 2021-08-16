---
layout: post
title: useSelector with Typescript
date: 2021-08-16 16:27 +0900
tags:
  - React
  - react-redux
  - Typescript
---

## Option 1. Typing a state type manually

```ts
// Inferred from rootReducer
export type AppState = ReturnType<typeof rootReducer>

// Explicitly set state type
const user = useSelector(state: AppState => state.user)
```

## Option 2. Define Typed Hook

`TypedUseSelectorHook` 이용

```ts
import { TypedUseSelectorHook } from "react-redux";
// Define typed hook
export const useAppSelector: TypedUseSelectorHook<AppState> = useSelector;

// 사용
const user = useAppSelector((state) => state.user);
```
