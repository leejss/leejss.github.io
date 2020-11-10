---
layout: post 
title: Argument - Keywords vs Default argument - 1
categories: Python
tags: 
- TIL
- Python
---

## 1. Argument 유형

```python

# caller
func(arg1, arg2)
func(arg1=value1, arg2=value2)
func(*iterable)
func(**dict)

# function header
def func(name)
def func(name=value)
def func(*name)
def func(**name)
def func(*other, name)
def func(*, name=value)
```

## 2. Positional Argument vs Keyword Argument

함수에 인자(argument)를 전달할 때, 함수는 어떻게 인자를 식별할까?

### Matched by position - positional argument

```python

def func(a, b, c, d):
    print(a, b, c, d)

func(3, 2, 5 4) # Positional argument. it prints 3 2 5 4

```

간단하게 함수를 정의했고, 호출했다.

로컬 변수인 a b c d는 어떻게 정해질까?

함수를 호출할때 argumnet의 순서에 따라 변수가 연결할 객체가 정해진다.

괄호안에 3 2 5 4를 넣은 경우, a는 int형 객체인 3과 연결되고 b는 int형 객체인 2와 연결된다. (다음 변수도 마찬가지)

이런 식으로 position에 따라 결정되기 때문에 이를 positional argument라 한다.

그냥 `func(arg1, arg2, ...)` 의 가장 일반적인 형태의 arguemnt passing을 어렵게 말한 것이다.

(헷갈리지 않기: 함수를 정의하는 쪽이 아니라, **함수를 호출하는 쪽** 입장에서 인자를 넘겨주는 경우를 말한다.)

```python

def check(name, age)
    print(f'{name} is {age} years old')

check(10, "James") # 당연하게도 10 is James years old 라고 print한다.

# 인자를 전달하는 순서에 따라 로컬 변수가 정해지기 때문이다.

```

### Matched by keywords - Keyword Argument

Position에 상관없이 변수의 이름을 통해서 인자를 연결하는 방식이다.

매개변수의 이름을 직접 명시하여 객체를 전달한다.

`func(arg1=10, arg2=20, ...)`의 형태를 가진다.

```python

def check(name, age) # 위의 예제와 똑같은 함수다.
    print(f'{name} is {age} years old')

check(age=20, name="James") # keyword로 전달. print James is 20 years old

```

Keyword로 전달할 경우, 순서에 상관없이 로컬변수와 인자가 연결된다.

Keyword argument에서 keyword를 변수의 이름으로 자연스럽게 해석하면 된다.

## 3. Default Argument

`def (name=value)` 유형에 해당한다.

헷갈리지 말자. `(name=value)` 의 모습이 keyword argument와 같지만 defualt argument는 함수를 정의하는 입장에서 바라본 형태다. (keyword argument는 함수에 인자를 전달할 때에 한 형태에 해당)

```python

def my_sum(iterable, start=0):
    for n in iterable:
        start += n

my_sum([1,2,3,4]) # 10. matched by position
my_sum([1,2,3,4], 10) # 20. matched by position

my_sum(start=100, iterable=[1,2,3,4]) # 물론 keyword로도 전달 가능하다.

```

함수의 매개변수로 `iterable`과 `start`를 선언했다. 하지만 `start=0`으로 디폴트 값을 주었다.

디폴트 값은 인자를 생략하면 셋팅되는 값이다.

