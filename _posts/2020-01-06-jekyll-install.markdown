---
layout: post
title:  "jekyll 설치 및 실행"
date:   2020-01-06 11:47:00 +0900
categories: jekyll
---

# 1. 루비(Ruby) 설치

- [루비 인스톨러 for windows](https://rubyinstaller.org/downloads/) 에서 **"==>"** 표시 되어 있는 버전 다운로드 및 설치
- 보안 프로토콜 문제 해결(**Gemfile**에 설정된 **https**가 있을 경우엔 실행하지 않는다.)
```
gem source
gem source -r https://rubygems.org/
gem source -a http://rubygems.org/
```


# 2. 지킬(Jekyll) 설치

- Windows 검색에서 **Start Command Prompt with Ruby** 실행

- Github 블로그와 연동된 로컬 경로로 이동

- 콘솔 창이 뜨면 아래 명령어로 Gem 설치
```
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```
Gemfile이 있는 경우
```
gem install
```

- 아래 명령어로 로컬 블로그 시작
```
jekyll serve
```

- 브라우저에서 아래 주소로 이동하여 확인
```
http://127.0.0.1:4000/
```
또는
```
http://localhost:4000/
```


# Redcarpet, Kramdown 설치

```
gem install redcarpet
gem install kramdown
```