---
layout: post
title:  "TextView 기본 패딩 제거"
subtitle: "Remove textView default padding"
date:   2020-01-10 18:01:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
<TextView
    android:id="@+id/textview"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:includeFontPadding="false"/>
```