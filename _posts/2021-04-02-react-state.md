---
layout: post
title: React의 state 정리
date: 2021-04-02 01:08 +0900
tags: React
---

## What is state

>In the React sense, “state” is an object that represents the parts of the app that can change.

state는 단순하게 말하면 객체다.

그렇다면 state에 어떤 데이터가 들어 있을까?

state는 컴포넌트 내에서 변할 수 있는 부분을 담는다.

컴포넌트 내에서 무언가를 한다면, 예를 들어 데이터를 추가하거나 삭제한다면 그 컴포넌트는 state를 가지고 있다.

state가 변하면 view도 변한다.(re-render)

그렇다면 리엑트는 어떻게 state가 변하는 것을 알까?

>React knows when state has changed because you tell it explicitly, by calling this.setState from inside a component.

함수를 이용하여 직접 리엑트에게 알려주기 때문이다.

## Two points

1. 항상 언제나 setState()를 이용하여 state를 바꿔야 한다. state를 직접 접근해서 바꾸면 안된다.

2. setState는 비동기적이다. 호출 이후, 바로 state값을 참조한다면 값이 반영이 안 돼있을 수 있다.

## Summary

state는 객체 형태로 존재한다.

setState()를 호출하여 state를 바꾼다.

state가 바뀌면 re-render한다.

## what's next?

컴포넌트는 각자 state를 가질 수 있다.

컴포넌트를 reusable하게 설계하려면 container와 presentational 컴포넌트를 분리하는 것이 좋다.

따라서 global한 state를 가지는 것이 설계에 유리한데 이를 가능하게 하는 것이 Redux다.

## 참고

[visual-guide-to-state-in-react](https://daveceddia.com/visual-guide-to-state-in-react/)
