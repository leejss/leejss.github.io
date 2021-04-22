---
layout: post
title: scrollIntoView다루기
date: 2021-04-22 11:30 +0900
tags: Javascript
---

## scrollIntoView

```js
DOM요소.scrollIntoView(옵션)
```

DOM요소의 `scrollIntoView`를 호출하면 해당 요소로 스크롤이 이동한다.

EX) 클릭 시 맨 위로 올라가는 버튼을 만들기 (scroll to top)

```js

const scrollToTop = document.querySelector('.scroll-to-top')
const topDiv = doucment.querySelector('.top-div')

scrollToTop.addEventListener('click', e => {
    topDiv.scrollIntoView({
        block: "start",
        behavior: "smooth"
    })
})

```

EX) 해당 화면으로 이동하는 nav bar 만들기

```html

<nav>
    <ul class="navBar">
        <li class="nav-item">1</li>
        <li class="nav-item">2</li>
        <li class="nav-item">3</li>
    </ul>
</nav>
<div class="conatiner">
    <div class="conatiner-item">1</div>
    <div class="conatiner-item">2</div>
    <div class="conatiner-item">3</div>
</div>

```

```js
const navItems = Array.from(document.querySelectorAll(".nav-item"))
const containerItems = Array.from(document.querySelectorAll(".container-item"))
const navBar = document.querySelector(".navBar")

navBar.addEventListener("click", e => {
    const targetItem = e.target
    if (targetItem.className === "nav-item") {
        const index = navItems.indexOf(targetItem)
        containerItems[index].scrollIntoView({
            block: "start",
            behavior: "smooth"
        })
    }
})
```
