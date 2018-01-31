---
title: android-shape
date: 2016-05-18 17:28:11
tags: android
---

​	在Android程序开发中，我们经常会用到Shape去定义各种各样的形状。shape可以在selector，layout等里面使用，有6个子标签，如下：
<!--more-->
## Shape 格式
```java
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <!-- 圆角，同时设置五个属性，则Radius属性无效 -->
    <corners
        android:radius="9dp" //设置四个解的半径
        android:topLeftRadius="2dp" //设置左上角的半径
        android:topRightRadius="2dp" //设置右上角的半径
        android:bottomLeftRadius="2dp" //设置左下角的半径
        android:bottomRightRadius="2dp"/> //设置右下角的半径

    <!-- 渐变，当设置填充颜色后，无渐变效果，angle的值必须是45的倍数(包括0)，仅在type="linear"有效，角度以逆时针方向 -->
    <gradient
        android:startColor="@android:color/white"
        android:centerColor="@android:color/black"
        android:endColor="@android:color/black"
        android:useLevel="true"
        android:angle="45"
        android:type="radial"
        android:centerX="0"
        android:centerY="0"
        android:gradientRadius="90"/>

    <!-- 间隔，设置四个方向的间隔-->
    <padding
        android:left="2dp"
        android:top="2dp"
        android:right="2dp"
        android:bottom="2dp"/><!-- 各方向的间隔 -->

    <!-- 大小，设置大小-->
    <size
        android:width="50dp"
        android:height="50dp"/><!-- 宽度和高度 -->

    <!-- 填充，设置填充的颜色 -->
    <solid
        android:color="@android:color/white"/><!-- 填充的颜色 -->

    <!-- 描边 dashWidth和dashGap属性，只要其中一个设置为0dp，则边框为实线边框-->
    <stroke
        android:width="2dp" //设置边框的宽度
        android:color="@android:color/black" //设置边框的颜色
        android:dashWidth="1dp" //设置虚线的宽度
        android:dashGap="2dp"/> //设置虚线的间隔宽度
</shape>
```
