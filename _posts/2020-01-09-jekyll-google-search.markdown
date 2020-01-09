---
layout: post
title:  "[jekyll] Github page google 검색 허용"
subtitle: "Google 검색 허용"
date:   2020-01-09 12:59:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# 1. sitemap

- _config.yml

```java
plugins:
  - jekyll-sitemap
```

<br>

# 2. robot.txt

- 루트 폴더에 robot.txt 파일 생성

```java
User-agent: *
Allow: /

Sitemap: https://msshin-pnc.github.io/sitemap.xml
```

<br>

# 3. 구글 등록

- [구글 웹마스터](https://www.google.com/webmasters/#?modal_active=none) 접속

- **SEARCH CONSOLE** 클릭

- URL 인증

    - **URL 접두어** 등록

        - https://msshin-pnc.github.io
    
    - google65d2626114100ad5.html 을 최상위 폴더에 다운로드 및 적용

    - 확인
    
- 메뉴에서 **Sitemaps** 클릭

- https://msshin-pnc.github.io/sitemap.xml 등록