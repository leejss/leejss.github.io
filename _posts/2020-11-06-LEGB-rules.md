---
layout: post 
title: LEGB rule
categories: Python 
tags: 
- TIL
- python
---

## Scope

Scope란 변수가 사는 공간이다.

변수는 scope를 벗어나면 소멸하게 된다.

변수의 scope는 변수에 처음 대입(assignment)연산이 이루어진 곳으로 정해진다.

```python
# beginning of module

x = 1

def func():
    x = 1

```

## LEGB rule

이름을 찾는 과정을 나타낸 규칙을 말한다.

LEGB는 각각 scope를 나타낸다.

L: Local

E: Enclosing functions(defs)

G: Global

B: Builtins

이름을 찾을 때, 같은 scope부터 찾으며 동일한 이름이 없는 경우 상위 스코프로 탐색을 이어 나간다.

Local: 함수 안에서 선언된 변수의 스코프

```python

def func():
    # 이름 x는 local scope에 살게 된다.
    x = 1

```

Enclosing functions: local보다 상위 이면서, global보다는 하위인 scope다.

```python
def outer():
    x = 10 # enclosing functions scope
    def inner():
        y = 10 # local scope
```

Global: function 밖인 동시에 module안의 scope

```python
x = 10 # global variable
def func():
    x = 10 # local vairable

# 위에 보이는 두 x는 같은 객체가 아니다.
# 스코프가 다르기 때문.
```

이름을 찾을 때, 같은 스코프 부터 LEGB 순으로 이름을 찾는다.

## `global` 과 `nonlocal`

변수이름 앞에 `global`과 `nonlocal`를 표시함으로써 찾아야할 scope를 지정할 수 있다.

```python
x = 10
def func():
    global x # x는 global scope에 있는 x를 가리킨다.
    x = 20
func()
print(x) # 20
```

`nonlocal`은 enclosing def의 scope를 가리킨다.

```python
def maker():
    count = 0
    def func():
        nonlocal count
        # count는 local variable이 아닌 enclosing func인 maker의 영역의 count를 가리킨다.
        count += 1
        print(count)
    return func

foo = maker()
foo() # 1
foo() # 2
foo() # 3

```
