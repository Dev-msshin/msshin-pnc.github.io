---
layout: post
title:  "List 정렬하기, 순서 바꾸기"
subtitle: "Collections, Comparator 사용"
date:   2020-01-14 10:53:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
Collections.sort(mContents, new Comparator<TestClass>() {
	@Override
	public int compare(TestClass o1, TestClass o2) {
		if (o1 != null && o2 != null){
			if (o1.id > o2.id){
				return -1;
			}else{
				return 1;
			}
		}
		return 0;
	}
});
```