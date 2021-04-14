---
layout: post
title: Regexp Quick Recap (정규표현식 간단 요약) with Javascript
date: 2021-04-14 17:38 +0900
tag: Regexp
---

## Regexp 생성

### Regexp constructor

```js
const str = "Almost before we knew it, we had left the ground."
// new RegExp("pattern", "flags")
const regexp = new RegExp("[a-z]", "g")

str.match(regexp)
```

### literal

```js
const str = "Almost before we knew it, we had left the ground."

// /pattern/flags
str.match(/[a-z]/g)
```

## Methods

### test()

정규식.test(문자열) : 일치여부(boolean) 반환

```js
/naver/.test(str)
```

### match()

문자열.match(정규식): 일치하는 문자열의 배열 반환

```js

str.match(/dog/g)

```

### replace()

문자열.replace(정규식, 문자열): 정규식으로 찾은 문자열을 다른 문자열로 바꾼 뒤 반환

```js
str.replace(/dog/, 'cat')
```

## Flags

### g

g (global search): 일치하는 결과를 모두 반환

```js
const str = 'dogdogdog'
str.match(/dog/) // return ['dog']
str.match(/dog/g) // return ['dog','dog','dog']
```

### i

i (ignore case): 대소문자를 구별하지 않음

```js
const str = 'dogDogdogDog'
str.match(/dog/g) // return ['dog', 'dog']
str.match(/dog/gi) // return ['dog', 'Dog', 'dog', 'Dog']
```

### m

m (multi line): 문장의 시작과 끝을 여러 개로 인식 >> 문자열 내 문장을 인식

본래 문자열의 시작과 끝은 문자열이 시작하는 지점과 끝나는 지점이다.

즉,

```js
const str = `
Almost before we knew it, we had left the ground.
Almost before we knew it, we had left the ground.
Almost before we knew it, we had left the ground.
Almost before we knew it, we had left the ground.
Almost before we knew it, we had left the ground.
`
```

이렇게 문자열이 있을 때, m flag가 없는 경우, 문장의 시작과 끝은 백틱이 시작하는 지점과 끝나는 지점이다.

m flag를 붙여야 문장을 여러개로 인식한다.

```js
str.match(/^A/g) // return ['A']
str.match(/^A/gm) // return ['A','A','A','A','A']
```

## Anchors

문자열의 위치와 관련된 패턴

### ^

문자열 또는 문장(with m flag)의 시작을 의미

```js
str.match(/^The/gm) // return 문자열 내 문장의 시작에 위치한 'The'를 배열에 담아 모두 반환
```

### $

문자열 또는 문장(with m flag)의 끝을 의미

```js
/.$/gm // 문장 마지막에 위치한 임의의 문자
```

### \b

word boundary.

단어의 경계를 의미한다.

```js
/.\b/gm // 단어 끝에 위치한 임의의 문자
```

## Character Classes

### .

dot: line break(\n, \r)을 제외한 모든 문자

### [ABC]

character set: match any character in the set

```js
'Almost before we knew it, we had left the ground.'.match(/[abc]/g) // return ['b', 'a']
```

### [A-C]

range: 두 캐릭터 사이에 있는 문자열을 match

```js
const str = 'Almost before we knew it, we had left the ground.'

str.match(/a-f/g) // ['f','a','f']
```

### \d

digits: [0~9]

### \w

any word character: [A-Za-z0-9_]

## Quantifier

### {min,max}

문자열의 length를 명시.

```js
// b로 시작하는 문자 중, 길이가 2~3인 문자를 반환
/b\w{1,3}/ 

// max를 명시하지 않은 경우, unlimit
// 길이가 2 이상인 문자를 반환
/b\w{1,}/ 
```

## Alternation

### +

plus: Matches 1 or more of the preceding token.

```js
// b로 시작하는 길이가 2이상인 문자를 반환
/b\w+/ 


// 위와 동일
/b\w{1,}/ 


```

### *

star: Matches 0 or more of the preceding token.

```js
// b로 시작하는 길이가 1 이상인 문자열을 반환
/b\w*/ 


```

### |

or 을 의미한다.

```js
// bad, bed, bid를 검사
/b(a|e|i)d/ 
```

## Lookaround

### (?=ABC)

lookahead: ABC 앞을 검사한다.

### (?<=ABC)

lookbehind: ABC 뒤를 검사한다.