---
layout: post
title: Inersection Observer API를 활용한 Infinite Scroll
date: 2021-04-26 22:29 +0900
tags: Javascript
---

출처: [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API){:target="_blank"}

## Intersection Observer API

>The Intersection Observer API lets code register a callback function that is executed whenever an element they wish **to monitor enters or exits another element** (or the viewport), or when the amount by which the two intersect changes by a requested amount.

target element가 root element (or viewport)와 intersecting하면 호출할 함수를 등록해주는 API.

```js
let observer = new IntersectionObserver(callback, options);
```

`IntersectionObserver` 클래스를 통해서 callback를 등록한다.

callback 함수는 다음 두 가지 경우에 호출된다.

>1. A target element intersects either the device's viewport or a specified element. That specified element is called the root element or root for the purposes of the Intersection Observer API.
>2. The first time the observer is initially asked to watch a target element.

## Intersection observer options

callback이 호출되는 환경을 구성할 수 있는 옵션을 설정한다.

```js

const options = {
    root: // target요소의 visibility(or intersection)를 결정할 root element. null일 경우 viewport로 설정
    rootMargin: // Margin around the root. 단위는 px 또는 %다. root 요소의 bounding box에 영향을 준다.
    threshold: // Either a single number or an array of numbers which indicate at what percentage of the target's visibility the observer's callback should be executed.
}

let observer = new IntersectionObserver(callback, options);
```

`threshold`는 target 요소의 visibility의 범위를 설정하는데, 예를 들면 `threshold`에 0.5를 주면 target 요소가 viewport에 50%정도 보이는 순간 callback이 호출한다.

`threshold`의 디폴트 값은 0인데, 이는 타겟이 뷰포트에 1픽셀이라도 보이면 callback이 호출된다는 뜻이다.

`threshold`값이 1인 경우는, 타겟 요소가 뷰포트에 전부 보여야 콜백함수가 호출된다는 뜻이다.

`threshold`값에 숫자 배열을 줄 수 있다. 이는 콜백함수가 호출되는 지점이 여러 개라는 뜻이다.

```js
const options = {
    threshold: [0, 0.25, 0.5, 0.75, 1] // 타겟 요소가 viweport에 25%씩 보일 때마다 콜백함수가 호출된다.
}
```

## Targeting an element to be observed

`new`를 통해서 `IntersectionObserver` 인스턴스를 생성했으면 observe할 target 요소를 설정하면 된다.

```js
let target = document.querySelector('#listItem');
observer.observe(target); // 이때 콜백함수가 최초 실행된다.
```

## callback 함수

```js
new IntersectionObserver(callback, options)
```

생성자에 등록한 callback함수는 `IntersectionObserverEntry` 객체 배열과 `observer`를 인자로 받는다.

`IntersectionObserverEntry` 객체 배열은 다음과 같은 target 요소의 정보를 담은 객체를 요소로 가지고 있다.

```js
let callback = (entries, observer) => {
  entries.forEach(entry => {
    // Each entry describes an intersection change for one observed
    // target element:
    //   entry.boundingClientRect
    //   entry.intersectionRatio
    //   entry.intersectionRect
    //   entry.isIntersecting
    //   entry.rootBounds
    //   entry.target
    //   entry.time
  });
};
```

복잡하게 생각할 것 없이 여기서 주목해서 봐야할 것은 `isIntersecting` 값이다.

해당 값은 `boolean`값을 가진다.

다음과 같이 할당문법을 통해 값을 가져올 수도 있다.

```js
const callback = ([{isIntersecting}]) => {
    if (isIntersecting) {
        // do somthing
    }
}
```

## Infinite Scroll

`IntersectionObserver` API를 활용하면 Infinite Scroll이 간단해진다.

원래 scroll의 위치를 계산해서 데이터를 호출할 지점을 계산해야 한다.

이러한 방법은 코드가 복잡해지고 효율도 떨어진다.

`IntersectionObserver`를 활용한다면 타겟 요소를 하나 정해놓고 해당 타겟 요소가 intersecting한다면 데이터를 fetch하는 코드를 쉽게 만들 수 있다.

## Example

```html
<!-- 생략 -->
        <div id="bottom"></div> <!-- body 맨 밑에 위치 -->
    </body>
</html>
```

```js
const bottom = document.querySelector("#bottom")
async function fetchData () {
    // fetchData

}
const observer = new IntersectionOberver(([{isIntersecting}]) => {
    if (isIntersecting) {
        fetchData()
    }
})

observer.observe(bottom)
fetchData()
```
