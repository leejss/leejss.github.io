---
layout: post 
title: Python으로 cli app 만들기
tags: python
---

## CLI(Command Line Interface) application이란

Text를 이용하여 프로그램에 명령(Command)를 내려 상호작용하는 Application 또는 program  
CLI APP을 만들기 위해서는 Text를 커맨드로 바꾸는 Parsing이 필요하다.

## argparse

python에서는 cli를 만들기 위한 parser를 제공하고 있다.  
argparse 모듈을 이용하여 cli app을 만들 수 있지만 좀 더 직관적이고 쉬운 방법이 있다.  

## click

click패키지를 이용하면 쉽게 cli app을 만들 수 있다. [공식 click 홈페이지](https://click.palletsprojects.com/en/7.x/){:target="_blank"}는 click 패키지를 “Command Line Interface Creation Kit”라고 설명하고 있다. 패키지이름이 "click"인 이유인 것 같다.

### 1. virualenv 설정

현재 윈도우에서 WSL을 사용중이므로 [해당글](https://docs.microsoft.com/ko-kr/windows/python/web-frameworks#install-python-pip-and-venv){:target="_blank"}을 참고하여 가상환경을 설정한다.

### 2. setuptools을 이용한 설정

setuptools를 이용하여 script환경을 설정한다. click 공식 홈페이지에 나와 있는 [설명](https://click.palletsprojects.com/en/7.x/setuptools/){:target="_blank"}을 잘 따라하면 된다.  

setup을 설정하지 않으면 cli app을 실행할 때, script에 `if __name__ =="__main__":`  suite안에 `cli()`를 호출해줘야 하며 터미널에서 `python 이름.py` 이런 식으로 스크립트를 실행해야 한다.

그 외에도 click 공식 홈페이지에 setuptool를 사용해야 하는 이유를 3가지 제시하고 있다.

### 3. command 생성

click은 기본적으로 [decorator](https://realpython.com/primer-on-python-decorators/){:target="_blank"}를 이용한다. 이는 직관적으로 cli app을 만들 수 있게 해준다.

```python
import click

# command 생성
@click.commnad()
def cli():
    pass
```

`@click.command()` 를 이용하여 commnad를 바로 생성할 수 있다. 이 때 def 이름인 cli가 command의 이름이 된다.

shell에 cli -- help를 입력하면 help 메시지가 나오는 것을 확인할 수 있다. command를 만들면 click이 자동으로 help 메시지를 추가해준다.

### 4. option과 argument 생성

```python
import click

@click.command()
@click.option('--msg') # option 생성
def cli(msg):
    click.echo(msg)
```

옵션은 `@click.option()`을 이용하여 만들 수 있다. option은 optional argument로 command에 반드시 필요한 argument는 아니다. option의 값을 `cli(msg)`에서 옵션이름과 동일한 이름(여기서는 `msg`, `--`는 무시된다.)을 가진 파라미터로 전달받아 사용할 수 있다. `click.echo()`는 `print()`와 기능이 거의 똑같은데 더 높은 호환성을 가진다.

shell에서 `cli --msg hello`를 입력하면 `hello`를 출력한 것을 확인할 수 있다.

```python
import click

@click.command()
@click.option('--msg')
@click.argument('say') # argument추가
def cli(msg,say):
    click.echo(msg)
    click.echo(say)
```

위 와 같이 decorator를 이용하여 option과 argument를 동시에 추가할 수 있다.

argument는 option과는 다르게 command와 함께 명시하지 않으면 error가 발생한다.

`cli hello --msg world` 해당 command를 입력할 경우, `say`에는 `hello`가, `msg`에는 `world`가 각각 할당된다. 할당된 값을 `cli()` command 안에서 사용할 수 있다.

### 5. 기타 유용한 기능

click은 option과 argument를 추가하는 것을 넘어서 기타 유용한 옵션을 제공한다.

#### nargs, type

```python
@click.option('--num', nargs=2, type=int)
```

`type`을 이용하여 값의 타입을 지정할 수 있고, `nargs`를 이용하여 user로 부터 넘겨 받는 값의 개수를 정할 수 있다. 이때 값은 tuple 타입으로 전달된다.

#### is_flag

```python
@click.option('--show', is_flag = True)
def cli(show):
    if show: 
        # do something
```

`is_flag` 기능은 `show` 값이 bool 타입을 가지게 한다.

특정 기능을 on/off하는 경우 사용된다.

#### option name

```python
@click.option('-v', '--verbose')
```

옵션이름을 한 알파벳으로 줄이고 싶으면 위 처럼 두 개의 이름 인자를 주면 된다.

#### help message

```python
@click.option('--msg', help="Type message)
```

위와 같이 help 메시지를 직접 정할 수 있다.

#### callback

```python
def do_something(ctx, param, value):
    # do_something
    ctx.exit()



@click.option('--num', callback=do_something, expose_value=False):
def cli():
    pass
```

옵션에 callback함수를 주면 컨트롤의 흐름을 다른 함수로 전달할 수 있다. 이때 callback함수는 위와 같이 세 개의 인자를 받아야 한다.

#### prompt, confirm

```python

@click.command()
def cli():
    value = click.prompt("Enter integer", type=int)
```

user로부터 값을 입력받으려면 `click.prompt()`를 이용한다.

```python

@click.command()
def cli():
    click.confirm("Are you sure?")
```

user에게 confirmation message를 보여준다. y/N(True or False)값을 가진다.

#### group command

```python
@click.group()
def main():
    pass

@main.command():
def sub():
    pass
```

main command 안에 sub command를 둘 수 있다. 먼저 main command에 `@click.group()`을 주고 `@main.commnad()`로 sub command를 만들 수 있다.

## 도움이 되는 자료

- [Building Beautiful Command Line Interfaces with Python](https://codeburst.io/building-beautiful-command-line-interfaces-with-python-26c7e1bb54df){:target="_blank"}
- [Building Command Line Applications with Click](https://www.youtube.com/watch?v=kNke39OZ2k0){:target="_blank"}
