---
layout: post 
title: Tagged Template Literals
tags: javascript
---

## Template Literal

템플릿 리터럴은 내장된 표현식을 허용하는 문자열 리터럴이다.

`(backtick) 기호로 문자열을 감싸면 템플릿 리터럴이다.

리터럴 안에서 자바스크립트 표현식(expression)을 쓰고 싶으면 `${}`안에 작성하면 된다.

```javascript
// example
const name = "James"
const msg = `Welcome ${name}`
console.log(msg) // Welcome James

const flag = true;
const msg2 = `Look at ${flag ? "me" : "you"}`;
console.log(msg2); // Look at me

```

## Tagged Template Literals

템플릿 리터럴을 함수와 함께 쓰이면 Tagged Template Literal이다.

템플릿 리터럴의 내용이 함수로 넘어가고 해당 함수에서 탬플릿 리터럴의 내용에 접근하여 코드를 수행할 수 있다.

```javascript

function show(literals) {
  console.log(literals);
}

show`This is template literal`; // [ 'This is template literal' ]
```

표현식 사용없이 문자열 리터럴이 넘어가게 되면 해당 문자열을 원소로한 배열이 함수로 넘어가게 된다.

### 표현식과 함께 쓰이는 경우

표현식이 들어간 템플릿 리터럴 경우 함수로 넘어가는 모습이 달라진다.

```javascript

function show(literals) {
  console.log(literals);
}

const info = "template";

show`This is ${info} literal`; // [ 'This is ', ' literal' ]

```

오직 문자열만 배열에 담겨져서 함수로 넘어가게 된 것을 알 수 있다. 표현식의 값은 함수로 넘어가지 않았는데 이는 함수의 매개변수를 조정하면 해결할 수 있다.

```javascript
function show(literals, info) {
  console.log(literals);
  console.log(info);
}

const info = "template";

show`This is ${info} literal`;
// 결과
// [ 'This is ', ' literal' ]
// template 

```

`info`변수의 값이 인자로 넘어가게 된 것을 알 수 있다.

표현식이 여러 개인 경우 매개변수도 그에 맞춰서 여러 개로 늘리면 된다.

```javascript
function show(literals, a, b, c) {
  console.log(literals);
  console.log(a);
  console.log(b);
  console.log(c);
}

const be = "is";
const info = "template";
const noun = "literal";

show`This ${be} ${info} ${noun}`;

/*
결과

[ 'This ', ' ', ' ', '' ]
is
template
literal

*/
```

### rest operator

함수의 첫번째 매개변수는 문자열의 배열을 받고, 나머지 매개변수는 표현식의 값을 받는 것을 알 수 있다.

하지만 표현식의 개수에 맞춰서 매개변수를 항상 수정할 수 없기 때문에 rest연산자를 사용하며 표현식의 값을 배열로 받는다.

```javascript
function show(literals, ...args) {
  console.log(literals);
  console.log(args);
}

const be = "is";
const info = "template";
const noun = "literal";

show`This ${be} ${info} ${noun}`;

/*
결과

[ 'This ', ' ', ' ', '' ]
[ 'is', 'template', 'literal' ]

*/

```

하나의 배열에 템플릿 리터럴 요소를 전부 담아서 보내고 싶다면 `...args`만 매개변수로 사용하면 된다.

```javascript
function show(...args) {
  console.log(args);
}

const be = "is";
const info = "template";
const noun = "literal";

show`This ${be} ${info} ${noun}`;

/*
결과

[ [ 'This ', ' ', ' ', '' ], 'is', 'template', 'literal' ]

*/

```