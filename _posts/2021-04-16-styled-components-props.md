---
layout: post
title: styled-components에서 props 활용하기
date: 2021-04-16 23:12 +0900
tags: React styled-components
category: React
---

## styled-component에서 props 받기

tagged template literal을 사용하기 때문에 `${}`내부에 props를 받는 함수를 정의하여 props를 사용할 수 있다.

```jsx
const StyledDiv = styled.div`

    ${props => /* do something with props*/}

`
```

## props에 따라 스타일 다르게 정의하기

특정한 props의 유무에 따라서 스타일을 다르게 정의할 수 있다.

`&&` 연산자와 styled-components의 `css`를 이용한다.

```jsx

const Square = styled.div`
    widht: 500px;
    height: 500px;****

    ${props => props.blue &&  css`
        background: blue;
    `}

    ${props => props.red &&  css`
        background: red;
    `}

    ${props => props.black &&  css`
        background: black;
    `}
`

const Box = () => {

    return (
        <div>
            <Square blue />
            <Square red />
            <Square black />
        </div>
    )
}

```
