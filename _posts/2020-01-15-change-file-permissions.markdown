---
layout: post
title:  "파일 권한 변경하기"
subtitle: "파일 권한 변경"
date:   2020-01-15 09:37:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
private void testCode(){
    File file = new File("/Test/test.cfg");
    if (file.exists()){
        Log.d(TAG, "[test] file exists");
        try{
            int i = changePermissons(file,0777);
            Log.d(TAG, "[test] change permissions : "+i);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

public int changePermissons(File path, int mode) throws Exception {
    Class<?> fileUtils = Class.forName("android.os.FileUtils");
    Method setPermissions = fileUtils.getMethod("setPermissions",
            String.class, int.class, int.class, int.class);

    return (Integer) setPermissions.invoke(null, path.getAbsolutePath(),
            mode, -1, -1);
}
```