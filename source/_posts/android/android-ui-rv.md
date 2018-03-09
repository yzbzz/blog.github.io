---
title: RecyclerView
date: 2018-03-06 11:10:28
tags: android
---

### RecyclerView快速滚动

使用`ListView`实现快速滚动只需要添加一行代码就可以了

```kotlin
listView.setFastScrollEnabled(true)
```

在`RecyclerView`中，并没有提供快速滚动的API。但是现在在Android Support Library 26，官方终于提供了。让我们开始吧。

<!--more-->

**首先你需要进行以下配置**

```groovy
dependencies {
    ....
    compile 'com.android.support:design:26.0.1'
    compile 'com.android.support:recyclerview-v7:26.0.1'
    ....
}
```

```
buildscript {
    
    repositories {
        google()
    }
    ....
}
```

**设置你的xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:context="com.shaishavgandhi.fastscrolling.MainActivity"
    tools:showIn="@layout/activity_main">


 <android.support.v7.widget.RecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:fastScrollEnabled="true"
    app:fastScrollHorizontalThumbDrawable="@drawable/thumb_drawable"
    app:fastScrollHorizontalTrackDrawable="@drawable/line_drawable"
    app:fastScrollVerticalThumbDrawable="@drawable/thumb_drawable"
    app:fastScrollVerticalTrackDrawable="@drawable/line_drawable">

 </android.support.v7.widget.RecyclerView>

</android.support.constraint.ConstraintLayout>
```

- **fastScrollEnabled：**是否启用快速滚动
- **fastScrollHorizontalThumbDrawable：**水平滚动块
- **fastScrollHorizontalTrackDrawable：** 水平滚动背景
- **fastScrollVerticalThumbDrawable：** 竖直滚动块
- **fastScrollVerticalTrackDrawable：** 竖直滚动背景

**line_drawable.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:state_pressed="true"
        android:drawable="@drawable/line"/>

    <item
        android:drawable="@drawable/line"/>
</selector>
```

**line.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="rectangle">

    <solid android:color="@android:color/darker_gray" />

    <padding
        android:top="10dp"
        android:left="10dp"
        android:right="10dp"
        android:bottom="10dp"/>
</shape>
```

**thumb_drawable.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:state_pressed="true"
        android:drawable="@drawable/thumb"/>

    <item
        android:drawable="@drawable/thumb"/>
</selector>
```

**thumb.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="rectangle">

    <corners
        android:topLeftRadius="44dp"
        android:topRightRadius="44dp"
        android:bottomLeftRadius="44dp" />

    <padding
        android:paddingLeft="22dp"
        android:paddingRight="22dp" />

    <solid android:color="@color/colorPrimaryDark" />

</shape>
```

**效果如下：**

![android-ui-rv](/img/android/android-ui-rv.gif)

> 原文链接： [Fast Scrolling with RecyclerView](https://android.jlelse.eu/fast-scrolling-with-recyclerview-2b89d4574688)

