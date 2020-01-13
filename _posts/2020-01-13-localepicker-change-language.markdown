---
layout: post
title:  "LocalePicker를 사용하여 기본 언어 변경하기"
subtitle: "Android 기본 언어 변경"
date:   2020-01-13 10:53:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
private void changeLanguage(){
    List<LocalePicker.LocaleInfo> localeInfoList = LocalePicker.getAllAssetLocales(this, false);
    for (LocalePicker.LocaleInfo localeInfo : localeInfoList) {
        Log.d(TAG, localeInfo.getLocale() + ", " + localeInfo.getLabel());
    }
    if (localeInfoList.size() > 0) {
        LocalePicker.updateLocale(localeInfoList.get(0).getLocale());
    }
}
```