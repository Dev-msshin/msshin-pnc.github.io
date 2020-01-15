---
layout: post
title:  "LayoutParams width, height를 dp로 바꿀 때"
subtitle: "LayoutParams, getLayoutParams, width, height"
date:   2020-01-15 17:50:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
private void setLayoutWidthHeight(View view, int width, int height){
    if (view != null) {
        ViewGroup.LayoutParams layoutParams = view.getLayoutParams();
        if (width > 0){
            layoutParams.width = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, width, getResources().getDisplayMetrics());
        }
        if (height > 0){
            layoutParams.height = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, height, getResources().getDisplayMetrics());
        }
    }
}
```