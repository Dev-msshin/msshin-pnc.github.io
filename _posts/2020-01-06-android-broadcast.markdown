---
layout: post
title:  "Broadcast 다루기"
subtitle: "Broadcast 다루기"
date:   2020-01-06 17:52:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# 1. Broadcast **정적** 할당

- **AndroidManifest.xml**

```java
<receiver
    android:name=".StaticBroadcastReceiver"
    android:enabled="true">
    <intent-filter>
        <action android:name="ipl.action.test"/>
    </intent-filter>
</receiver>
```

- **StaticBroadcastReceiver.java**

```java
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

public class StaticBroadcastReceiver extends BroadcastReceiver {
    private String TAG = "StaticBroadcastReceiver";
    private final String ACTION_TEST = "ipl.action.test";
    private final String EXTRA_TEST = "ipl.extra.test";

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent != null) {
            String action = intent.getAction();
            Log.d(TAG, "onReceive() - action: " + intent.getAction());
            if (action != null) {
                switch (action) {
                    case ACTION_TEST:
                        String extra = intent.getStringExtra(EXTRA_TEST);
                        Log.d(TAG, "onReceive() - extra: "+extra);
                        break;
                }
            }
        }
    }
}
```

<br>

# 2. Broadcast **동적** 할당

- **DynamicBroadcastReceiver.java**

```java
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

public class DynamicBroadcastReceiver extends BroadcastReceiver {
    private String TAG = "DynamicBroadcastReceiver";
    public static final String ACTION_TEST = "ipl.action.test";
    private final String EXTRA_TEST = "ipl.extra.test";

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent != null) {
            String action = intent.getAction();
            Log.d(TAG, "onReceive() - action: " + intent.getAction());
            if (action != null) {
                switch (action) {
                    case ACTION_TEST:
                        String extra = intent.getStringExtra(EXTRA_TEST);
                        Log.d(TAG, "onReceive() - extra: "+extra);
                        break;
                }
            }
        }
    }
}
```

- **MainActivity.java**

```java
import android.app.Activity;
import android.content.BroadcastReceiver;
import android.content.IntentFilter;
import android.os.Bundle;
import android.util.Log;

public class MainActivity extends Activity {
    private final String TAG = getClass().getSimpleName();

    private BroadcastReceiver mBroadcastReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
    }

    private void init(){
        mBroadcastReceiver = new DynamicBroadcastReceiver();
    }

    @Override
    protected void onResume() {
        super.onResume();
        registerBroadcast();
    }

    @Override
    protected void onPause() {
        super.onPause();
        unregisterBroadcast();
    }

    private void registerBroadcast() {
        Log.d(TAG, "registerBroadcast()");
        if (mBroadcastReceiver != null) {
            IntentFilter filter = new IntentFilter();
            filter.addAction(DynamicBroadcastReceiver.ACTION_TEST);
            registerReceiver(mBroadcastReceiver, filter);
        }
    }
    private void unregisterBroadcast() {
        Log.d(TAG, "unregisterBroadcast()");
        if (mBroadcastReceiver != null) {
            unregisterReceiver(mBroadcastReceiver);
        }
    }
}
```

<br>

- [샘플 소스](https://github.com/msshin-pnc/broadcastSample)