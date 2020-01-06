---
layout: post
title:  "[Android Studio] Bitmap 다루기"
date:   2020-01-06 16:15:00 +0900
categories: android
comments: true
---

# 1. **Bitmap 크기 조절(resize)**

- **[1-1]createScaledBitmap**를 사용하면 이미지가 흐릿해진다. 따라서 **[1-2]Canvas, Matrix**를 이용하면 좋다.

## 1-1. width, height 모두 조절

### 1-1-1. createScaledBitmap 이용 

```java
/**
 * @param original original bitmap
 * @param resizeWidth resize width (0: keep width, Less then 0: maintain ratio)
 * @param resizeHeight resize height (0: keep height, Less then 0: maintain ratio)
 * @return resized bitmap
 */
static public Bitmap resizeBitmap(Bitmap original, int resizeWidth, int resizeHeight) {
    if (resizeWidth <= 0) {
        if (resizeWidth < 0){
            float ratio = (float) original.getWidth() / (float) original.getHeight();
            resizeWidth = (int)((float)resizeHeight * ratio);
        }else {
            resizeWidth = original.getWidth();
        }
    }
    if (resizeHeight <= 0) {
        if (resizeHeight < 0){
            float ratio = (float) original.getHeight() / (float) original.getWidth();
            resizeHeight = (int)((float)resizeWidth * ratio);
        }else{
            resizeHeight = original.getHeight();
        }
    }
    return Bitmap.createScaledBitmap(original, resizeWidth, resizeHeight, false);
}
```

### 1-1-2. Canvas, Matrix 이용

```java
/**
 * @param original original bitmap
 * @param resizeWidth resize width (0: keep width, Less then 0: maintain ratio)
 * @param resizeHeight resize height (0: keep height, Less then 0: maintain ratio)
 * @return resized bitmap
 */
static public Bitmap resizeBitmap(Bitmap original, int resizeWidth, int resizeHeight) {
    if (resizeWidth <= 0) {
        if (resizeWidth < 0){
            float ratio = (float) original.getWidth() / (float) original.getHeight();
            resizeWidth = (int)((float)resizeHeight * ratio);
        }else {
            resizeWidth = original.getWidth();
        }
    }
    if (resizeHeight <= 0) {
        if (resizeHeight < 0){
            float ratio = (float) original.getHeight() / (float) original.getWidth();
            resizeHeight = (int)((float)resizeWidth * ratio);
        }else{
            resizeHeight = original.getHeight();
        }
    }
    
    int newWidth = resizeWidth;
    int newHeight = resizeHeight;

    Bitmap scaledBitmap = Bitmap.createBitmap(newWidth, newHeight, Bitmap.Config.ARGB_8888);

    float ratioX = newWidth / (float) original.getWidth();
    float ratioY = newHeight / (float) original.getHeight();
    float middleX = newWidth / 2.0f;
    float middleY = newHeight / 2.0f;

    Matrix scaleMatrix = new Matrix();
    scaleMatrix.setScale(ratioX, ratioY, middleX, middleY);

    Canvas canvas = new Canvas(scaledBitmap);
    canvas.setMatrix(scaleMatrix);
    canvas.drawBitmap(original, middleX - original.getWidth() / 2, middleY - original.getHeight() / 2, new Paint(Paint.FILTER_BITMAP_FLAG));

    return scaledBitmap;
}
```

## 1-2. width, height 둘 중 가장 작은 것(혹은 큰 것)을 조절 (비율 유지)

```java
/**
 *
 * @param original original bitmap
 * @param resize resize width or height
 * @param isResizeMin true: resize the smaller of width or height, false: resize the bigger of width or height
 * @return resized bitmap
 */
static public Bitmap resizeBitmap(Bitmap original, int resize, boolean isResizeMin) {
    int resizeWidth;
    int resizeHeight;
    if (isResizeMin){
        if (original.getWidth() > original.getHeight()){
            float ratio = (float) original.getWidth() / (float) original.getHeight();
            resizeWidth = (int)((float)resize * ratio);
            resizeHeight = resize;
        }else{
            float ratio = (float) original.getHeight() / (float) original.getWidth();
            resizeWidth = resize;
            resizeHeight = (int)((float)resizeWidth * ratio);
        }
    }else{
        if (original.getWidth() > original.getHeight()){
            float ratio = (float) original.getHeight() / (float) original.getWidth();
            resizeWidth = resize;
            resizeHeight = (int)((float)resizeWidth * ratio);
        }else{
            float ratio = (float) original.getWidth() / (float) original.getHeight();
            resizeWidth = (int)((float)resize * ratio);
            resizeHeight = resize;
        }
    }
    return Bitmap.createScaledBitmap(original, resizeWidth, resizeHeight, false);
}
```

--------------------------------------

# 2. **Bitmap 자르기(Crop)**

## 2-1. 비율로 자르기

```java
static public Bitmap cropBitmap(Bitmap original, float x_start, float x_end, float y_start, float y_end) {
    float x = original.getWidth() * x_start;
    float y = original.getHeight() * y_start;
    float xEnd = original.getWidth() * x_end;
    float yEnd = original.getHeight() * y_end;
    float width = xEnd - x;
    float height = yEnd - y;
    return Bitmap.createBitmap(original
            , (int) x
            , (int) y
            , (int) width
            , (int) height);
}
```

## 2-2. 위치 값으로 자르기

```java
static public Bitmap cropBitmap(Bitmap original, int x_start, int x_end, int y_start, int y_end) {
    return Bitmap.createBitmap(original
            , (int) x_start
            , (int) y_start
            , (int) x_end
            , (int) y_end);
}
```

---------------------------------------------------------

# 3. **Bitmap 회전**

```java
static public Bitmap rotateImage(Bitmap source, float angle) {
    Matrix matrix = new Matrix();
    matrix.preRotate(angle, 0, 0);
    return Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight(),
            matrix, true);
}
```

---------------------------------------------------------

# 4. **Bitmap 반전**

```java
/**
 * @param isLR : Left and right
 * @param isTB : Top and bottom
 */
static public Bitmap inversionImage(Bitmap source, boolean isLR, boolean isTB) {
    Matrix sideInversion = new Matrix();
    if (isLR) {
        sideInversion.setScale(-1, 1);
    }
    if (isTB) {
        sideInversion.setScale(1, -1);
    }
    return Bitmap.createBitmap(source, 0, 0,
            source.getWidth(), source.getHeight(), sideInversion, false);
}
```

-------------------------------------------------------------

# 5. **Bitmap 정사각형(square)으로 만들기**

```java
static public Bitmap getSquareBitmap(Bitmap original, int MAX_LENGTH) {
    double aspectRatio = (double) original.getHeight() / (double) original.getWidth();
    if (MAX_LENGTH < 0) {
        MAX_LENGTH = 120;
    }
    int targetWidth, targetHeight;
    int startW = 0;
    int startH = 0;
    if (aspectRatio >= 1) {
        targetWidth = MAX_LENGTH;
        targetHeight = (int) (targetWidth * aspectRatio);
        startH = (targetHeight - targetWidth) / 2;
    } else {
        targetHeight = MAX_LENGTH;
        targetWidth = (int) (targetHeight / aspectRatio);
        startW = (targetWidth - targetHeight) / 2;
    }
    Bitmap originalResizeBmp = Bitmap.createScaledBitmap(original, targetWidth, targetHeight, false);
    return Bitmap.createBitmap(originalResizeBmp, startW, startH
            , (targetWidth > targetHeight ? targetHeight : targetWidth)
            , (targetWidth > targetHeight ? targetHeight : targetWidth));
}
```

-------------------------------------------------------------

# 6. **Bitmap 덮어쓰기(overlay)**

- **overlayAlpha**가 클 수록 **overlayBitmap**이 안 보임

```java
/**
* @param overlayAlpha 0-255
*/
static public Bitmap overlayBitmap(Bitmap original, Bitmap overlayBitmap, int overlayAlpha) {
    if (overlayAlpha < 0) {
        overlayAlpha = 125;
    }
    int bgWidth = original.getWidth() > overlayBitmap.getWidth() ?
            original.getWidth() : overlayBitmap.getWidth();
    int bgHeight = original.getHeight() > overlayBitmap.getHeight() ?
            original.getHeight() : overlayBitmap.getHeight();
    Bitmap bgBmp = Bitmap.createBitmap(bgWidth, bgHeight, Bitmap.Config.ARGB_8888);
    Paint alphaPaint = new Paint();
    alphaPaint.setAlpha(overlayAlpha);
    Canvas canvas = new Canvas(bgBmp);
    canvas.drawBitmap(original, new Matrix(), null);
    canvas.drawBitmap(overlayBitmap, new Matrix(), alphaPaint);

    return bgBmp;
}
```

-------------------------------------------------------------

# 7. **Bitmap 붙이기(merge)**

- **position**에 **partTwo**의 위치를 정하면 됨
> ex) **partOne** 왼쪽에 **partTwo**를 붙이고 싶다면 **position = 1**

```java
/**
 * @param position : partTwo position (1 - left, 2 - top, 3 - right, 4 - bottom)
 */
static public Bitmap mergeMultiple(Bitmap partOne, Bitmap partTwo, int position){
    int width = partOne.getWidth() + partTwo.getWidth();
    int height = partOne.getHeight() + partTwo.getHeight();
    switch (position){
        case 1: // left
        case 3: // right
            width = partOne.getWidth() + partTwo.getWidth();
            height = partOne.getHeight() > partTwo.getHeight() ? partOne.getHeight() : partTwo.getHeight();
            break;
        case 2: // top
        case 4: // bottom
            width = partOne.getWidth() > partTwo.getWidth() ? partOne.getWidth() : partTwo.getWidth();
            height = partOne.getHeight() + partTwo.getHeight();
            break;
    }
    Bitmap result = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);
    Canvas canvas = new Canvas(result);
    Paint paint = new Paint();
    switch (position){
        case 1: // left
            canvas.drawBitmap(partTwo, 0, 0, paint);
            canvas.drawBitmap(partOne, partTwo.getWidth(), 0, paint);
            break;
        case 2: // top
            canvas.drawBitmap(partTwo, 0, 0, paint);
            canvas.drawBitmap(partOne, 0, partTwo.getHeight(), paint);
            break;
        case 3: // right
            canvas.drawBitmap(partOne, 0, 0, paint);
            canvas.drawBitmap(partTwo, partOne.getWidth(), 0, paint);
            break;
        case 4: // bottom
            canvas.drawBitmap(partOne, 0, 0, paint);
            canvas.drawBitmap(partTwo, 0, partOne.getHeight(), paint);
            break;
    }
    return result;
}
```

-------------------------------------------------------------

# 8. **Bitmap 특정 색을 다른 색으로 바꾸기** (Replace a specific color with a different color)

```java
static public Bitmap replaceSpecificPixelColor(Bitmap originBitmap, int specificColor, int replaceColor) {
    if(replaceColor < 0){
        replaceColor = Color.TRANSPARENT;
    }
    
    int sW = originBitmap.getWidth();
    int sH = originBitmap.getHeight();

    int[] pixels = new int[sW * sH];
    originBitmap.getPixels(pixels, 0, sW, 0, 0, sW, sH);
    for (int i = 0; i < pixels.length; i++) {
        if (pixels[i] == specificColor)
            pixels[i] = replaceColor;
    }
    Bitmap newBitmap = Bitmap.createBitmap(originBitmap);
    newBitmap.setPixels(pixels, 0, sW, 0, 0, sW, sH);
    return newBitmap;
}
```

-------------------------------------------------------------

# 9. **byte array**, **bitmap** 변환

```java
static public Bitmap byteArrayToBitmap(byte[] data) {
    BitmapFactory.Options options = new BitmapFactory.Options();
    options.inPreferredConfig = Bitmap.Config.ARGB_8888;
    return BitmapFactory.decodeByteArray(data, 0, data.length, options);
}
```

```java
static public byte[] bitmapToByteArray(Bitmap original) {
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    original.compress(CompressFormat.JPEG, 100, stream);
    return stream.toByteArray();
}
```

-------------------------------------------------------------

# 10. **Bitmap**을 **파일**로 저장

```java
/**
* @param quality 0-100
* @param checkExistPath true: change the file to a different name if it exists
*/
public String saveDataToJpeg(Bitmap bitmap, String path, int quality, boolean checkExistPath) {
    if (checkExistPath){
        String folder = path.substring(0, path.lastIndexOf(File.separator));
        String fileName = path.substring(path.lastIndexOf(File.separator) + 1, path.length());
        String[] fileNameSep = fileName.split("\\.");

        String ext = "";
        if (fileNameSep.length > 1){
            ext = fileNameSep[1];
        }
        
        int fileNameCnt = 0;
        while (new File(path).exists()){
            path = folder + File.separator + fileNameSep[0] + "(" + (++fileNameCnt) + ")." + ext;
        }
    }
    try {
        FileOutputStream out = new FileOutputStream(path);
        Log.d(TAG, "saveDataToJpeg() - path: " + path);
        bitmap.compress(Bitmap.CompressFormat.JPEG, quality, out);
        out.close();
    } catch (FileNotFoundException exception) {
        Log.e("FileNotFoundException", exception.getMessage());
    } catch (IOException exception) {
        Log.e("IOException", exception.getMessage());
    }
    return path;
}
```
