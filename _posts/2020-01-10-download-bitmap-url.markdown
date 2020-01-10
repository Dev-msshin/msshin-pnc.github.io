---
layout: post
title:  "URL로 Bitmap 다운로드"
subtitle: "Download bitmap task as Asynctask"
date:   2020-01-10 15:46:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- code

```java
import java.io.InputStream;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.AsyncTask;

public class DownloadImageTask extends AsyncTask<String, Void, Bitmap>{
    private OnResponseListener onResponseListener;

    public DownloadImageTask(OnResponseListener onResponseListener){
        this.onResponseListener = onResponseListener;
    }

    protected Bitmap doInBackground(String... urls){
        Bitmap bitmap = null;

        try{
            InputStream in = new java.net.URL(urls[0]).openStream();
            bitmap = BitmapFactory.decodeStream(in);
        }catch(Exception ex){
            ex.printStackTrace();
        }

        return bitmap;
    }

    protected void onPostExecute(Bitmap result){
        if (onResponseListener != null){
            onResponseListener.onResponse(result);
        }
    }

    public interface OnResponseListener{
        void onResponse(Bitmap bitmap);
    }
}
```