---
layout: post
title:  "암호화, 복호화"
subtitle: "Encrypt, decrypt"
date:   2020-01-08 11:22:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# **복호화**(Decrypt)

```java
public static String decrypt(byte[] value, String key, byte[] iv) {
    try {
        SecretKeySpec sks = new SecretKeySpec(key.getBytes("UTF-8"), "AES");
        IvParameterSpec ivSpec = new IvParameterSpec(iv);
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(Cipher.DECRYPT_MODE, sks, ivSpec);
        byte[] bres = cipher.doFinal(value);
        return new String(bres, "UTF-8");
    } catch (Exception ex) {
        ex.printStackTrace();
        return null;
    }
}
```

```java
public static String decrypt(String value, String key, String iv){
    try{
        byte[] ukey = key.getBytes("UTF-8");
        byte[] bkey = new byte[ukey.length];
        byte[] ivBytes = iv.getBytes("UTF-8");
        byte[] copiedIvBytes = new byte[ivBytes.length];
        System.arraycopy(ukey, 0, bkey, 0, ukey.length);
        System.arraycopy(ivBytes, 0, copiedIvBytes, 0, ivBytes.length);

        SecretKeySpec sks = new SecretKeySpec(bkey, "AES");
        IvParameterSpec ivParameterSpec = new IvParameterSpec(bkey);

        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(Cipher.DECRYPT_MODE, sks, ivParameterSpec);

        byte[] u64 = Base64.decode(value, Base64.DEFAULT);
        byte[] bres = cipher.doFinal(u64);

        return new String(bres, "UTF-8");
    }
    catch (Exception ex){
        return null;
    }
}
```

<br>

# **암호화**(Encrypt)

- EncryptResult class

```java
public static class EncryptResult {
    private final byte[] result;
    private final byte[] iv;

    public EncryptResult(byte[] result, byte[] iv) {
        this.result = result;
        this.iv = iv;
    }

    public byte[] getResult() {
        return result;
    }

    public byte[] getIv() {
        return Arrays.copyOf(iv, iv.length);
    }
}
```

<br>

- Encrypt code 1

```java
public static byte[] getRandomAesCryptIv() {
    byte[] randomBytes = new byte[16];
    new SecureRandom().nextBytes(randomBytes);

    return new IvParameterSpec(randomBytes).getIV();
}
    
public static EncryptResult encrypt(String value, String key) {
    try {
        SecretKeySpec sks = new SecretKeySpec(key.getBytes(), "AES");
        IvParameterSpec ivSpec = new IvParameterSpec(getRandomAesCryptIv());
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(Cipher.ENCRYPT_MODE, sks, ivSpec);
        byte[] res = cipher.doFinal(value.getBytes("UTF-8"));
        return new EncryptResult(res, cipher.getIV());
    }
    catch (Exception ex){
        Log.d(TAG, "encrypt error : " + ex.getMessage());
        Log.d(TAG, "encrypt error : " + ex.getCause());
        return null;
    }
}
```

<br>

- Encrypt code 2

```java
public static String encrypt(String value, String key, String iv){
    try{
        byte[] ukey = key.getBytes("UTF-8");
        byte[] bkey = new byte[ukey.length];
        byte[] ivBytes = iv.getBytes("UTF-8");
        byte[] copiedIvBytes = new byte[ivBytes.length];
        System.arraycopy(ukey, 0, bkey, 0, ukey.length);
        System.arraycopy(ivBytes, 0, copiedIvBytes, 0, ivBytes.length);

        SecretKeySpec sks = new SecretKeySpec(bkey, "AES");
        IvParameterSpec ivParameterSpec = new IvParameterSpec(copiedIvBytes);

        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(Cipher.ENCRYPT_MODE, sks, ivParameterSpec);

        byte[] res = cipher.doFinal(value.getBytes("UTF-8"));

        String base64 = Base64.encodeToString(res, Base64.DEFAULT);
        return base64;
    }
    catch (Exception ex){
        return null;
    }
}
```