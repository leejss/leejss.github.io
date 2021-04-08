---
layout: post
title: esm 패키지를 이용하여 commonjs를 esm으로 변환하기 
date: 2021-04-08 10:50 +0900
tags: esm commonjs
---

## commonJS

자바스크립트의 모듈 시스템을 지원하기 위해 만들어졌다.

Nodejs의 모듈 환경

`require()`와 `module.exports` (또는 `exports`)가 등장하면 commonJS다.

```javascript
const express = require('express')
const app = express()

exports.add = (a, b) => a+b 

module.exports = app
```

## esm

es6부터 도입한 모듈 시스템

자바스크립트의 표준 모듈 시스템이다.

`import`와 `export` (또는 `export default`)를 사용한다면 esm 모듈 환경이다.

```javascript
import React from 'react'
//..

export getUser = () => {}

export default App
```

## esm 패키지

[esm 소개](https://medium.com/web-on-the-edge/tomorrows-es-modules-today-c53d29ac448c)

[esm - npm](https://www.npmjs.com/package/esm)

commonjs 코드를 esm 코드로 바꾸기 위해서 로더가 필요한데,

esm 패키지가 그 역할을 한다.

>esm is the world’s most advanced ECMAScript module loader.

## 사용하기

```bash
npx create-esm -y
```

위 커맨드를 입력하면 esm 패키지가 적용된 프로젝트가 생성된다.

서버 사이드에서 esm을 사용할 수 있게 한다.

### 기존 프로젝트를 esm으로 바꾸기

먼저 esm을 설치한다.

```bash
npm install esm
yarn add esm
```

프로젝트의 기존 entry인 `index.js` 파일명을 `main.js`로 바꾼다.

`index.js`를 다음과 같이 작성한다.

```javascript
// index.js
// Set options as a parameter, environment variable, or rc file.
require = require("esm")(module/*, options*/)
module.exports = require("./main.js")
```

프로젝트에서 esm을 사용할 준비가 되었다.

```javascript
import express from 'express'
const app = express()

// ...

export default app
```
