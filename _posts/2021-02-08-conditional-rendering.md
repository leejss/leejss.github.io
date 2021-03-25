---
layout: post 
title: React - 조건부 렌더링
tags: React
---

## 조건부 렌더링이란

조건부 렌더링이란 특정 조건에 따라서 화면에 보여지는 부분을 다르게 렌더링 하는 것을 말한다.

React 컴포넌트에서 화면에 보여지는 부분을 구성하는 것은, `render()`함수의 `return` 부분이다.

특정 변수의 값에 따라서 렌더링 하기 위한 방법 중 간단한 방법은 `&&` 연산자를 이용한다.

    {변수 && UI부분}

변수값이 true면 UI부분을 렌더링 하고 false면 렌더링하지 않는다.

## Example

플래그 변수에 따라서 화면을 visible/invisible 하게 바꾸는 버튼

```jsx

// import 생략

import React, { useState } from "react";

const Sample = () => {
  const [msg, setMsg] = useState("Message");
  const [flag, setFlag] = useState(true);
  const handleClick = () => {
    setFlag(!flag);
  };

  return (
    <div>
      {flag && <h1>{msg}</h1>}
      <button onClick={handleClick}>Switch</button>
    </div>
  );
};

export default Sample;

```

