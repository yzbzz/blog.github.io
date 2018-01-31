---
title: android-context
date: 2016-05-21 18:53:02
tags: android
---

## Context的能力

Context能做的通用操作决定于这个context最初来源于哪里。下表所列的是，在应用中常见的会收到context对象，以及对应的每种情况，它可以用于哪些地方：

|                     | Application | Activity | Service | ContentProvider | BroadcaseReceiver |
| ------------------- | ----------- | -------- | ------- | --------------- | ----------------- |
| 显示Dialog            | NO          | YEA      | NO      | NO              | NO                |
| 启动Activity          | NO1         | YES      | NO1     | NO1             | NO1               |
| Layout Inflation    | NO2         | YES      | NO2     | NO2             | NO2               |
| 启动Service           | YES         | YES      | YES     | YES             | YES               |
| 绑定Service           | YES         | YES      | YES     | YES             | NO                |
| 发送Broadcast         | YES         | YES      | YES     | YES             | YES               |
| 注册BroadcastReceiver | YES         | YES      | YES     | YES             | NO3               |
| 加载Resource          | YES         | YES      | YES     | YES             | YES               |

> 注：
>
> 1. NO1表示Application context的确可以开始一个Activity，但是它需要创建一个新的task。这可能会满足一些特定的需求，但是在你的应用中会创建一个不标准的回退栈（back stack），这通常是不推荐的或者不是最好的实践。