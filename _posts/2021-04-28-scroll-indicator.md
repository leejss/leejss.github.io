---
layout: post
title: Scroll Indicator
date: 2021-04-28 01:40 +0900
tags: Javascript
---

## scrollTop, scrollHeight, clientHeight

### scrollTop

>An element's scrollTop value is a measurement of the distance from the element's top to its topmost visible content.

```js
element.scrollTop // return the number of pixels scrolled.
```

root element인 `html`의 `scrollTop`을 계산하는 경우, `window`의 `scrollY`를 리턴한다.

```js
document.docuemntElement.scrollTop === window.scrollY // true
```

### scrollHeight

>The Element.scrollHeight read-only property is a measurement of the height of an element's content, including content not visible on the screen due to overflow.

요소의 padding을 포함한 content의 총 높이.

border, 수평 스크롤바의 높이는 포함하지 않는다.

```js
element.scrollHeight

document.documentElement.scrollHeight // html요소의 전체 높이
```

scroll bar가 맨 밑에 도달했는지 check하기

```js
const {scrollHeight, scrollTop, clientHeight} = document.documentElement
Math.round(scrollHeight - scrollTop) === clientHeight ? "맨밑" : ""
```

### clientHeight

```js
element.clientHeight // read only
```

element의 inner height을 반환한다.

css의 `height` + `padding` - scroll bar height과 같다.

`html`요소의 clientHeight는 viewport의 height과 같다.

```js
document.documentElement.clientHeight // viewport height
```

## Scroll Indicator

scroll indicator란 scroll의 위치를 추적하여 bar형태로 나타내는 UI 요소다.

현재 스크롤의 위치에 따라 bar의 `width`도 같이 동적으로 변해야 한다.

scroll 이벤트를 이용해야 하므로 window 객체에 이벤트 리스너를 등록한다.

```js

window.addEventListener("scroll", e => {
    // code here
})

```

`scrollTop`, `scrollHeight`, `clientHeight` 값을 `html` 요소로부터 가져온다.

```js
window.addEventListener("scroll", e => {
    // code here
    const { scrollTop, scrollHeight, clientHeight } = document.documentElement;
})
```

현재 viewport의 height을 제외한 나머지 content 부분의 height을 계산한다.

```js
window.addEventListener("scroll", e => {
    // code here
    const { scrollTop, scrollHeight, clientHeight } = document.documentElement;
    const remainHeight = scrollHeight - clientHeight;
})
```

`scrollTop`값이 `remainHeight`값과 같게되는 지점이 스크롤이 맨 밑에 도달한 상태다.

즉, `scrollTop`과 `remainHeight`값의 비율이 1:1이 되는 순간이다.

`scrollTop`과 `remainHeight`값의 비율을 이용하여 scroll indicator의 `translateX`값을 동적으로 부여한다.

```js
window.addEventListener("scroll", e => {
    // code here
    const { scrollTop, scrollHeight, clientHeight } = document.documentElement;
    const remainHeight = scrollHeight - clientHeight;
    const ratio = scrollTop / remainHeight; //
    scrollBar.style.transform = `translateX(-${100 - 100 * ratio}%)`
})
```
