---
layout: post 
title: 너비 우선 탐색(Breadth-first search)
tags: algorithm
---

## 1. Basic

**Breadth**를 번역하면 '**너비**' (또는 폭)다.

그렇다면 여기서 왜 너비라는 단어를 사용하는 것일까?

그래프 G = (V, E)와 **source** vertex *s*가 주어져 있다고 가정해보자.

*s*를 기준으로 그래프를 너비 우선 탐색하고자 한다.

기준 *s*를 기준으로 1만큼 떨어져 있는 vertices를 먼저 discover한다.

1만큼 떨어져 있는 vertices를 전부 discover했으면 2만큼 떨어져 있는 vertices를 탐색하기 시작한다.

**즉, distance k만큼 떨어져 있는 vertices를 전부 discover해야 distance k+1 탐색이 시작한다.**

이러한 탐색의 결과로 Tree구조가 만들어진다.

여기서 **너비**와 **깊이**가 등장하게 된다.

![breadth-first-search](../../../../assets/images/bead-first.jpg)

위 그림에서 s를 기준으로 탐색을 수행한다.

s를 기준으로 너비우선 탐색을 수행하면 다음과 같은 Tree구조가 만들어진다.

![breadth-first-search](../../../../assets/images/tree.jpg)

숫자는 기준 *s*가 0일때, *s*로 부터 떨어진 거리를 의미한다.

여기서 **너비를 우선으로 탐색** 한다는 것은, 검색 결과를 Tree구조로 만들어 나갈때, 너비가 우선적으로 만들어 지기 때문에 너비 우선 탐색이라고 한다.(하는 거 같다.)

**다시 말하지만 이렇게 너비가 우선적으로 채워지는 이유는 같은 거리에 있는 vertices부터 찾아나가기 때문이다.**

k만큼 떨어져 있는 vertices를 전부 찾아야 k+1만큼 떨어져 있는 vertices를 탐색한다.
