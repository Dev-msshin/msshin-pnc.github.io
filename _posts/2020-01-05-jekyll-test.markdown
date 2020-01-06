---
layout: post
title:  "jekyll 연습"
date:   2020-01-05 16:42:25 +0900
categories: jekyll
---

------------


Jekyll 마크다운 툴

**redcarpet** 혹은 **kramdown**

----------


```
---
layout: post // post, page 중 하나를 넣어 이것이 한 페이지에 속하는 블로그 포스트인지, 홈페이지를 구성하는 하나의 큰 페이지인지를 결정한다.
title: "제목" // 포스트나 페이지의 제목을 넣는다.
date: 2015-02-08T20:39:51-09:00 // 생성일로, 양식은 년-월-일T시:분:초-GMT시간 으로 추측된다.
modified: 2015-02-08T22:21:31-09:00 // 변경일
categories: articles // 어느 페이지에 속하는지를 표시한다.
excerpt: "요약문" // 이 페이지에 대한 요약문을 정하는 것 같은데, 정확히 어떤 동작을 보이는지 아직 모르겠다.
tags: [haroopad] // 태그를 지정할 수 있다. 해시태그인지 어떻게 동작하는지는 역시 아직 모른다.
image: // so-simple-theme 에서 글 맨 위에 노출될 큰 사진을 결정할 때 쓴다. 기본 폴더는 images.
  feature: test.jpg // 사진 파일 이름
  credit: Me // 사진 제작자 및 저작권자
  creditlink: me.org // 저작권자의 홈페이지이거나 이 사진을 직접 구할 수 있는 링크
share: true // so-simple-theme 의 사이드바에 공유 버튼이 추가된다.
comments: true // 블로그 포스트 하나하나마다 개별적으로 disqus 댓글 버튼을 추가해줄 수 있다.
---
```





{% highlight language %}
하이라이트
{% endhighlight %}

{% highlight language linenos=table %}
하이라이트 줄 번호
{% endhighlight %}

# **소스 코드에서** *중괄호* **넣는 방법** 
{% highlight language linenos=table %}
{% raw %}
소스코드를 여기에 넣는다.
{% endraw %}
{% endhighlight %}


# Header 1
## Header 2
### Header 3

- Bulleted
- List


# **다른 타이틀 보이기**
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
 

--------------------------
# 1. 1111111
asdfsdf
#### 2. 222
asdfasdf
### 3. 33
asdfasdf

---------

1. Numbered
2. List


- Bulleted
- List


```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```


**Bold** and _Italic_ and `Code` text





---------------


> Coffee. The finest organic suspension ever devised... I beat the Borg with it.
> - Captain Janeway


-----------

```
if (isAwesome){
  return true
}
```

```javascript
if (isAwesome){
  return true
}
```

```java
if (isAwesome){
  return true
}
```

{% highlight ruby linenos %}
def foo
  puts 'foo'
end
{% endhighlight %}

--------------

{% include show_title.html %}

----------------


{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}