---
layout: post
title:  "DpToPx & PxToPx"
subtitle: "DpToPx & PxToPx"
date:   2020-01-16 16:30:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# Dp To Px

```java
public int dpToPx(Context context, float dp){
    int px = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp, context.getResources().getDisplayMetrics());
    return px;
}
```

<br>

# Px To Dp

```java
public float pxToDp(Context context, float px) {
    // 해상도 마다 다른 density 를 반환. xxxhdpi는 density = 4
    float density = context.getResources().getDisplayMetrics().density;
    if (density == 1.0){      // mpdi  (160dpi) -- xxxhdpi (density = 4)기준으로 density 값을 재설정
        density *= 4.0;
    }else if (density == 1.5){ // hdpi  (240dpi)
        density *= (8 / 3);
    }else if (density == 2.0){ // xhdpi (320dpi)
        density *= 2.0;
    }
    return px / density;     // dp 값 반환
}
```