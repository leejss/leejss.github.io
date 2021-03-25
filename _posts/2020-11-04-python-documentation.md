---
layout: post 
title: 궁금한 거 파이썬에게 직접 물어보기(Python Documentation Sources)
tags: python
---

## 1. `dir` function

Python interpreter(shell)에 `dir()` 를 입력하면 caller의 스코프에 있는 변수들의 list를 보여준다.

```python
>>> x = 1
>>> name = "python"
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__',
'__name__', '__package__', '__spec__', 'name', 'x']
>>>
```

`dir()` 의 매개변수로 built-in type이나 object를 넘겨주면 해당 타입의 attributes의 list를 볼 수 있다.

```python
>>> dir(str)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
>>> 
>>> dir(dict)
['__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
```

**List comprehensions**를 통해서 간단하게 double underscore(`__`)를 가지지 않은 attributes의 list를 볼 수 있다.

```python
>>> [a for a in dir(list) if not a.startswith('__')]
['append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
>>> [a for a in dir(dict) if not a.startswith('__')]
['clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
```

## 2. `__doc__`(doctstring)

\# comment가 statement나 expression 단위의 documentation이라면, docstring은 function, class, module 단위의 documentation이다.

코드가 시작하기 전에 주로 `'''` (multi line string) 사이에 작성한다.

쓰여진 doctstring은 해당 function, class 또는 module의 `__doc__` 속성에 저장된다.

Python shell에서 직접 `__doc__`을 통하여 docstring을 볼 수 있다.

```python
>>> import math
>>> print(math.__doc__)
This module provides access to the mathematical functions
defined by the C standard.
>>> import random
>>> print(random.__doc__)
Random variable generators.

    integers
    --------
           uniform within range

    sequences
    ---------
           pick random element
           pick random sample
           pick weighted random sample
           generate random permutation

    distributions on the real line:
    ------------------------------
           uniform
           triangular
           normal (Gaussian)
           lognormal
           negative exponential
           gamma
           beta
           pareto
           Weibull

    distributions on the circle (angles 0 to 2pi)
    ---------------------------------------------
           circular uniform
           von Mises

General notes on the underlying Mersenne Twister core generator:

* The period is 2**19937-1.
* It is one of the most extensively tested generators in existence.
* The random() method is implemented in C, executes in a single Python step,
  and is, therefore, threadsafe.
```

함수의 docstring도 직접 볼 수 있다.

```python
>>> print(random.shuffle.__doc__)
Shuffle list x in place, and return None.

        Optional argument random is a 0-argument function returning a
        random float in [0.0, 1.0); if it is the default None, the
        standard random.random will be used.
```

## 3. `help` function

Pydoc의 인터페이스에 속한다.

docstring에 해당하는 정보를 좀 더 readable하게 보여준다.

`help()`의 매개변수로 모듈, 클래스, 함수를 전달한다. 매개변수의 정보를 보여준다.

```python
>>> help(math.factorial)
Help on built-in function factorial in module math:

factorial(x, /)
    Find x!.
    
    Raise a ValueError if x is negative or non-integral.
```

## 4. Python Module Docs

`help`가 Pydoc의 command-line 버전이라면, python module docs는 GUI 버전이다.

시작 프로그램의 python 설치폴더에 들어가면 찾을 수 있고, command prompt에서 접근할 수 있다.

```python
PS C:\Users\pc> python -m pydoc -b
```

## 5. IDEL help menu

python IDLE을 띄우고 `f1`을 누르면 Python documentation 페이지를 볼 수 있다.
