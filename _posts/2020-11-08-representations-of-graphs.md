---
layout: post 
title: 그래프 나타내기 (Reresentations of graphs)
---

## 0. Basic

Vertices(점)가 V고 Edges(선)가 E인 그래프 G: G = (V,E)

*Edge*를 기준으로 나눈 그래프의 두 가지 종류:

- Undirected graph: edge가 양뱡향(bidirectional) ( O --- O )
- Directed graph: edge가 단방향(unidirectional)  ( O --> O )

**Searching a graph**의 의미: edges를 따라서 vertices를 찾아가는(visit) 것

그래프를 표현하는 두 가지 방법 (two **computational reprentations** of graphs)

- adjacency-list representation (인접리스트)
- adjacency-matrix representation (인접행렬)

## 1. adjacency-list representation (인접리스트)

각각의 Vertex는 edge로 연결된 다른 Vertex를 요소로 삼은 Adj 리스트를 가진다.

각각의 다른 V인 `u`, `v`가 존재할때, `edge(u,v)`가 존재한다면,

`Adj[u]`는 `v`를 요소로 가지고 있다.

`Adj[u]`의 `length` 의 합은 `|E|`와 같다. (when G = (V,E))

**Undirected 그래프인 경우**, edge가 점 양쪽을 이어주기 때문에 만약 `Adj[u]`의 요소 중 `v`가 존재한다면 `Adj[v]`의 요소에 `u`도 존재하게 된다.

**반면에 Directed 그래프인 경우**, edge는 단방향이기 때문에 `Adj[u]`의 요소 중 `v`가 존재한다면 `Adj[v]`의 요소에는 `u`가 존재할 수 없다.

## 2. adjacency-matrix representation (인접행렬)

`|V|` x `|V|` 행렬을 구성한다

예를 들어, 그래프의 점(Vertex)의 숫자가 총 5개라면 5 x 5 행렬을 구성

만약 edge(u,v)가 존재한다면 1, 존재하지 않는다면 0으로 행렬을 채운다.

## 3. 참고 사이트

[인접리스트와 인접행렬 그림](https://www.geeksforgeeks.org/graph-and-its-representations/)
