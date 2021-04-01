---
layout: post
title: flux-pattern
date: 2021-04-01 21:24 +0900
tags: React Redux
---

## keywords

- Design Pattern
- Redux
- Web application architecture

## Flux

Flux는 디자인 패턴 또는 웹 어플리케이션 아키텍처 중 하나다.

전통적인 아키텍처 중 하나인 MVC와 다르게 단방향데이터 흐름을 가지고 있다.

### 이해를 돕기위한 그림

![flux2](https://haruair.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)
![flux1](https://github.com/facebook/flux/raw/master/img/flux-diagram-white-background.png)

그림에서 알 수 있듯이 Flux 구조는 크게 4가지로 이루어져 있다.

- Action
- Dispatcher
- Store
- View

### 간단한 설명

유저와 상호작용으로 인해 View에서 action을 발생시킨다. (View dispatch actions)

Store는 disaptcher를 통해서 action을 받는다. (Store receive actions)

전달받은 action을 통해서 state를 변화시킨다.

View는 변화한 state를 감지하여 re-render한다.

## Flux implemantaitons

이러한 Flux 구조를 우아하게 구현한 것이 바로 Redux다.
