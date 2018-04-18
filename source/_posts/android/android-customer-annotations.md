---
title: Android 自定义注解
date: 2018-04-11 17:56:24
tags: android
---

新建一个module，选择`Java Library`

[auto-service](https://link.jianshu.com/?t=https://github.com/google/auto/tree/master/service)：Google 公司出品，用于自动为 JAVA Processor 生成 META-INF 信息。

**AutoService**注解处理器是Google开发的，用来生成 **META-INF/services/javax.annotation.processing.Processor **文件的，你只需要在你定义的注解处理器上添加 @AutoService(Processor.class) 就可以了，简直不能再方便了。