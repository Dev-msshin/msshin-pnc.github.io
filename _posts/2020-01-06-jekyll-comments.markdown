---
layout: post
title:  "jekyll 댓글 추가"
date:   2020-01-06 14:03:00 +0900
categories: jekyll
comments: true
---

# 1. Disqus 생성 및 Universal code 복사

- [Disqus](https://disqus.com/) 회원가입

- Disqus에서 아래 경로로 이동해 블로그 사이트 등록
```
사용자 계정 정보 - Setting - **Add Disqus to your site**
```

- 아래 경로를 통해 **Universal code** 복사
```
Setting - Installation
```

# 2. 적용

- 복사한 **Universal code**를 **_layouts/post.html**에 추가