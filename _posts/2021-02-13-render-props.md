---
layout: post 
title: React - Render props
---

## Sharing data with other components

부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달하기 위해서 `props`를 사용한다

~~~jsx

class Parent extends Component { 
    construcotr(props) {
        super(props);
        this.state = {x: 10, y:20}
    }

    render() {
        return (
            <Child cord={this.state}>
        )
    }
}
~~~

## Render props

'리엑트를 다루는 기술'이라는 책을 읽던 중 `render props`에 대해 나왔다.

단번에 이해가 되지 않아서 공식 문서를 참고해 공부해보았다.

먼저 `render props`는 *render props technique* 또는 *render props pattern*을 말하는 것으로

여기서 `render`라는 이름은 관습적으로 지칭하는, 함수를 받는 `props`객체 안 필드를 말한다. (라이프 사이클 함수인 render()와 같은 것이 아니다. render()는 메소드이고 `render props`의 `render`는 props필드 이름이다.)

다음 코드를 보면 무슨 말인이 조금 이해가 갈 것이다.

~~~jsx
// ParentComponent.js
import React from "react";
import ChildComponent from "./ChildComponent";

const ParentComponent = () => {
  return (
    <div>
      <ChildComponent render={(number) => number + 2} />
    </div>
  );
};

export default ParentComponent;

~~~

~~~jsx
// ChildComponent.js
import React from 'react';

const ChildComponent = ({render}) => {
    return (
        <div>
            {render(2)} {/* 렌더링 시 화면에 4가 출력된다.*/}
        </div>
    );
};

export default ChildComponent;
~~~

사실 보면 알겠지만 함수를 받기 위해 render라는 이름을 굳이 안써도 된다. 하지만 render라고 쓰는 이유는 해당 props의 역할을 명확하게 하기 위해서 인 것 같다.

## 언제 쓰는 것인가?

다른 컴포넌트에 함수를 넘겨주는 경우는 어떤 경우인가?

다음 코드를 살펴보자 (지금부터 나오는 코드는 React의 공식문서에 나와 있는 render props예시다.)

~~~jsx

// Mouse.js ------------------------------------
import React, { Component } from "react";

class Mouse extends Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  // 마우스의 움직임을 추적해서 state에 업데이트 한다.
  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    });
  }

  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {/* 함수의 인자로 state객체를 넘긴다. */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

export default Mouse;
~~~

~~~ jsx
// MouseTracker.js ------------------------------------
import React, { Component } from "react";
import Mouse from "./Mouse";

class MouseTracker extends Component {
  render() {
    return (
      <>
        <h1>Move the mouse around!</h1>
        <Mouse render={(mouse) => {}} /> {/* 이런 식으로 함수가 전달 된다. */}
      </>
    );
  }
}

export default MouseTracker;

~~~

`Mouse` 컴포넌트는 마우스의 움직임을 추적하여 state객체에 값을 업데이트한다. 그리고 그 값을 `props`로 받는 `render`라는 함수에 인자로 넘긴다.

`render props`를 이용하여 다른 컴포넌트에서 `Mouse` 컴포넌트에서 구현한 기능(마우스 추적기능)을 사용할 수 있다.

다음 코드를 살펴보자

~~~jsx
// ReactLogo.js
import React, { Component } from "react";

class ReactLogo extends Component {
  render() {
    const mouse = this.props.mouse;
    const style = { position: "absolute", left: mouse.x, top: mouse.y}
    return (
      <img
        src="/logo192.png" // react logo
        style={style}
        alt="logo"
      />
    );
  }
}

export default ReactLogo;
~~~

위 컴포넌트는 특정 absoluti position에 react 로고를 띄우는 컴포넌트다. `mouse`라는 props을 받고, 그 객체의 x와 y를 이용하여 로고의 위치를 계산한다. 위 컴포넌트와 `mouse`컴포넌트의 기능을 결합하기 위해서 render props 테크닉을 이용한다.

MouseTracker.js를 다음과 같이 수정한다.

~~~jsx
// MouseTracker.js ------------------------------------
import React, { Component } from "react";
import Mouse from "./Mouse";
import ReactLogo from './ReactLogo';

class MouseTracker extends Component {
  render() {
    return (
      <>
        <h1>Move the mouse around!</h1>
        <Mouse render={(mouse) => {<ReactLogo mouse={mouse} />}} /> 
      </>
    );
  }
}

export default MouseTracker;
~~~

마우스 추적 기능을 가진 `Mouse` 컴포넌트에 그 기능을 이용하여 화면에 렌더링을 수행할 다른 컴포넌트를 결합한다.

`props`에 함수를 전달함으로써 무엇을 렌더링 할 것인지 결정할 수 있다.

## children 을 이용한 render props

다음과 같은 형태도 render props이다.

~~~jsx
// import 생략

const MyComponent = ({children}) => {
  return (
    <div>
      {children(2)}
    </div>
  )
}


<MyComponent>{value => 2 * value}</MyComponent>
~~~

`MyComponent`는 컴포넌트 사이에 함수를 받아 인자로 2를 넘긴다.

컴포넌트 사이에 `{value => 2 * value}`는 인자에 2를 곱해서 반환하는 함수를 넣는다.

해당 함수는 props.children을 통해서 MyComponet로 넘어가서 계산을 수행하여

~~~html
<div>
  4
</div>
~~~

로 computed된다.

