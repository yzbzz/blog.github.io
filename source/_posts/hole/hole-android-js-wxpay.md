---
title: 记一次Android端H5调用微信支付引发的坑
date: 2018-01-30 15:28:26
tags: hole
---

### 事件背景

今天一位H5的同事过来找我们，说他们在H5里调用微信支付(不是通过jsapi调用)，到支付界面，点击返回键，会导致APP重新启动，把`webview`给关掉了

### 事件原因分析

经过我一顿猛操作，最后发现原来是`webview`设置`userAgent`引发的问题。

因为H5的要求，我们会在`userAgent`里设置一些信息，这里以`abc`为例，我们把`abc`这个字符串设置到了`userAgent`里，同时在`AndroidManifest`的`activity`节点注册了`abc`的`scheme`

```java
// 设置userAgent
webSettings.setUserAgentString(ua + " abc/" + getVersionName());
```

```java
// 设置scheme
<intent-filter>
  <data android:scheme="abc" />
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
</intent-filter>
```

这里设置之后，当点击微信支付的返回按钮后，发现微信会发起一个类似下面结构的`Intent`:

```
url: abc://
action: android.intent.action.VIEW
```

这里导致我们注册的`scheme`的`Activiy`会接收到，导致应用重启。

### 解决办法

通过我们的观察，发现`scheme`是区别大小写的，我们最后把`userAgent`的`abc`改成大写`ABC`就OK了