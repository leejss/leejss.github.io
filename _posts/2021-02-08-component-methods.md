---
layout: post 
title: React - 컴포넌트에 method 추가하기
---

## 컴포넌트 메소드

컴포넌트에 메소드를 추가하여 이벤트 헨들러로 등록할 수 있다.

```jsx
// Example
<button onClick={컴포넌트 메소드}>버튼</button>

```

## 클래스형 컴포넌트에서 메소드 추가하기 - 1

클래스형 컴포넌트에서 메소드를 추가하기 위해서는 `bind()` 작업이 필요하다.

```jsx
// ...
    constructor(props) {
        super(props) {
            this.handleClick = this.handleClick.bind(this) // bind 작업
        }
    }
``` 

클래스형 컴포넌트에서 `bind()` 작업을 하는 이유는 `this`가 정상적으로 컴포넌트를 가리키도록 하기 위해서다.

`constructor` 내부에서 이루어진다.

새로운 메소드를 추가할 때마다 `constructor`도 수정해야 하는 불편함이 있다.

```jsx
// Example

// import 생략

class MyComponent extends Component {
    constructor(props) {
        super(props) {
            this.handleClick = this.handleClick.bind(this) // bind 작업
        }
    }

    // method 추가
    // 자바스크립트 클래스 문법
    handleClick(e) {
        // code here
    }    
} 

```

## 클래스형 컴포넌트에서 메소드 추가하기 - 2

`bind()` 작업을 생략할 수 있는 방법이 있다.

### Property Initializer Syntax

babel 플러그인인 transform-class-properties을 이용하여 클래스 내부에 화살표 함수 형태로 메소드를 추가할 수 있다.

[예시: transform-class-properties](https://www.npmjs.com/package/babel-plugin-transform-class-properties#example){:target="_blank"}

React에는 해당 플러그인이 내장되어 있어 위 문법을 바로 사용가능 한 것 같다.

이를 이용하면 `bind()`작업과 `constructor` 수정도 필요 없다.

```jsx
// example
class MyComponent extends Component {


    // method 추가
    // Property Initializer Syntax
    handleClick = (e) => {
        // code here
        // arrow function 내부에서 this는 컴포넌트를 가리킨다.
    }
} 
```

## 함수형 컴포넌트에서 메소드 추가하기

컴포넌트 함수 코드 블록 안에서 변수에 함수객체를 할당하면 된다.

```jsx
// example

const MyComponent = () => {

    // 메소드 추가
    const handleClick = (e) => {
        // code here
    }
}
```
