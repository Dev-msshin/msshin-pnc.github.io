---
layout: post
title:  "Gson 사용법"
subtitle: "Android gson 사용법"
date:   2020-01-10 13:06:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

- **app/build.gradle** dependencies 추가

```java
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.google.code.gson:gson:2.8.2'
}
```

<br>
<br>

- Sample code

```java
public class SampleResponse implements Cloneable {
    public String id;
    public String ref;
    public String timestamp;
    public String lang;
    
    // Json -> Gson
    public String parseGsonToJsonString() {        
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(this);
    }
    
    // Gson -> Json
    public SampleResponse parseJsonStringToGson(String jsonStr) {
        if (jsonStr == null){
            return null;
        }
        
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        SampleResponse sampleResponse = gson.fromJson(strResponse, NLPResponse.class);
        this = sampleResponse.clone();
        return this;
    }
    
    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }    
}
```