---
layout: post
title:  "타이틀바 없애기"
subtitle: "Full screen, 타이틀바 제거"
date:   2020-01-21 16:30:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- styles.xml
    - **android:windowNoTitle=true** 추가

```java
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="android:Theme.Material.Light.DarkActionBar">
        <item name="android:windowNoTitle">true</item>
    </style>
</resources>
```

<br>

- MainActivity.java

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                WindowManager.LayoutParams.FLAG_FULLSCREEN);
    }
}
```