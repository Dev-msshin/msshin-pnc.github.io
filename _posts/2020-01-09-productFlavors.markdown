---
layout: post
title:  "productFlavors 사용하기"
subtitle: "Build variant 다르게 만들기"
date:   2020-01-09 15:21:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# abi32, abi64 빌드 파일 두개 만들기

-----------------------------------------------

- app/build.gradle

```java
android{
    productFlavors {
        abi32 {
            buildConfigField 'boolean', 'Is_64Bit', "false"
    
            ndk{
                abiFilters 'armeabi', 'armeabi-v7a'
            }
        }
    
        abi64 {
            buildConfigField 'boolean', 'Is_64Bit', "true"
    
            ndk{
                abiFilters 'armeabi', 'arm64-v8a', 'armeabi-v7a'
            }
        }
    }
}
```
- ndk 다르게 사용 가능
    
- **BuildConfig.Is_64Bit**로 사용

```java
public class MainActivity extends Activity{
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (BuildConfig.Is_64Bit){
            
        }
    }
}
```