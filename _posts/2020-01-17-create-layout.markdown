---
layout: post
title:  "동적으로 레이아웃(Layout) 만들기"
subtitle: "LinearLayout, TextView"
date:   2020-01-16 16:20:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# LinearLayout

```java
// 여기서는 RelativeLayout이 부모 레이아웃
private LinearLayout createLinearLayout(int id, int belowId){
    RelativeLayout.LayoutParams relativeLayoutParams = new RelativeLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT , ViewGroup.LayoutParams.WRAP_CONTENT);
    relativeLayoutParams.addRule(RelativeLayout.BELOW, belowId);
    LinearLayout linearLayout = new LinearLayout(this);
    linearLayout.setOrientation(LinearLayout.HORIZONTAL);
    linearLayout.setLayoutParams(relativeLayoutParams);
    linearLayout.setId(id);
    return linearLayout;
}
```

# TextView

```java
// 여기서는 LinearLayout이 부모 레이아웃
private TextView createTextView(int id, int textID, int startPadding){
    LinearLayout.LayoutParams linearLayoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT , ViewGroup.LayoutParams.WRAP_CONTENT, 1f);
    TextView textView = new TextView(this);
    textView.setGravity(Gravity.START);
    textView.setTextColor(getResources().getColor(R.color.list_select_color));
    textView.setLayoutParams(linearLayoutParams);
    textView.setTextSize(15);
    textView.setPadding(startPadding, 0, 0, 0);
    textView.setId(id);
    textView.setText(textID);
    return textView;
}
```