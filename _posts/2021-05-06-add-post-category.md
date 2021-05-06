---
layout: post
title: Jekyll 블로그에 카테고리 페이지 추가하기
date: 2021-05-06 18:35 +0900
tags: Jekyll
categories: ETC
---

## 깃허브 블로그에 카테고리 추가, 그러나 404 오류

저의 깃허브 블로그는 [Tale](https://github.com/chesterhow/tale){:target="_blank"}이라는 Jekyll Theme으로 만들었습니다.

Alogorithm과 관련된 포스트를 위한 카테고리가 필요해서 Jekyll 블로그에서 카테고리를 어떻게 추가하는지 알아보았습니다.

구글링 결과 [다음 글](https://devyurim.github.io/development%20environment/github%20blog/2018/08/07/blog-6.html)을 찾게 되었고 해당 내용으로 카테고리를 추가했습니다.

카테고리를 추가하고 github에 push했습니다.

하지만 카테고리 페이지에 들어가보면 404 Error가 발생합니다.

빌드 결과를 깃허브 블로그에서 인식?하지 못하는 것 같습니다.

```bash
bundle exec jekyll serve
```

해당 커맨드를 통해서 로컬 서버에서는 페이지가 잘 나오는데 깃허브 블로그에는 나오지 않았습니다.

그래서 다음과 같이 카테고리 페이지를 작성했습니다.

## Jekyll Variables

카테고리 페이지를 직접 작성하기 위해서는 Jekyll 블로그의 변수에 대해서 알아야 합니다.

[지킬 공식문서](https://jekyllrb.com/docs/variables/)를 통해서 자세히 알 수 있었습니다.

이 중 `site.posts`와 `post.categories`를 통해서 카테고리 페이지를 만들 수 있을 것 같았습니다.

## Liquid

Jekyll은 [Liquid](https://shopify.github.io/liquid/) 템플릿 언어를 사용합니다. 페이지를 작성하기 위해서는 해당 언어에 대해서 알아야 합니다.

먼저 알고리즘 포스트를 담을 `html` 파일을 생성합니다.

```shell
_pages
    - algorithm.html
```

사이트의 모든 포스트를 불러옵니다.

```txt
---
layout: default
title: "Algorithm"
author: "Lee Jongseo"
permalink: /algorithm/
---
{{ for post in site.posts }}

```

만약 포스트의 카테고리가 alogirithm이라면 해당 포스트 링크와 관련 데이터를 가져옵니다.

```txt
{{ for post in site.posts }}
    {{ if post.categories[0] == "algorithm"}}
    <!-- post data -->

    {{ endif }}
{{ endfor }}


```

`post.categories == "algorithm"` 이 아니라 `post.categories[0] == "algorithm"` 으로 한 이유는 post.categories가 배열이기 때문입니다.

이런 식으로 jeykll variable을 이용하여 직접 카테고리 페이지를 만들 수 있습니다.
