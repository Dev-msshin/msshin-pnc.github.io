---
layout: post
title:  "HttpURLConnection과 AsyncTask로 url에서 데이터 전송 받기"
subtitle: "Receive data transfer from url to HttpURLConnection and AsyncTask"
date:   2020-01-10 15:55:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- **GetDataTask.java**

<hr>

```java
import android.os.AsyncTask;
import android.util.Log;

import java.io.IOException;

/**
 * Created by msshin-pnc on 2020-01-10.
 */

public class GetDataTask extends AsyncTask<String, Void, String> {
    private final static String TAG = "GetDataTask";

    private HttpConnectionHelper httpConnectionHelper;
    private OnDataReceivedListener onDataReceivedListener;

    public GetDataTask(OnDataReceivedListener onDataReceivedListener) {
        this.onDataReceivedListener = onDataReceivedListener;
    }
    @Override
    protected void onPreExecute() {
        super.onPreExecute();
    }

    @Override
    protected String doInBackground(String... params) {
        setupConnection(params[0]);
        String res= getResponseData();
        Log.d(TAG, "URL : " + params[0]);
        Log.d(TAG, "getResponseData : " + res);

        return res;
    }

    @Override
    protected void onPostExecute(String data) {
        if(onDataReceivedListener != null) {
            onDataReceivedListener.onDataReceived(data);
        }
    }



    private void setupConnection(String url) {
        try {
            httpConnectionHelper = new HttpConnectionHelper(url);
            httpConnectionHelper.openHttpURLConnection(HttpConnectionHelper.GET, 5000);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private String getResponseData() {
        StringBuilder resData = new StringBuilder();
        try {
            resData.append(httpConnectionHelper.getResponseStringData());
        } catch (IOException e) {
            e.printStackTrace();
        }
        return resData.toString();
    }

    public interface OnDataReceivedListener {
        void onDataReceived(String data);
    }
}
```

<br>

- **HttpConnectionHelper.java**

<hr>

```java
import android.util.Log;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.ProtocolException;
import java.net.URL;

/**
 * Created by msshin-pnc on 2020-01-10.
 */

public class HttpConnectionHelper {
    private final static String TAG = "HttpConnectionHelper";

    public final static String GET = "GET";
    public final static String POST = "POST";

    private HttpURLConnection httpURLConnection;
    private URL url;

    public HttpConnectionHelper(String urlString) {
        url = makeURL(urlString);
    }

    public HttpURLConnection openHttpURLConnection(String requestMethod, int connectTimeout) throws IOException {
        httpURLConnection = null;
        if(url != null) {
            httpURLConnection = (HttpURLConnection) url.openConnection();
            httpURLConnection.setConnectTimeout(connectTimeout);
            if (requestMethod.equals(POST)) {
                httpURLConnection.setDoOutput(true);
            }
            httpURLConnection.setRequestMethod(requestMethod);
        }
        return httpURLConnection;
    }

    public String getResponseStringData() throws IOException{
        String res = null;
        if (httpURLConnection != null) {
            try {

                int resCode = httpURLConnection.getResponseCode();
                Log.d(TAG, "getResponseCode : " + resCode);
                if(resCode == HttpURLConnection.HTTP_OK) {
                    BufferedReader reader = new BufferedReader(new InputStreamReader(
                            httpURLConnection.getInputStream()));
                    StringBuilder builder = new StringBuilder();
                    while (true) {
                        String temp = reader.readLine();
                        if (temp != null) {
                            builder.append(temp);
                        } else {
                            break;
                        }
                    }
                    reader.close();
                    res = builder.toString();
                }
            } catch (ProtocolException e) {
                e.printStackTrace();
            } finally {
                disConnect();
            }
        }
        return res;
    }

    public void disConnect() {
        if(httpURLConnection != null) {
            httpURLConnection.disconnect();
            httpURLConnection = null;
        }
    }

    public URL makeURL(String urlString) {
        URL url = null;
        try {
            url = new URL(urlString);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return url;
    }
}
```