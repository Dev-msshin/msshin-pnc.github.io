---
layout: post
title:  "lottie okio 에러"
subtitle: "lottie okio 에러"
date:   2020-01-07 14:46:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# **문제**
-------------------------
- lottie 적용 후 release 빌드 시 아래와 같은 오류가 나며 빌드 실패

```java
Information:Gradle tasks [clean, :app:assembleAbi64Release]
Warning:okio.AsyncTimeout: can't find referenced class javax.annotation.Nullable
Warning:okio.Buffer: can't find referenced class javax.annotation.Nullable
Warning:okio.BufferedSource: can't find referenced class javax.annotation.Nullable
Warning:okio.ByteString: can't find referenced class javax.annotation.Nullable
Warning:okio.DeflaterSink: can't find referenced class org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
Warning:okio.HashingSink: can't find referenced class javax.annotation.Nullable
Warning:okio.Okio: can't find referenced class org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
Warning:okio.Okio$4: can't find referenced class javax.annotation.Nullable
Warning:okio.Pipe: can't find referenced class javax.annotation.Nullable
Warning:okio.RealBufferedSource: can't find referenced class javax.annotation.Nullable
Warning:okio.Segment: can't find referenced class javax.annotation.Nullable
Warning:okio.SegmentPool: can't find referenced class javax.annotation.Nullable
Warning:okio.package-info: can't find referenced class javax.annotation.ParametersAreNonnullByDefault
Warning:there were 19 unresolved references to classes or interfaces.
Warning:Exception while processing task java.io.IOException: Please correct the above warnings first.
Error:Execution failed for task ':app:transformClassesAndResourcesWithProguardForAbi64Release'.
> Job failed, see logs for details
Information:BUILD FAILED in 23s
Information:1 error
Information:15 warnings
Information:See complete output in console
```

<br>

# **해결**
-------------------------

- **build.gradle**
    - lottie 버전을 2.8.0 미만으로 설정

```java
dependencies {
//    compile 'com.airbnb.android:lottie:3.0.7'
    compile 'com.airbnb.android:lottie:2.7.0'
}
```

<br>

- [[lottie github page](https://github.com/airbnb/lottie-android)] 하단에 아래와 같이 적혀있음

```java
Lottie 2.8.0 and above only supports projects that have been migrated to androidx. For more information, read Google's migration guide.
```

<br>

- androidx로 인해 빌드를 실패한 경우라 해결 함