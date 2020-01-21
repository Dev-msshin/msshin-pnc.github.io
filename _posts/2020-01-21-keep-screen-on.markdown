---
layout: post
title:  "스크린 항상 켜두기"
subtitle: "keep screen, always screen on, 스크린 꺼지지 않게 하기"
date:   2020-01-21 16:33:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- MainActivity.java

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON,
                WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
    }
}
```