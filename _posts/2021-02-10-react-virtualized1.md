---
layout: post 
title: React - react-virtualized 맛보기 -1
categories: Post 
tags: 
- TIL
- React
---

## 언제 react-virtualized가 필요한가

**react-virtualized**는 리엑트의 렌더링 최적화에 사용한다. 컴포넌트의 내용이 많을 경우 한꺼번에 렌더링하지 않고 화면에 보이는 부분만 렌더링하기 위해 사용한다.

    //설치
    yarn add react-virtualized

## List

**react-virtualized**는 컴포넌트의 모습에 따라 다른 사용방식을 제공하는데 여기서는 `List` 컴포넌트를 예로 든다. 라이브러리에서 제공하는 `List`는 실제 컴포넌트가 아닌 virtualized 컴포넌트이다.

```javacsript
import {List} from 'react-virtualized'
```

### rowRenderer

`List`를 사용하기 위해서는 `rowRenderer`라는 함수를 먼저 정의해야 한다. 이 함수에서는 리스트의 row에 해당하는 컴포넌트 (여기서는 TodoListItem)를 렌더링을 담당한다.

```javascript
// import 생략

const TodoList = ({todos, onRemove, onToggle}) => {
    const rowRenderer = ({index, key, style}) => {
        const todo = todos[index]
        return (
            // render 부분
            // 리스트의 row 를 이루는 개발자가 만든 컴포넌트
            <TodoListItem 
                todo={todo}
                key={key}
                onRemove={onRemove}
                onToggle={onToggle}
                style={style}
            />
        )
    }
}

```

`rowRenderer`함수의 파라미터에 대한 설명은 공식 [깃허브 페이지](https://github.com/bvaughn/react-virtualized/blob/master/docs/List.md#rowrenderer)에 나와있다.

index는 row의 인덱스, key는 row의 unique 키, style은 row의 스타일을 지정하는 스타일 객체다.

인자를 받아서 `TodoListItem` 컴포넌트에 props로 전달하고 있다.

`rowRenderer`함수를 정의한 후에는 `TodoList`컴포넌트의 렌더 부분을 만든다. 여기서 `List` 버츄얼 컴포넌트를 사용한다.

```javascript
// import 생략

const TodoList = ({todos, onRemove, onToggle}) => {
    // rowRenderer 정의 생략

    return (
        <List
            className='TodoList'
            width={512} // 컴포넌트의 width (픽셀단위)
            height={513} // 컴포넌트의 height (픽셀단위)
            rowCount={todos.length}
            rowHeight={}
            rowRenderer={rowRenderer}
            list={todos}
            style={{outline: none}}
        />
    )

// export 생략
}
```

그 다음 `TodoListItem`컴포넌트로 가서 `return`에 해당하는 부분을 `div`태그로 감싼다.

```javascript

const TodoListItem = ({todo, onRemove, onToggle, style}) => {
    // ...

    return (
        <div className='TodoListItem-virtualized' style={style}>
            {/* 기존 UI부분*/}
        </div>
    )
}

```

## 문제

위 내용을 실제로 적용해보려고 하자 문제가 발생했다. `List`컴포넌트에 `width`와 `height`에 픽셀 단위로 값을 주어야 하는데 픽셀이 아닌 viewport unit인 `vh`나 `vw`로 길이를 설정할 경우 픽셀 단위가 브라우저 크기에 따라 바뀐다는 것이다.

해당 문제를 해결하기 위해서 `react-virtualized`라이브러리는 AutoSizer라는 하이어오더 컴포넌트를 제공한다.

>The AutoSizer component decorates a React element and automatically manages width and height properties so that decorated element fills the available space. This simplifies usage of components like Grid, Table, and List that require explicit dimensions. -- 공식 깃허브페이지에서

다음에는 AutoSizer에 대해서 맛보기로 알아봐야 겠다.
