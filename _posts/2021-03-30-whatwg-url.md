---
layout: post 
title: The WHATWG URL API (Node.js 모듈)
tags: Nodejs
---

## keyword

- URL
- Parsing

## url 모듈

`url.resove()`을 사용하려고 하니 터미널에서 deprecated 되었다고 알려준다.

그 대신 WHATWG URL API을 사용하라고 한다.

공식 docs에서 해당 내용을 알아보았다.

## WHATWG URL API

먼저 URL을 로드한다.

```javascript
const {URL} = require("url")
```

url parsing을 지원하는 URL 객체를 생성한다.

`new URL(input[, base])`

```javascript

const myURL = new URL("https://www.naver.com/")
console.log(myURL)

/*
URL {
  href: 'https://www.naver.com/',
  origin: 'https://www.naver.com',
  protocol: 'https:',
  username: '',
  password: '',
  host: 'www.naver.com',
  hostname: 'www.naver.com',
  port: '',
  pathname: '/',
  search: '',
  searchParams: URLSearchParams {},
  hash: ''
}
*/
```

파싱 내용을 객체에 담아 리턴한다.

객체의 다양한 키 값을 통해서 파싱내용에 접근할 수 있다.