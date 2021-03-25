---
layout: post 
title: iterable과 iterator
tags: python
---

## 1. Iteration

Python에서 iteration은 scanning과 selecting의 결합이다.

object를 왼쪽에서 오른쪽으로 scanning하여 값을 하나씩 selecting하는 것을 말한다.

가장 기본적인 iteration은 `for` loop을 통해 구현할 수 있다.

iteration의 대상은 `iterable object` 이며 `list`, `tuple`, `str` 같은 sequence type이 여기에 포함된다.

```python
for x in [1,2,3,4]:
    print(x, end=" ")
```

`for` loop 을 수행할 경우 내부적으로 `iter`를 호출하여 `iterator`를 반환하고 `iterator`의 `__next__()`를 호출한다.

대상이 `iterator`일 경우 `__next__`를 바로 호출한다.

## 2. `iterable`

Python에서는 `iterable`과 `iterator`가 의미하는 것이 다르다.

`iterable` 은 `iter()`의 매개변수 또는 `__iter__(self)`를 호출할 수 있는 객체다.

```python
>>> help(iter)
Help on built-in function iter in module builtins:

iter(...)
    iter(iterable) -> iterator
    iter(callable, sentinel) -> iterator
    
    Get an iterator from an object.  In the first form, the argument must
    supply its own iterator, or be a sequence.
    In the second form, the callable is called until it returns the sentinel.

```

## 3. `iterator`

`iter`의 호출로 반환값이 `iterator`다.

`iterator`는 `__next__`를 호출할 수 있는 객체다.

`iterator`는 값을 모두 메모리에 적재하는 것이 아니라 한번에 하나씩 값을 가져온다.

`__next__()`를 호출하여 값을 하나씩 가져오고 `StopIeration`을 catch 하면 호출을 중단한다.

```python
# file iterator를 이용하여 line 가져오기.
for line in open("text.txt"):
    print(line, end='')
```

최근 Python에서 `dict`는 내부적으로 `iterator`를 가지고 있다.
그래서 다음 스타일의 코딩이 가능하다.

```python
D = {
    'name': 'Smith',
    'age': 21
}
# D.keys()가 아닌 직접 D를 for loop의 대상으로 삼을 수 있다.
# 변수 k에는 key가 할당된다.
for k in D:
    print(k, D[k])
```

## 4. 그 외 iteration tool

iteration tool은 `for` 뿐만이 아니다.

`map()`, `reduce()`, `comprehension`, `zip()`, `enumerate()`, `filter()`, `sorted()` 등 다양한 iteration tool이 존재한다.
