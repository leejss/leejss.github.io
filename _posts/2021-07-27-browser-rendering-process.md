---
layout: post
title: 브라우저 렌더링 과정
date: 2021-07-27 10:21 +0900
tags:
  - Web
---

## 브라우저 컴포넌트

1. User interface
2. Browser engine
3. Rendering engine
4. Networking
5. UI backend
6. Javascript interpreter
7. Data storage

## Rendering engine

역할: HTML content 또는 이미지를 화면에 보여준다. 그 외 파일을 플러그 인을 통해 보여준다.
종류 (Browser - engine) - Internet explorer: Trident - Firefox: Gecko - Safari: Webkit - Chrome: Blink

## Basic flow of rendering engine

0. Networking layer을 통해서 8kb chunks사이즈로 contents를 전달받는다.
1. Parse HTML, Convert elememt to DOM Node, and Contructs DOM tree
2. Styling information(external CSS and style elements)을 결합하여 render tree를 만든다.
3. Layout of the render tree: DOM 노드들을 스크린 상 정확한 좌표에 배치.
4. Painting: 각각의 DOM node를 순회하면서 UI backend layer을 통하여 페인트.

### Paint order

1. background color
2. background image
3. border
4. children
5. outline

### Flow Example

1. HTLM parser parse HTML file => DOM Tree
2. CSS parser parse Style sheet => style rules
3. 결합
4. Render tree
5. Layout
6. Paint
7. Display
