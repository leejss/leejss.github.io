---
layout: post
title: useEffect의 infinite loop에서 탈출하기
date: 2021-07-01 15:09 +0900
tags: React
---

## React Render는 언제 발생할까?

리액트 컴포넌트는 state와 props의 데이터를 통해서 UI를 구성한다. 따라서 state, props의 값이 바뀌면 Re-render가 발생한다.

state가 변경하면 UI업데이트가 이루어지고 리렌더링이 발생하기 때무네 useState가 반환하는 State 업데이트 함수를 호출하면 컴포넌트는 다시 호출되고 렌더링한다.

## useEffect와 Infinite Loop

useEffect의 콜백 함수(effect) 내부에는 렌더링 이후 수행해야 할 내용을 담는다. 초기 렌더링 이후 실행되며, 이 후 업데이트 마다 수행한다.(의존성 배열을 이용하여 컨트롤할 수 있다.)

```jsx
useEffect(() => {}, []);
```

다음 코드를 살펴보자

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  setCount((prev) => prev + 1);
}, [count]);
```

위 useEffect를 실행하면 infinite loop에 빠지게 된다.

왜냐하면,

1. 초기 렌더링 이후 setCount를 호출한다.
2. setCount는 count 상태값을 업데이트한다. => 리렌더링한다.
3. useEffect는 count가 업데이트하면 다시 effect를 실행한다.
4. 1로 돌아간다.

따라서 effect내부에서 setCount를 넣으면 infinite loop에 빠질 위험이 있다.

## loop에서 빠져나오기

useEffect에서 리스너를 등록하는 경우, 리스너가 state업데이트 함수를 담고 있는 경우, 의존성 배열을 적절히 설정해야 infinite loop에서 벗어날 수 있다.

useEffect 안에서 state업데이트 함수를 넣고, 의존성 배열에 state를 넣어서는 안된다.

## Further Readings

[useEffect](https://ko.reactjs.org/docs/hooks-effect.html){:target="\_blank"}
