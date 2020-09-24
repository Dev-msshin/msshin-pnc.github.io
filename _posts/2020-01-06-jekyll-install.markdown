---
layout: post
title:  "jekyll 설치 및 실행"
date:   2020-01-06 11:47:00 +0900
categories: jekyll
comments: true
background: '/img/posts/06.jpg'
---

#  1. 루비(Ruby) 설치

- [[루비 인스톨러 for windows](https://rubyinstaller.org/downloads/)] 에서 **==>** 표시 되어 있는 버전 다운로드 및 설치
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
bundle install
```

- 아래 명령어로 로컬 블로그 시작
```
jekyll serve
```
혹은 의존성 빌드
```
bundle exec jekyll serve
```

**Windows UTF-8 인코딩 에러 시 해결**
```
chcp 65001
bundle exec jekyll serve
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


# 테마 설치

- [루비 젬 테마](https://rubygems.org/search?utf8=%E2%9C%93&query=jekyll-theme) 사이트에서 테마 선택

- **GEMFILE:** 해당 내용 복사 후 붙여넣기, 기존 테마 삭제

- **_config.yml**에 **theme:** 수정

- **INSTALL:** 루비 콘솔 창에서 실행

- 사이트 빌드
```
jekyll serve
```
