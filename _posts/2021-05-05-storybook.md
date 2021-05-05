---
layout: post
title: storybook
date: 2021-05-05 15:51 +0900
tags: React
categories: React
---

## What is Storybook?

>Storybook is an open source tool for developing UI components and pages in isolation. - official doc

## Add storybook in your React project

```bash
# at root directory
npx sb init
```

커맨드 실행 시, 자동으로 루트 디렉토리에 `.storybook` 폴더가 생기고, `src` 아래에 `stories` 폴더가 생긴다.

그리고 `package.json`에 자동으로 두 개의 스크립트를 추가한다.

```json
    "storybook": "start-storybook -p 6006 -s public",
    "build-storybook": "build-storybook -s public"
```

`-p` 는 포트, `-s`은 static 파일을 제공하는 디렉토리를 명시하는 CLI 옵션이다. 이 외 다양한 CLI 옵션이 존재하는데 공식 홈페이지에서 확인할 수 있다.

[Storybook CLI Options](https://storybook.js.org/docs/react/api/cli-options){:target="_blank"}

```shell
yarn storybook
```

커맨드를 실행하면 storybook 웹과 연결한다.

## Add story

### Create stories.js

story를 추가하기 위해 먼저 파일을 만든다.

```txt
# Example

Button.stories.js
Header.stories.js
...
```

`.stories.js` 는 다음과 같이 이루어져 있다.

```jsx
import React from 'react'
import Component from './Component'
// Template
// configure props value via args object
const Template = args => <Component {...args} />

// metadata
export default {
    title: "Story book title", 
    component: Component // base Component
}

// Named story export
export const StoryName = Template.bind({})
// set args
StoryName.args = {
    // args
}
```

#### meta data

```js
export default {
    // meta data here

}
```

`export default` 를 통해서 컴포넌트에 관한 meta data를 담은 객체를 내보낸다.  
공식 문서: [Default export](https://storybook.js.org/docs/react/api/csf#default-export){:target="_blank"}

#### Named story exports

named story를 통해서 개발자가 원하는 상태를 args를 통하여 만들 수 있다.

공식 문서: [Named story exports](https://storybook.js.org/docs/react/api/csf#named-story-exports){:target="_blank"}

Named story를 작성하는 방식에는 두 가지가 있다.

```js

// 1. no args --> Template없이 사용
export const DefaultButton = () => <Button />
export const WhiteButton = () => <Button color="white" />

// 2. with args --> Template 정의
const Template = args => <Button {...args} />
export const WhiteButton = Template.bind({})
WhiteButton.args = {
    color: "white"
}
```

`args`로 작성한 story를 작성한 경우 storybook의 control panel을 활용할 수 있다.

## Configure story

루트 디렉토리에 생긴 `.storybook`에는 `main.js`와 `preview.js`가 있다.

`main.js`는 storybook의 webpack config를 설정할 수 있다.

```js
// main.js

module.exports = {
    // config
}
```

이를 통해서 storybook에 addon을 추가하거나 `sass-loader`를 추가할 수 있다.
