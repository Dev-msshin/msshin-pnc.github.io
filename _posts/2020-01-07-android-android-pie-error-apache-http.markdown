---
layout: post
title:  "Apache http에 의해 죽는 문제"
subtitle: "Apache http에 의해 죽는 문제"
date:   2020-01-07 14:46:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# **문제**
-------------------------
- Android 9.0 이상에서 Apache http 실행 시 아래 로그와 함께 앱이 죽음

```java
AndroidRuntime: FATAL EXCEPTION: Thread-5 
AndroidRuntime: Process: global.ipl.daris.t1.ijinihub, PID: 26776 
AndroidRuntime: java.lang.NoClassDefFoundError: Failed resolution of: Lorg/apache/http/ProtocolVersion; 
AndroidRuntime: 	at com.android.volley.toolbox.HurlStack.performRequest(HurlStack.java:109) 
AndroidRuntime: 	at com.android.volley.toolbox.BasicNetwork.performRequest(BasicNetwork.java:97) 
AndroidRuntime: 	at com.android.volley.NetworkDispatcher.run(NetworkDispatcher.java:114) 
AndroidRuntime: Caused by: java.lang.ClassNotFoundException: Didnt find class "org.apache.http.ProtocolVersion" on path: DexPathList[[zip file "/data/app/global.ipl.daris.t1.ijinihub-OsOdQnEh-rX8JnoiMFGHIw==/base.apk"],nativeLibraryDirectories=[/data/app/global.ipl.daris.t1.ijinihub-OsOdQnEh-rX8JnoiMFGHIw==/lib/arm, /data/app/global.ipl.daris.t1.ijinihub-OsOdQnEh-rX8JnoiMFGHIw==/base.apk!/lib/armeabi-v7a, /system/lib]] 
AndroidRuntime: 	at dalvik.system.BaseDexClassLoader.findClass(BaseDexClassLoader.java:134) 
AndroidRuntime: 	at java.lang.ClassLoader.loadClass(ClassLoader.java:379) 
AndroidRuntime: 	at java.lang.ClassLoader.loadClass(ClassLoader.java:312) 
AndroidRuntime: 	... 3 more 
```

<br>

# **해결**
-------------------------

- **AndroidManifest.xml**
    - **usesCleartextTraffic** 활성화, **org.apache.http.legacy** 비활성화

```java
<application
        android:usesCleartextTraffic="true">
        <uses-library
            android:name="org.apache.http.legacy"
            android:required="false" />
</application>
```