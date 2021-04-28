---
layout: post
title: Express에 Logger와 Debugger 셋팅하기
date: 2021-04-28 20:39 +0900
tags: Express Nodejs
---

## Logger

logger로 `morgan` 패키지를 이용할 것이다.

### 설치

```shell
npm install -D morgan
```

### import

```js
import logger from 'morgan'

// ...
app.use(logger('포맷'))
```

### Mount

인자를 통하여 log의 포맷을 정할 수 있다.

```js
app.use(logger('dev'))
app.use(logger('common'))
app.use(logger('combined'))
app.use(logger('tiny'))
```

이렇게만 설정할 경우 로그 내역이 콘솔화면(`process.stdout`)에 출력된다.

로그내역을 `.txt`파일로 만들고 싶으면 두번째 인자에 `stream`키를 가진 옵션 객체를 넣어준다.

```js
app.use(logger('common'), {
    stream: //writableStream
})
```

### log file 만들기

로그 파일도 만들고 rotate기능도 추가하기 위해 다음 패키지를 설치한다.

```shell
npm i -D rotating-file-stream
```

```js
import rfs from 'rotating-file-stream'
```

패키지를 이용하여 stream을 생성하고 옵션 객체의 `stream` 키의 값으로 할당한다.

```js
app.use(logger('common'), {
    stream: rfs.createStream("로그파일이름", 옵션객체)
})
```

옵션객체을 통해서 rotate를 설정한다.

```js
app.use(logger('common'), {
    stream: rfs.createStream("log.txt", {
        size: "10M", // 로그파일이 10mb일 경우, rotate log file
        interval: "1d", // or 하루가 지나면, rotate log file
        compress: "gzip" // 압축 설정
    })
})
```

로그파일 이름을 환경변수로 관리할 수 있다.

```js
app.use(logger('common'), {
    stream: process.env.LOG_FILE ? rfs.createStream(process.env.LOG_FILE, {
        size: "10M", // 로그파일이 10mb일 경우, rotate log file
        interval: "1d", // or 하루가 지나면, rotate log file
        compress: "gzip" // 압축 설정
    }) : process.stdout // 로그파일이름이 주어지지 않는 경우 콘솔에 출력
})
```

```bash
LOG_FILE=log.txt npm start
```

## Debugger

`debug` 패키지를 이용한다.

### 설치

```bash
npm i -D debug
```

### import

```js
import DBG from 'debug'
```

### 디버거 생성

다음과 같이 디버깅 함수를 생성한다.

```js
const debug = DBG("myapp:debug")
const error = DBG("myapp:error")
```

`DBG()`에 debug의 역할을 알려주는 specifier를 부여한다.

### 디버거 예시

```js
server.on("requset", (req, res) => {
    debug(`request ${req.method} ${req.url}`)
})
```

### 장점

`debug`가 하는 역할은 `console.log`와 크게 다르지 않다. 하지만 `DEBUG` 환경변수를 통해서 쉽게 메시징을 on & off 할 수 있다.

```bash
DEBUG=myapp:* node ./app.js # debug on

node .app.js # debug off
```
