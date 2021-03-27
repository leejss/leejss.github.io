---
layout: post 
title: AbortController API 활용
tags: TIL
---

## AbortController Web AI

요청을 취소할 수 있는 인터페이스를 제공한다.

```javascript
// Creates a new AbortController object instance.
const abortController = new AbortController()
```

### AbortController.signal

DOM request의 요청과 communication할 수 있는 객체

### AbortController.abort()

DOM request를 abort(or cancel) 한다.

abort()가 호출되면, AbortError가 발생한다.

## 사용

### 1. GET request를 abort하는 버튼을 생성

```javascript
const abortController = new AbortController()
const signal = abortController.signal

const abortBtn = document.querySelector('.abort')

abortBtn.addEventListener('click', () => {
    abortController.abort()
    console.log('Download aborted');
})

const fetchVideo = async () => {
    try {
        const response = await fetch(url, {signal})
    } catch (err) {
        console.error(err)
    }
}

```

### 2. React에서 컴포넌트 unmount시, abort() 호출

```jsx
// ...
const User = (props) => {
    // ...
    useEffect(() => {

        return () => {
            // unmount시 요청 중단
            abortController.abort()
        }
    })
}

```
