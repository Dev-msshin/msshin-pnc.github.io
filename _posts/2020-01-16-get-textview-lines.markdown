---
layout: post
title:  "TextView Line"
subtitle: "TextView text lines"
date:   2020-01-16 16:20:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# Rendering 되기 전에 TextView Line 가져오기

```java
private int getTextLines(float currentTextSize, float currentViewWidth, String textString){
    final Rect bounds = new Rect();
    final Paint paint = new Paint();
    paint.setTextSize(currentTextSize);
    paint.getTextBounds(textString, 0, textString.length(), bounds);
    return (int) Math.ceil((float) bounds.width() / currentViewWidth);
}
```

<br>

# 한 줄로만 표시하고 한 줄 이상이면 **...** 표기하기
 
 ```java
private void setSingleLineTextView(TextView textView){
    textView.setMaxLines(1);
    textView.setEllipsize(TextUtils.TruncateAt.END);
}
```

<br>

# 한 줄로만 표기하고 흐르는 효과를 주고 싶은 경우

 ```java
private void setSingleLineTextView(TextView textView){
    textView.setSingleLine();
    textView.setEllipsize(TextUtils.TruncateAt.MARQUEE);
    textView.setMarqueeRepeatLimit(3); // 흐르는 횟수 (기본 값은 무제한)
    textView.setFocusable(true);
    textView.setSelected(true);
}
```
