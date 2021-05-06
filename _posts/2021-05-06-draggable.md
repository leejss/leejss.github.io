---
layout: post
title: Drag to Scroll (with React)
date: 2021-05-06 14:08 +0900
tags: Javascript DOM
---

해당 포스트는 아래 내용을 기반으로 작성하였다.

[Drag to Scroll](https://htmldom.dev/drag-to-scroll){:target="_blank"}

## 이벤트 리스터 안에 addEventListener()

어떤 이벤트가 다른 이벤트에 의존하는 경우, 이벤트리스너를 따로 등록하는 것보다 이벤트 리스너 안에 `addEventListener()`를 사용하여 종속관계를 명확하게 할 수 있다.

```js
// Example) Modal

/*
    버튼을 클릭하면 Modal이 나타난다. 이 후 아무 키나 누르면 Modal이 사라진다.
    이를 구현하는 경우 이벤트 리스너를 따로 등록할 수 있다.
        Button.addEventListener("click", onClick)
        document.addEventListener("keydown", onKeyDown)
    
    이렇게 작성하는 경우보다

    버튼의 이벤트 리스너 안에 이와 관련된 이벤트를 등록한다.
*/

const onClick = (e) => {
    setModal(true)
    document.addEventListener("keydown", ()=> {
        setModal(false)
    })
}
```

## Drag to Scroll (horizontal)

드래그를 하여 element나 scroll을 특정 위치로 이동시키는 것은 다음 세 가지 마우스 이벤트를 이용한다.

- mousedown: 마우스(or any pointing device)를 element 영역에서 press했을 때, 발생.
- mousemove: 마우스를 element영역 위에서 움직이는 경우 발생.
- mouseup: 마우스를 element 영역 위에서 release한 경우 발생.

그리고 위치와 관련된 변수 두 가지가 필요하다.

- clientX: 마우스의 수평위치
- scrollLeft: 스크롤이 수평으로 이동한 거리

### Steps

1. mousedown 시, 해당 지점의 clienX, scrollLeft를 pos 객체에 저장하고 이벤트 리스너를 등록
2. mousemove 시, down 지점의 마우스 수평위치(pos 객체에 저장된 값)와 mousemove시 발생하는 clientX의 차이 계산 (e.clientX - pos.x)
3. down 지점의 scrollLeft(pos.left)에 차이를 뺀다.
4. mouseup시, 이벤트리스너를 해제한다.

### Code

```js
const ref = useRef(null)
// 위치를 저장
let pos = {
    left: 0,
    x: 0
}

const onMouseDown = e => {
    pos = {
        left: ref.current.scrollLeft,
        x: e.clientX
    }

    document.addEventListener("mousemove", onMousemove);
    document.addEventListener("mouseup", onMouseup);
}

const onMouseDown = e => {
    // 오른쪽으로 드래그시, dx는 음수값을 가진다.
    // 왼쪽으로 드래그시, dx는 양수값을 가진다.
    const dx = e.clientX - pos.x
    ref.current.scrollLeft = pos.left - dx
}

const onMouseUp = e => {
    document.removeEventListener("mousemove", onMousemove);
    document.removeEventListener("mouseup", onMouseup);
}

return (
    <div className="container" ref={ref}>
        <div className="box"></div>
        <div className="box"></div>
        <div className="box"></div>
        <div className="box"></div>
        <div className="box"></div>
        <div className="box"></div>
        <div className="box"></div>
    </div>
)
```

## Draggable function

draggable container를 만들기 위한 함수를 작성한다.

```js
export const draggable = (ref, document = window.document) => {
  let pos = {
    x: 0,
    left: 0,
    // y: 0,
    // top: 0,
  };
  const onMouseDown = (e) => {
    pos = {
      left: ref.current.scrollLeft,
      //   top: ref.current.scrollTop,
      x: e.clientX,
      //   y: e.clientY,
    };

    document.addEventListener("mousemove", onMousemove);
    document.addEventListener("mouseup", onMouseup);
  };

  const onMousemove = (e) => {
    const dx = e.clientX - pos.x;

    // const dy = e.clientY - pos.y;
    // ref.current.scrollTop = pos.top - dy;

    ref.current.scrollLeft = pos.left - dx;
  };

  const onMouseup = (e) => {
    document.removeEventListener("mousemove", onMousemove);
    document.removeEventListener("mouseup", onMouseup);
  };

  return ref.current.addEventListener("mousedown", onMouseDown);
};

```
