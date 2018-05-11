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

需要注意的是：

- NO1：表示这些类的确可以开始一个Activity，但是它需要创建一个新的task。这可能会满足一些特定的需求，但是在你的应用中会创建一个不标准的回退栈（back stack），这通常是不推荐的或者不是最好的实践
- NO2：表示这些类去layout inflate是合法的，但是会使用系统默认的主题，如果你自定义了某些样式，可能不会被使用
- NO3：表示在receiver为null时允许，在4.2或以上的版本中，用于获取黏性广播的当前值。

### 总结

对于UI相关的方法，都不建议或者不可使用Application，都应该使用Activity作为Context来处理。另外需要注意Context引用的持有，防止内存泄漏。