---
layout: post
title:  "HTML 기본 문법"
subtitle: "HTML 기본 문법"
date:   2020-01-06 14:57:00 +0900
categories: html
comments: true
background: '/img/posts/06.jpg'
---

# HTML(HyperText Markup Language)

- 주요
{% highlight language %}
{% raw %}
<br> : 줄 바꾸기
<p> : 단락 바꾸기
<hr> : 가로줄
<center> ... </center> : 가운데 정렬
<font> ... </font> : 폰트 변경
<ul><li> ... <li> ... </ul> : 순서없는 목록(동그라미)
<ol><li> ... <li> ... </ol> : 순서있는 목록(숫자)
<table> ... </table> : 표 만들기
<tr> ... </tr> : 행
<td> ... </td> : 열
<a> ... </a> : 하이퍼링크
{% endraw %}
{% endhighlight %}

- 하이퍼링크 속성
{% highlight language %}
{% raw %}
href : 연결할 주소

target : 지정된 href 주소를 보여줄 때 넣어줄 설정
- target = "_self" : 현재 브라우저에서 링크를 연결
- target = "_blank" : 새 창에서 링크를 연결
- target = "_top" : 현재 브라우저의 가장 위쪽으로 스크롤

title : 마우스를 링크위에 올려두면 나오는 설명

id : href 속성으로 링크가 아닌 현재 페이지에서 이동(#Link 사용)
예 : <a href="#here">이동</a>
<a id ="here">여기</a>
{% endraw %}
{% endhighlight %}

- 주석
```
<!-- 내용 -->
```


