---
layout: post
title:  "싱글 라인 텍스트뷰"
subtitle: "SingleLineTextView"
date:   2020-01-21 15:50:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
package global.ipl.daris.t1_local.setting.view;

import android.content.Context;
import android.graphics.Paint;
import android.graphics.Rect;
import android.support.annotation.Nullable;
import android.support.v7.widget.AppCompatTextView;
import android.text.TextUtils;
import android.util.AttributeSet;
import android.util.Log;
import android.util.TypedValue;
import android.widget.TextView;

import global.ipl.daris.t1_local.setting.BuildConfig;

/**
 * Created by msShin on 2020-01-21.
 */

public class SingleLineTextView extends AppCompatTextView {
    private static String TAG = "SingleLineTextView";
    public SingleLineTextView(Context context) {
        super(context);
    }

    public SingleLineTextView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public SingleLineTextView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        super.onLayout(changed, left, top, right, bottom);
        refreshTextSize();
    }

    @Override
    protected void onTextChanged(CharSequence text, int start, int lengthBefore, int lengthAfter) {
        super.onTextChanged(text, start, lengthBefore, lengthAfter);
        refreshTextSize();
    }

    private void refreshTextSize(){
        int lineCnt = getLineCount();
        if (BuildConfig.DEBUG) Log.d(TAG, getWidth()+", "+getHeight()+", "+getTextSize()+", "+lineCnt+", "+getText());
        if (lineCnt > 1){
            float textSize = getTextSizeToSingleLine();
            if (textSize > 9){
                if (BuildConfig.DEBUG) Log.d(TAG, "set text size: "+textSize);
                this.setTextSize(TypedValue.COMPLEX_UNIT_PX, textSize);
            }else {
                setSingleLineTextView(this);
            }
        }
    }

    private void setSingleLineTextView(TextView textView){
        textView.setMaxLines(1);
        textView.setEllipsize(TextUtils.TruncateAt.END);
    }

    private float getTextSizeToSingleLine(){
        float textSize = getTextSize();
        String textStr = getText().toString();
        while (textSize > 0){
            int textLines = getTextLines(textSize, getWidth() - 20 > 0 ? getWidth() - 20 : getWidth(), textStr);
            if (textLines < 2){
                break;
            }
            textSize -= 1;
        }
        return textSize;
    }

    private int getTextLines(float currentTextSize, float currentViewWidth, String textString){
        final Rect bounds = new Rect();
        final Paint paint = new Paint();
        paint.setTextSize(currentTextSize);
        paint.getTextBounds(textString, 0, textString.length(), bounds);
        return (int) Math.ceil((float) bounds.width() / currentViewWidth);
    }
}

```