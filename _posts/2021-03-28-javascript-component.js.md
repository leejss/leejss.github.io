---
layout: post 
title: HTML Element componet by vanilla javascript
tags: Javascript
---

## Idea

자바스크립트의 class 문법을 이용하여 Element Component를 만든다.

- entry point인 `main.js`를 정의
- `element.js`를 정의
- `main.js`에서 element instance를 생성
- `main()`을 호출

## Implementation

### main.js

```html
<!-- index.html -->
<script type="module" src="./src/main.js"></script>
```

```javascript
// main.js
import {Btn} from "./Btn.js"

const main = () => {
    // 요소가 렌더링할 데이터
    const elemData = ["로그인", "회원가입", "설정"]
    // 클래스 인스턴스 생성 => html 생성
    // config 객체를 생성자에 전달한다.
    new Btn({
        // 요소가 담길 parent 요소
        selector: '#main-container',
        data: elemData,
        // 단순히 인덱스를 콘솔에 출력하는 콜백함수
        cb: (index) => {
            console.log(index)
        }
    })
}

main()
```

### Btn.js

버튼 요소를 생성하는 클래스를 정의한다.

```javascript

export class Btn {
    constructor(config) {
        this.selectedIndex = -1
        this.cb = config.cb
        this.$buttons = this.init(config.selector, config.data)

        this.eventBinding()
    }

    init(selector, data) {
        // 요소를 생성하고 HTML을 생성한다.
        const parentElem = document.querySelector(config.selector)

        let renderStr = ''
        // 전달받은 데이터를 순회하여 동적으로 요소를 생성한다.
        for (const str of data) {
            renderStr += `
                <button class="btn">
                    <span>${str}</span>
                </button>
            `
        }
        // innerHTML을 이용하여 생성한 html을 셋팅한다.
        parentElem.innerHTML = renderStr

        return document.querySelectorAll('.btn')
    }

    eventBinding() {

        // 생성된 요소에 이벤트리스너를 등록한다.
        this.$buttons.forEach((btn, index) => {
            btn.addEventListener('click', () => {
                // click 이벤트 리스너를 등록한다.
            })
        })

        // 전달받은 cb를 호출한다.
        this.cb()
    }
}
```
