---
layout: post
title:  "FTP 전송"
subtitle: "FTP 전송"
date:   2020-01-07 15:27:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# FTP 전송 샘플 코드

--------------------

- **AndroidManifest.xml**

```java
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

<br>

- **libs**

```java
commons-net-3.6.jar
```

<br>

- **app/builde.gradle**

```java
dependencies {
    compile files('libs/commons-net-3.6.jar')
}
```

<br>

- **/builde.gradle**

```java
allprojects {
    repositories {
        flatDir{
            dirs 'libs'
        }
    }
}
```

<br>

- **MainActivity.java**

```java
package example.pnc.msshin.transferftpsample;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

public class MainActivity extends Activity implements View.OnClickListener {
    private final String TAG = getClass().getSimpleName();

    private ProcessFTP mProcessFTP;
    private Button mSendBtn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
    }

    private void init(){
        mSendBtn = findViewById(R.id.send);
        mSendBtn.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.send:
                transferFTP(
                        "",
                        "",
                        "",
                        "",
                        "");
                break;
        }
    }

    private void transferFTP(String url, String id, String pw, final String transFilePath, final String toFolderPath){
        if (mProcessFTP == null || !mProcessFTP.isProcessing()) {
            mProcessFTP = new ProcessFTP(url, id, pw);
            mProcessFTP.connect(new ProcessFTP.OnEventListener() {
                @Override
                public void onResult(int resultCode, String addString) {
                    Log.d(TAG, "connect() onResult - resultCode : " + resultCode + ", addString : " + addString);
                    if (resultCode == ProcessFTP.EVENT_SUCCESS) {
                        if (mProcessFTP != null) {
                            mProcessFTP.upload(new ProcessFTP.OnEventListener() {
                                @Override
                                public void onResult(int resultCode, String addString) {
                                    Log.d(TAG, "upload() onResult - resultCode : " + resultCode + ", addString : " + addString);
                                    if (resultCode == ProcessFTP.EVENT_SUCCESS) {
                                        Log.d(TAG, "transfer success");
                                    }
                                    if (mProcessFTP != null) {
                                        mProcessFTP.disconnect(new ProcessFTP.OnEventListener() {
                                            @Override
                                            public void onResult(int resultCode, String addString) {
                                                Log.d(TAG, "disconnect() onResult - resultCode : " + resultCode + ", addString : " + addString);
                                            }
                                        });
                                    }
                                }
                            }, transFilePath, toFolderPath);
                        }
                    }
                }
            });
        }
    }
}
```

<br>

- **activity_main.xml**

```java
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"/>

</RelativeLayout>
```

<br>

- **ProcessFTP.java**

```java
package example.pnc.msshin.transferftpsample;

import android.os.AsyncTask;
import android.util.Log;

import org.apache.commons.net.ftp.FTP;
import org.apache.commons.net.ftp.FTPClient;
import org.apache.commons.net.ftp.FTPFile;
import org.apache.commons.net.io.CopyStreamAdapter;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class ProcessFTP {
    private final String TAG = "ProcessFTP";
    private final String FTP_CONNECT = "CONNECT";
    private final String FTP_CONNECT_TAG = "CONNECT - ";
    private final String FTP_DISCONNECT = "DISCONNECT";
    private final String FTP_DISCONNECT_TAG = "DISCONNECT - ";
    private final String FTP_UPLOAD = "UPLOAD";
    private final String FTP_UPLOAD_TAG = "UPLOAD - ";
    private final String FTP_DOWNLOAD = "DOWNLOAD";
    private final String FTP_DOWNLOAD_TAG = "DOWNLOAD - ";
    private boolean DEBUG = BuildConfig.DEBUG;

    public static final int EVENT_FAIL = 0;
    public static final int EVENT_SUCCESS = 1;

    private String FTP_URL;
    private String FTP_ID;
    private String FTP_PW;
    private FTPClient ftp = null;
    private FileInputStream fis = null;
    private FileOutputStream fos = null;
    private File uploadFile;
    private File downloadFile;
    private boolean progress_state = false;
    private long intTotalFileSize = 0;
    private static ProcessFTP mInstance;
    private OnEventListener onEventListener;

    public static ProcessFTP getInstance(){
        if (mInstance == null){
            mInstance = new ProcessFTP();
        }
        return mInstance;
    }

    public static ProcessFTP getInstance(String url, String id, String pw){
        if (mInstance == null){
            mInstance = new ProcessFTP(url, id, pw);
        }
        return mInstance;
    }

    public ProcessFTP(){
        mInstance = this;
    }

    public ProcessFTP(String url, String id, String pw){
        new ProcessFTP();
        FTP_URL = url;
        FTP_ID = id;
        FTP_PW = pw;
    }

    public void connect(OnEventListener onEventListener){
        this.onEventListener = onEventListener;
        new ConnTask().execute(FTP_CONNECT);
    }

    public void disconnect(OnEventListener onEventListener){
        this.onEventListener = onEventListener;
        new ConnTask().execute(FTP_DISCONNECT);
    }

    public void upload(OnEventListener onEventListener, String filePath, String targetFolder){
        if (DEBUG) Log.d(TAG, "upload() - filePath : "+filePath+"\ntargetFolder : "+targetFolder);
        this.onEventListener = onEventListener;
        uploadFile = new File(filePath);
        progress_state = true;
        new ConnTask().execute(FTP_UPLOAD, targetFolder);
    }

    public void download(OnEventListener onEventListener, String path){
        this.onEventListener = onEventListener;
        downloadFile = new File(path);
        progress_state = true;
        new ConnTask().execute(FTP_DOWNLOAD, path);
    }

    private class ConnTask extends AsyncTask<String, String, String>
    {
        protected void onProgressUpdate(String... progress) {
        }

        @SuppressWarnings("deprecation")
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            if (DEBUG) Log.d(TAG, "ConnTask - start");
            if(progress_state){

            }
        }

        @Override
        protected void onPostExecute(String result) {
            super.onPostExecute(result);
            progress_state = false;
        }

        @Override
        protected String doInBackground(String... params) {
            boolean isResponsed = false;
            switch (params[0]){
                case FTP_CONNECT:{
                    Log.d(TAG, FTP_CONNECT_TAG+"start");
                    try {
                        ftp = new FTPClient();
                        ftp.setControlEncoding("UTF-8");
                        ftp.connect(FTP_URL);
                        ftp.login(FTP_ID, FTP_PW);
                        ftp.enterLocalPassiveMode();
                        Log.d(TAG, FTP_CONNECT_TAG+"success");
                        FTPFile[] ftpFiles = ftp.listFiles("/");
                        if(ftpFiles != null){
                            for(FTPFile file : ftpFiles){
                                if (DEBUG) Log.d(TAG,FTP_CONNECT_TAG+"ftpFile = "+file);
                            }
                        }
                    } catch (Exception e) {
                        Log.e(TAG, FTP_CONNECT_TAG+e);
                        if (onEventListener != null && !isResponsed) {
                            isResponsed = true;
                            onEventListener.onResult(EVENT_FAIL, e.getMessage());
                        }
                    }
                    Log.d(TAG, FTP_CONNECT_TAG+"end");
                    if (onEventListener != null && !isResponsed) {
                        isResponsed = true;
                        onEventListener.onResult(EVENT_SUCCESS, "");
                    }
                }
                break;
                case FTP_DISCONNECT: {
                    if (DEBUG) Log.d(TAG, FTP_DISCONNECT_TAG+"start");
                    if (ftp != null && ftp.isConnected()) {
                        try {
                            ftp.disconnect();
                            if (DEBUG) Log.d(TAG, FTP_DISCONNECT_TAG+"success");
                        } catch (IOException e) {
                            Log.e(TAG, FTP_DISCONNECT_TAG+e);
                            if (onEventListener != null && !isResponsed){
                                isResponsed = true;
                                onEventListener.onResult(EVENT_FAIL, e.getMessage());
                                isResponsed = true;
                            }
                        }
                    } else {
                        if (DEBUG) Log.d(TAG, FTP_DISCONNECT_TAG+"else");
                        if (onEventListener != null && !isResponsed){
                            isResponsed = true;
                            if (ftp == null) {
                                onEventListener.onResult(EVENT_FAIL, "is not connected");
                            }else{
                                onEventListener.onResult(EVENT_FAIL, "is connected");
                            }
                        }
                    }
                    Log.d(TAG, FTP_DISCONNECT_TAG+"end");
                    if (onEventListener != null && !isResponsed) {
                        isResponsed = true;
                        onEventListener.onResult(EVENT_SUCCESS, "");
                    }
                }
                break;
                case FTP_UPLOAD: {
                    Log.d(TAG, FTP_UPLOAD_TAG + "start");
                    if (params[1] != null) {
                        String targetFolder = params[1];
                        if (DEBUG) Log.d(TAG, FTP_UPLOAD_TAG + params.length + ", " + targetFolder);
                        try {
                            ftp.changeWorkingDirectory(targetFolder);
                            ftp.setFileType(FTP.BINARY_FILE_TYPE);

                            fis = new FileInputStream(uploadFile);
                            intTotalFileSize = fis.getChannel().size();
                            ftp.setCopyStreamListener(new CopyStreamAdapter() {
                                @Override
                                public void bytesTransferred(long arg0, int arg1, long arg2) {
                                    super.bytesTransferred(arg0, arg1, arg2);
                                    int percent = (int) (arg0 * 100 / intTotalFileSize);
                                    publishProgress("" + percent);
                                }
                            });
                            ftp.storeFile(uploadFile.getName(), fis);

                            Log.d(TAG, FTP_UPLOAD_TAG + "success");
                        } catch (Exception e) {
                            Log.e(TAG, FTP_UPLOAD_TAG + e);
                            if (onEventListener != null && !isResponsed) {
                                isResponsed = true;
                                onEventListener.onResult(EVENT_FAIL, e.getMessage());
                            }
                        } finally {
                            if (fis != null) {
                                try {
                                    fis.close();
                                } catch (IOException e) {
                                    Log.e(TAG, FTP_UPLOAD_TAG + "fis.close()= " + e);
                                }
                            }
                        }
                        Log.d(TAG, FTP_UPLOAD_TAG + "end");
                        Logout();
                    }else{
                        Log.e(TAG, FTP_UPLOAD_TAG + "url is null");
                        if (onEventListener != null && !isResponsed) {
                            isResponsed = true;
                            onEventListener.onResult(EVENT_FAIL, "url is null");
                        }
                    }
                    if (onEventListener != null && !isResponsed) {
                        isResponsed = true;
                        onEventListener.onResult(EVENT_SUCCESS, "");
                    }
                }
                break;
                case FTP_DOWNLOAD: {
                    if (DEBUG) Log.d(TAG, FTP_DOWNLOAD_TAG + "start");
                    try {
                        ftp.changeWorkingDirectory("/");
                        ftp.setFileType(FTP.BINARY_FILE_TYPE);

                        fos = new FileOutputStream(downloadFile);

                        boolean isSuccess = ftp.retrieveFile(params[1], fos);
                        if (DEBUG) Log.d(TAG, FTP_DOWNLOAD_TAG + "success");
                        if (onEventListener != null && !isResponsed) {
                            isResponsed = true;
                            if (!isSuccess) {
                                onEventListener.onResult(EVENT_FAIL, "download failed");
                            }
                        }
                    } catch (Exception e) {
                        Log.e(TAG, FTP_DOWNLOAD_TAG +  e);
                        if (onEventListener != null && !isResponsed) {
                            isResponsed = true;
                            onEventListener.onResult(EVENT_FAIL, e.getMessage());
                        }
                    }finally {
                        if (fos != null) {
                            try {
                                fos.close();
                            } catch (IOException e) {
                                Log.e(TAG, FTP_DOWNLOAD_TAG + "fos.close() = " + e);
                            }
                        }
                    }
                    if (DEBUG) Log.d(TAG, FTP_DOWNLOAD_TAG + "end");
                    Logout();
                    if (onEventListener != null && !isResponsed) {
                        isResponsed = true;
                        onEventListener.onResult(EVENT_SUCCESS, "");
                    }
                }
                break;
            }
            return null;
        }
    }

    private void Logout() {
        if (DEBUG) Log.i(TAG, "Logout");
        try {
            ftp.logout();// FTP Log Out
            if (DEBUG) Log.i(TAG, "Logout / success");
        }
        catch (IOException e) {
            Log.e(TAG, "FileUpload / fis.logout();= " + e);
        }
        if (DEBUG) Log.i(TAG, "Logout / end");
    }

    public boolean isProcessing(){
        return progress_state;
    }

    public interface OnEventListener{
        void onResult(int resultCode, String addString);
    }
}
```

<br>

- [Github 샘플 코드](https://github.com/msshin-pnc/TransferFTPSample)