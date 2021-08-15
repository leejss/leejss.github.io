---
layout: post
title: 그리드 shorthand 속성 이해하기
date: 2021-08-15 15:25 +0900
tags:
  - CSS
---

## grid-template

`grid-template` 속성은 `grid-template-rows`, `grid-template-columns`, `grid-template-areas`를 한번에 쓸 수 있도록 도와준다.

```css
.wrapper {
  display: grid;
  /* 2개의 rows, 3개의 columns인 grid를 생성한다. */
  grid-template: repeat(2, 1fr) / repeat(3, 1fr);
}
```

```css
.wrapper {
  display: grid;
  grid-template: /* grid-template-rows */ //* grid-template-columns */;;
}
```

여기에 더해 `grid-template-areas`까지 추가할 수 있다.

```css
.wrapper {
  display: grid;
  grid-template: "h h h" 64px "m m m" 1fr / repeat(3, 1fr);
}
```

위 shorthand를 풀어 쓰면 다음과 같다.

```css
.wrapper {
  display: grid;
  grid-template-rows: 64px 1fr;
  grid-template-columns: repeat(3, 1fr);
  grid-template-areas: "h h h" "m m m";
}
```

Named line도 포함할 수 있다.

```css
.wrapper {
  display: grid;
  grid-template: [rows-start] "h h h" 64px "m m m" 1fr [rows-end] / repeat(3, 1fr);
}
```

### More example

```css
/* Side nav */
.wrapper {
  display: grid;
  grid-template: 1fr / max-content 1fr;
}

/* Top nav */
.wrapper {
  min-height: 100vh;
  display: grid;
  grid-template: 4rem 1fr / 1fr;
}
```

## grid

`grid`는 `grid-template`보다 다양한 속성을 설정할 수 있다. `grid-template`는 explicit grid만 설정 가능하다면 `grid`는 explicit, implicit 둘 다 가능하다.

공식적으로 `grid`는 다음 값을 가진다.

1. <'grid-template'>
2. <'grid-template-rows'> / [auto-flow && dense?] <'grid-template-columns'>?
3. [auto-flow && dense?] <'grid-template-rows>? / <'grid-template-columns'>

### Option 1

`grid-template` 문법을 `grid`도 똑같이 사용할 수 있다.

```css
.wrapper {
  display: grid;
  /* <'grid-template-rows'> / <'grid-template-columns'> */
  grid: 100px 200px / repeat(3, 1fr);
}
```

### Option 2

```css
.wrapper {
  display: grid;
  /* <'grid-template-rows'> / [auto-flow && dense?] <'grid-template-columns'>? */
  grid: 100px / auto-flow;
  grid: 100px / auto-flow dense;
}
```

하나의 `row`와 `grid-auto-flow: column;`인 그리드를 형성한다.

`grid-auto-flow`는 그리드 아이템의 auto placement를 결정하는 프로퍼티다. `grid-auto-flow: column;`이면 그리드 아이템이 column 방향으로 배치된다.

### Option 3

grid-auto-flow를 row로 하고 싶으면 auto-flow를 슬래쉬 왼편에 위치하면 된다.

```css
.wrapper {
  diplay: grid;
  grid: auto-flow 100px / repeat(4, 1fr);
}
```

### Example

```css
.wrapper {
  display: grid;
  grid: [stack] 1fr / max-content [stack] 1fr;
}
```

named lines 때문에 어려워 보이지만 위 속성을 다음과 같이 바꿔서 작성할 수 있다.

```css
.wrapper {
  display: grid;
  grid-template-rows: 1fr;
  grid-template-columns: max-content 1fr;
}
```

하나의 row, 두 개의 columns인 그리드를 생성하게 된다.

여기에 naemd line을 추가해보자.

```css
.wrapper {
  display: grid;
  grid-template-rows: [stack] 1fr;
  grid-template-columns: max-content [stack] 1fr;
}
```

이것을 그대로 shorthand 속성으로 옮기면 다음 처럼 된다.

```css
.wrapper {
  display: grid;
  grid: [stack] 1fr / max-content [stack] 1fr;
  /* 또는 */
  grid-template: [stack] 1fr / max-content [stack] 1fr;
}
```
