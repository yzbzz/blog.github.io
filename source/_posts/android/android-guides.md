---
title: android-guides
date: 2017-02-13 16:33:02
tags: android
---

## 基本用法
### Button
对于Button上的文本，系统会对Button中的所有英文字母自动进行大写转换，如果这不是你想要的效果，可以使用如下配置来禁用这一默认特性
```java
// android:textAllCaps="false"
<Button
  	android:id="@+id/button"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Button"
    android:textAllCaps="false" />
```

<!--more-->

### ProgressBar

```java
// style 默认圆形
style="?android:attr/progressBarStyleLarge"
style="?android:attr/progressBarStyleSmall"
style="?android:attr/progressBarStyleSmallTitle"
style="?android:attr/progressBarStyleHorizonal" //水平
```

### ProgressDialog

### Broadcast 

> 不允许开启线程，不能做耗时操作(10秒左右)
>
> 注册：registerReceiver(本地广播需要使用LocalBroadcastManager)
>
> 取消注册：unregisterReceiver

##### 广播类型：

- 无序广播(默认)：所有广播接收器都可以接受到。调用`sendBroadcast()`发送广播

- 有序广播：优先级比较高的广播接收器(`android:priority`)可以先收到广播，使用`abortBroadcast`可以截断广播。调用`sendOrderedBroadcast`发送广播

  ```java
  <intent-filter android:priority="100">
  	......
  </intent-filter>
  ```

- 本地广播：发出的广播只能本程序才能收到。调用`LocalBroadcastManager`的`sendBroadcast`发送广播

##### ACTION

```java
android.net.conn.CONNECTIVITY_CHANGE // 网络状态发生变化  权限：ACCESS_NETWORK_STATE
android.intent.action.BOOT_COMPLETED // 开机广播 权限：RECEIVE_BOOT_COMPLETED
```

### Shader



## 权限

> 可以使用[RxPermissions](https://github.com/tbruyelle/RxPermissions)简化权限操作

下表列出了Android中所有的危险权限，一共是9组24个权限。

|    权限组名    |                   权限名                    |
| :--------: | :--------------------------------------: |
|  CALENDAR  |     READ_CALENDAR<br>WRITE_CALENDAR      |
|   CAMERA   |                  CAMERA                  |
|  CONTACTS  | READ_CONTACTS<br>WRITE_CONTACTS<br>GET_ACCOUNTS |
|  LOCATION  | ACCESS_FINE_LOCATION<br>ACCESS_COARSE_LOCATION |
| MICROPHONE |               RECORD_AUDIO               |
|   PHONE    | READ_PHONE_STATE<br>CALL_PHONE<br>READ_CALL_LOG<br>WRITE_CALL_LOG<br>ADD_VOICEMAIL<br>USE_SIP<br>PROCESS_OUTGOING_CALLS |
|  SENSORS   |               BODY_SENSORS               |
|    SMS     | SEND_SMS<br>RECCEIVE_SMS<br>READ_SMS<br>RECEIVE_WAP_PUSH<br>RECEIVE_MMS |
|  STORAGE   | READ_EXTERNAL_STORAGE<br>WRITE_EXTERNAL_STORAGE |

| 密度             | 建议尺寸      |
| -------------- | --------- |
| mipmap-mdpi    | 48 * 48   |
| mipmap-hdpi    | 72 * 72   |
| mipmap-xhdpi   | 96 * 96   |
| mipmap-xxhdpi  | 144 * 144 |
| mipmap-xxxhdpi | 192 * 192 |

float xdpi = getResources().getDisplayMetrics().xdpi;
float ydpi = getResources().getDisplayMetrics().ydpi;

| dpi范围           | 密度      |
| --------------- | ------- |
| 0dpi ~ 120dpi   | ldpi    |
| 120dpi ~ 160dpi | mdpi    |
| 160dpi ~ 240dpi | hdpi    |
| 240dpi ~ 320dpi | xhdpi   |
| 320dpi ~ 480dpi | xxhdpi  |
| 480dpi ~ 640dpi | xxxhdpi |

**子线程更新UI**

因为在 OnCreate 的 时候，View 是还没有被 ViewRootImpl
加载到 Window，所以子线程修改 View 的代码没有经过 ViewRootImpl 的检测。贯穿全文，就告诉了大家在 onResum 之前在子线程可以修改 UI，具体原因没讲清楚。可以讲讲 contentView 是怎样被ViewRootImplement 加载到 Window。Window、Activity、View、ViewRootImpl 之间的关系？