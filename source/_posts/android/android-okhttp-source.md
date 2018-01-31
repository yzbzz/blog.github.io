---
title: android-okhttp-流程简析
date: 2017-03-10 14:51:38
tags: android
---

### 前言

> **OkHttp进行网络请求不是基于HttpURLConnection，而是基于socket。本篇只是对源码流程的简析。**

### 源码分析Request

> 不熟悉如何使用OKHttp3的同学，可以先阅读android-okhttp章节

在使用OKHttp3进行网络请求时，我们会使用下面2种方式进行调用

```java
private final OkHttpClient client = new OkHttpClient();

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
```

从上面的代码可以看出，会先调用`newCall(request)`方法获取一个`Call`对象，再调用`Call`对象的`excute`方法或`enqueue`方法执行同步或异步请求。

<!--more-->

接下来，我们来看看源码:
![okhttpclient01](/img/okhttp/okhttpclient01.jpg)

我们来看看`newCall`方法，该方法返回了一个`RealCall`对象

![realcall01](/img/okhttp/realcall01.jpg)

我们来看看execute和enqueue方法，我们可以看到这2个方法都用到了client.dispatcher()，这个方法得到的是一个Dispatcher对象，如下图：

![dispatcher01](/img/okhttp/dispatcher01.jpg)

Dispatcher类设置了一个最大的请求数，定义了一些双队列用于存储request请求，同时定义了一个ExecutorService线程池用于执行异步请求。我们来看看Dispatcher的enqueue方法

![dispatcher02](/img/okhttp/dispatcher02.jpg)

在Dispatcher的enqueue方法中，会使用创建的线程池执行AsyncCall，我们再来看看AsyncCall的实现

![realcall02](/img/okhttp/realcall02.jpg)

大家看图中标红的地方，是不是和我们RealCall同步execute方法很像，都是调用了`getResponseWithInterceptorChain`方法返回`response`，而且我们也可以知道**onFailure**和**onResponse**回调的逻辑了

我们再来持看`getResponseWithInterceptorChain`方法

![realcall03](/img/okhttp/realcall03.jpg)

在`getResponseWithInterceptorChain`方法内部，会定义一个List，装载你自己定义的拦截器和一些Okhttp3定义的拦截器，最后会调用`RealInterceptorChain`类的proceed方法去递归调用List中的拦截器的intercept方法，

![realinterceptorchain01](/img/okhttp/realinterceptorchain01.jpg)

我们来看看`ConnectInterceptor`拦截器，通过名字我们大概能知道是做网络连接用的。

![realinterceptorchain01](/img/okhttp/connectinterceptor01.jpg)

在`ConnectInterceptor`的intercept方法中会用调用StreamAllocation类的newStream获取HttpStream对象，调用connection方法得到RealConnection对象，在connection方法里面仅仅是返回了一个对象，那么RealConnection对象是怎么生成的呢，我们大概可以猜到是在newStream里生成的。我们来看看newStream方法

![streamallocation01](/img/okhttp/streamallocation01.jpg)

在方法内部，大家可以看到是调用了`findHealthyConnection`方法获取到了`RealConnection`对象，同时会把RealConnection的数据(**source/sink**)存放在**Http1xStream **OR **Http2xStream**中(大家先记住这一点，后面获取Reaponse时会用到**Http1xStream**或**Http2xStream**的数据)。在方法`findHealthyConnection`内部，会调用`findConnection`方法

![streamallocation02](/img/okhttp/streamallocation02.jpg)

在`findConnection`方法内部，最终会调到`193`行的`connect`方法，接下来，我们看看RealConnection的connect方法。

![realconnection01](/img/okhttp/realconnection01.jpg)

在connect方法的内部，最终会调用buildTunneledConnection或buildConnection方法，这2个方法最终都会调用RealConnection的connectSocket方法和establishProtocol方法。在connectSocket方法内部，会进行socket进行数据传输，在establishProtocol方法内部，会使用Okio进行数据流的读取，并给RealConnection的source和sink进行赋值，这个值会存储在后续Response的body中。

![realconnection02](/img/okhttp/realconnection02.jpg)

从上图可以看出，在connectSocket内部，会调用Platform的get方法获取相应平台，并调用Platform的connectSocket方法进行socket连接。

![platform01](/img/okhttp/platform01.jpg)
**至此，我们知道了Okhttp3是通过socket进行数据传输的，同时request请求大致也清楚了**



### 源码分析Response

在上面的**Request**分析篇中，大家还记得，在RealCall的`getResponseWithInterceptorChain`方法吗

![realcall03](/img/okhttp/realcall03.jpg)

我们在这个方法分析了`ConnectInterceptor`拦截器中的网络请求流程，在这个方法的最后，会调用`CallServerInterceptor`拦截器去进行Response的结果获取，接下来，我们来看看`CallServerInterceptor`

![callserverinterceptor01](/img/okhttp/callserverinterceptor01.jpg)

大家看第**62**行，**Response**的**body**就是在这赋值的，这里的**httpStream**就是我们**Request**篇叫大家记住的**Http1xStream**或**Http2xStream**，这2种**httpStream**流程差不多，我们这里以**Http1xStream**为例来分析

![http1xstream](/img/okhttp/http1xstream.jpg)

这里比较简单，调用**openResponseBody**返回一个**RealResponseBody**对象返回，在方法内部先调用**getTransferStream**方法获取一个**source**对象，然后生成一个**RealResponseBody**对象进行返回。熟悉Okhttp3用法的同学应该知道，在**onResponse**回调里，我们会使用**response.body.string()**获取字符串或**response.body.byteStream()**获取流对象。通过上面对**CallServerInterceptor**拦截器的分析(第**62**行)，我们知道**response.body**就是调用**httpStream**的**openResponseBody**方法返回的**RealResponseBody**，我们再来看看**RealResponseBody**类

![responsebody01](/img/okhttp/responsebody01.jpg)

通过上面的源码，我们可以看出，其它就是调用了**source**的一些方法进行数据处理，然后返回，这里的source就是我们**Request**分析篇中**RealConnection**的**source**

**至此，response整个流程也大致清楚了**