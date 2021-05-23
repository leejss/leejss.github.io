---
layout: post
title: Data-Structure[0] - Priority Queue와 Heap
date: 2021-05-22 17:22 +0900
tags: Algorithm
categories: algorithm
---

## Priority Queue(우선순위 큐)

[Priority queue](https://en.wikipedia.org/wiki/Priority_queue){:target="_blank"}

- Queue와 비슷하지만 각각의 요소가 "priority"를 가지고 있다.
- Abstact Data Type이다.

### Methods

Priorty ADT는 다음의 Method를 지원한다.

- `P.add(k,v)`: priority가 k고 값이 v인 요소를 insert한다.
- `P.min()`: k가 minimum인 요소를 반환한다.
- `P.remove_min()`: k가 minimum인 요소를 삭제한다.

**Pitfall**: Priority가 높다 ==> k가 낮다.

heap을 이용하면 가장 성능이 좋은 우선순위 큐를 구현할 수 있다.

## Heap(힙)

[Heap (data structure)](https://en.wikipedia.org/wiki/Heap_(data_structure)){:target="_blank"}

- heap property를 만족하는 tree-based 데이터 구조
- Almost complete binary tree: tree의 높이가 h인 경우, h-1까지 Complete binary tree
- Heap order property:
  - max-heap: 부모 노드의 key가 자식 노드의 key보다 크다.
  - min-heap: 부모 노드의 key가 자식 노드의 key보다 작다.
- root 노드의 key가 항상 제일 크거나(max-heap) 제일 작다.(min-heap)

## Python heapq 라이브러리

[python heapq](https://docs.python.org/ko/3/library/heapq.html){:target="_blank"}

heap을 구현하기 위해 주로 리스트 구조를 이용한다.

heapq 라이브러리는 기본적으로 min heap구조를 가지고 있다.

heapq를 이용하여 우선순위 큐를 구현할 수 있다.

### 주요 method

- `heapq.heappush(heap, item)`: heap구조를 지키면서 item을 heap에 push
- `heapq.heappop(heap)`: heap구조를 지키면서 가장 작은 item을 heap에서 pop

```py
import heapq

p_queue=[]

heapq.heappush(p_queue, (10, 'a'))
heapq.heappush(p_queue, (5, 'b'))
heapq.heappush(p_queue, (1, 'c'))
heapq.heappush(p_queue, (3, 'd'))
print(heapq.heappop(p_queue)) # (1, 'c')
print(heapq.heappop(p_queue)) # (3, 'd')
print(heapq.heappop(p_queue)) # (5, 'b')
print(heapq.heappop(p_queue)) # (10, 'a')
```

### Time complexity

O(log n)
