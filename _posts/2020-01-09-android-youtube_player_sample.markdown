---
layout: post
title:  "Youtube search/player sample"
subtitle: "Youtube search/player sample"
date:   2020-01-09 12:27:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- **MainActivity.java**

```java
package example.pnc.msshin.youtubeplayersample;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;

import java.util.ArrayList;

public class MainActivity extends Activity implements View.OnClickListener, AdapterView.OnItemClickListener {
    private final String TAG = getClass().getSimpleName();
    private final String YOUTUBE_API_KEY = ""; // 여기에 키를 입력해 주세요.

    private YoutubeHelper mYoutubeHelper;
    private Button mBtnSearch;
    private EditText mEditTextSearch;
    private ListView mListViewSearched;
    private YoutubeListViewAdapter mYoutubeListViewAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
    }

    private void init(){
        mBtnSearch = findViewById(R.id.btn_search);
        mEditTextSearch = findViewById(R.id.edittext_search);
        mListViewSearched = findViewById(R.id.listview_searched);
        mYoutubeListViewAdapter = new YoutubeListViewAdapter(this);
        mYoutubeHelper = new YoutubeHelper(this, YOUTUBE_API_KEY);

        mBtnSearch.setOnClickListener(this);
        mListViewSearched.setOnItemClickListener(this);
        mListViewSearched.setAdapter(mYoutubeListViewAdapter);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btn_search:
                searchYoutube(mEditTextSearch.getText().toString(), 10);
                break;
        }
    }

    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        switch (view.getId()){
            case R.id.listview_searched:
                YouTubeInfo youTubeInfo = mYoutubeListViewAdapter.getItem(position);
                playYoutube(youTubeInfo);
                break;
        }
    }

    private void searchYoutube(String searchKey, int maxItem){
        if (mYoutubeHelper != null){
            mYoutubeHelper.search(searchKey, maxItem, new YoutubeSearchTask.OnResponseListener() {
                @Override
                public void onResponse(ArrayList<YouTubeInfo> youtubeInfoList) {
                    mYoutubeListViewAdapter.setYoutubeInfoList(youtubeInfoList);
                    mYoutubeListViewAdapter.notifyDataSetChanged();
                }
            });
        }
    }

    private void playYoutube(YouTubeInfo youTubeInfo){
        if (youTubeInfo != null) {
            String videoId = youTubeInfo.getVideoId();
            Log.d(TAG, "load video id: " + videoId);
            Intent intent = new Intent(this, YouTubePlayerActivity.class);
            intent.putExtra("API_KEY", YOUTUBE_API_KEY);
            intent.putExtra("VIDEO_ID", videoId);
            startActivity(intent);
        }
    }
}
```

<br>

- [[샘플 전체 코드](https://github.com/msshin-pnc/youtubePlayerSample)]