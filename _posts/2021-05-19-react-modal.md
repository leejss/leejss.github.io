---
layout: post
title: React Modal
date: 2021-05-19 03:38 +0900
tags: React
categories:
    - React
---

## react-modal 패키지를 이용하여 Modal 구현하기

### Install 'react-modal'

```shell
yarn add react-modal
```

## Modal 구현하기

### Case. Register 컴포넌트를 modal로 구현

```js
// Registration.jsx
import ReactModal from "react-modal"

ReactModal.setAppElement("#root");

const Register = ({ isOpen, onClickToggle }) => {
  return (
    <ReactModal isOpen={isOpen} onRequestClose={onClickToggle}>
      <form>
        <div>
          <label>username</label>
          <input type="text" />
        </div>
        <div>
          <label>password</label>
          <input type="password" />
        </div>
      </form>
    </ReactModal>
  );
};

export default Register;
```

`isOpen`: ReactModal의 display를 결정하는 `boolean` 값  
`onRequestClose`: close 요청시, 호출할 함수  

### Modal toggle기능 구현하기

```jsx
import { useState } from "react";
import Register from "./Register";
import "./styles.css";

export default function App() {
    // React modal의 display를 토글하는 boolean 값
  const [showRegister, setShowRegister] = useState(false);
  // click시, showRegister값을 toggle한다. 
  const onClickToggle = (e) => {
    setShowRegister(!showRegister);
  };

  return (
    <div className="App">
      <span onClick={onClickToggle}>Register</span>
      <Register isOpen={showRegister} onClickToggle={onClickToggle} />
    </div>
  );
}

```

`react-modal`로 구현한 modal컴포넌트인 `Register`에 `props`로 `showRegister`과 `onClickToggle`을 전달한다.  
부모컴포넌트에서 modal의 display를 컨트롤할 수 있도록 한다.

## Link

[React Modal Example](https://codesandbox.io/s/recursing-robinson-0fqp4?file=/src/App.js)
