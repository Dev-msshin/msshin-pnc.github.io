---
layout: post
title:  "Android 권한 요청"
subtitle: "Android 권한 요청"
date:   2020-01-07 14:20:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# **권한 종류**

-----------------

- [[안드로이드 권한 종류 링크](https://developer.android.com/reference/android/Manifest.permission.html?hl=ko)]

<br>





# **권한 요청 코드**

---------------------

ex) camera

* **AndroidManifest.xml**

```java
<uses-permission android:name="android.permission.CAMERA" />
```

<br>

* **build.gradle**

```java
dependencies {
    compile 'com.android.support:appcompat-v7:26.1.0'
}
```

<br>

* **MainActivity.java**

```java
package example.pnc.msshin.cameratest;

import android.Manifest;
import android.app.Activity;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.util.Log;

public class MainActivity extends Activity {
    private final String TAG = getClass().getSimpleName();
    private final int PERMISSION_REQUEST = 101;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (checkPermission()) {
            nextEvent();
        }
    }

    private boolean checkPermission() {
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this
                    , new String[]{Manifest.permission.CAMERA}
                    , PERMISSION_REQUEST);
            return false;
        }
        return true;
    }

    private boolean hasAllPermission() {
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED) {
            return true;
        }
        return false;
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode) {
            case PERMISSION_REQUEST: {
                if (hasAllPermission()) {
                    Log.d(TAG, "Permission granted");
                    nextEvent();
                } else {
                    Log.e(TAG, "Permission deny");
                    finish();
                }
            }
            break;
        }
    }

    public void nextEvent() {

    }
}
```