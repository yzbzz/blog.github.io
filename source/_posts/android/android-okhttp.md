---
title: android-okhttp
date: 2017-02-06 16:14:28
tags: android
---

## 概述

> OKhttp3是一个高效的http&http/2的客户端，用于android和java，支持同步和异步请求。

OKhttp3是开源界著名的Square公司的大作，大神[JakeWharton](https://github.com/JakeWharton)的公司。Google已把Android的网络请求替换为Okhttp的实现

OKhttp3有以下优点：

1. 支持请求相同host的所有请求，共享socket

2. 连接池可以减少请求延迟(如果HTTP / 2不可使用)。

3. 透明的GZIP压缩

4. 响应缓存避免了完全重复的网络请求

5. socket自动选择最好路线，并支持自动重连

6. …...

支持版本：Android 2.3及以上，java1.7及以上

<!--more-->

## 引用

> 注:okhttp内部依赖okio，同时导入okio：

Grade

```java
compile ‘com.squareup.okhttp3:okhttp:3.3.1’
compile ‘com.squareup.okio:okio:1.8.0’
```

对于使用Eclipse的同学，可以去Square官网去下载相应的jar包

[官网](http://square.github.io/okhttp/)

[okhttp](https://search.maven.org/remote_content?g=com.squareup.okhttp3&a=okhttp&v=LATEST)

[okio](https://search.maven.org/remote_content?g=com.squareup.okio&a=okio&v=LATEST)

## 基本用法

初始化一个client: 

```java
private final OkHttpClient client = new OkHttpClient();
```

Get请求
```java
public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://publicobject.com/helloworld.txt")
      .build();

  // 同步
  Response response = client.newCall(request).execute();
  
  // 异步
  client.newCall(request).enqueue(new Callback() {
      @Override 
      public void onFailure(Call call, IOException e) {
      }

      @Override 
      public void onResponse(Call call, Response response) throws IOException {
      }
  });
}
```

Post请求
```java
public void run() throws Exception {
  Request.Builder builder = new Request.Builder()
  FormBody.Builder formEncodingBuilder = new FormBody.Builder();
  formEncodingBuilder.add(key,value);
  RequestBody requestBody = formEncodingBuilder.build()
  builder.post(requestBody)
  builder.url(“http://publicobject.com/helloworld.txt”);
  Request request = builder.build();
  
  // 同步
  Response response = client.newCall(request).execute();
              
  // 异步
  client.newCall(request).enqueue(new Callback() {
      @Override 
      public void onFailure(Call call, IOException e) {
      }

      @Override 
      public void onResponse(Call call, Response response) throws IOException {
      }
  });           
}
```

添加Headers
```java
public void run() throws Exception {
    Request request = new Request.Builder()
        .url("https://api.github.com/repos/square/okhttp/issues")
        .header("User-Agent", "OkHttp Headers.java")
        .addHeader("Accept", "application/json; q=0.5")
        .addHeader("Accept", "application/vnd.github.v3+json")
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println("Server: " + response.header("Server"));
    System.out.println("Date: " + response.header("Date"));
    System.out.println("Vary: " + response.headers("Vary"));
}
```

上传文件
```java
public static final MediaType MEDIA_TYPE_MARKDOWN
      = MediaType.parse("text/x-markdown; charset=utf-8");

public void run() throws Exception {
    File file = new File("README.md");
    Request request = new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, file))
        .build();
    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()){}
    System.out.println(response.body().string());
}
```

发送多个请求
```java
private static final String IMGUR_CLIENT_ID = "...";
private static final MediaType MEDIA_TYPE_PNG = MediaType.parse("image/png");

public void run() throws Exception {
    RequestBody requestBody = new MultipartBody.Builder()
        .setType(MultipartBody.FORM)
        .addFormDataPart("title", "Square Logo")
        .addFormDataPart("image", "logo-square.png",
         RequestBody.create(MEDIA_TYPE_PNG, new File("website/static/logo-square.png")))
        .build();

    Request request = new Request.Builder()
        .header("Authorization", "Client-ID " + IMGUR_CLIENT_ID)
        .url("https://api.imgur.com/3/image")
        .post(requestBody)
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()){}
}
```

用Gson解析Json Response
```java
private final Gson gson = new Gson();

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("https://api.github.com/gists/c2a7c39532239ff261be")
        .build();
    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    Gist gist = gson.fromJson(response.body().charStream(), Gist.class);
    for (Map.Entry<String, GistFile> entry : gist.files.entrySet()) {
      System.out.println(entry.getKey());
      System.out.println(entry.getValue().content);
    }
}

static class Gist {
  Map<String, GistFile> files;
}

static class GistFile {
  String content;
}
```

缓存
```java
private final OkHttpClient client;

public CacheResponse(File cacheDirectory) throws Exception {
    int cacheSize = 10 * 1024 * 1024; // 10 MiB
    Cache cache = new Cache(cacheDirectory, cacheSize);
    client = new OkHttpClient.Builder()
        .cache(cache)
        .build();
     Request request = new Request.Builder()
        .url("http://publicobject.com/helloworld.txt")
        .build();
    Response response1 = client.newCall(request).execute();
}
```

取消一个请求
```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://httpbin.org/delay/2")
        .build();
    final Call call = client.newCall(request);
    Response response = call.execute();    
    call.cancel();
}
```

设置超时
```java
private final OkHttpClient client = new OkHttpClient();

public ConfigureTimeouts() throws Exception {
    client = new OkHttpClient.Builder()
        .connectTimeout(10, TimeUnit.SECONDS)
        .writeTimeout(10, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .build();
}

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://httpbin.org/delay/2")
        .build();
    final Call call = client.newCall(request);
    Response response = call.execute();
}
```

自定义拦截器
```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  client.networkInterceptors().add(new Interceptor() {    
      @Override    
      public Response intercept(Chain chain) throws IOException {   
          Request originalRequest = paramChain.request();
          ……
          ……
          ……         
          return paramChain.proceed(compressedRequest);       
      }
  });
  Request request = new Request.Builder()
    	.url("http://httpbin.org/delay/2")
    	.build();
  final Call call = client.newCall(request);
  Response response = call.execute();
}
```