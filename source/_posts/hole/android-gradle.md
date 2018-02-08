---
title: android-gradle
date: 2018-02-07 10:20:42
tags: hole
---

**Failed to open zip file**

当我们编译项目时，有时会出现

```
Error:Failed to open zip file.
Gradle's dependency cache may be corrupt (this sometimes occurs after a network connection timeout.)
Re-download dependencies and sync project (requires network)
Re-download dependencies and sync project (requires network)
```

<!-- More -->

这时我们可以去http://services.gradle.org/distributions/ 下载新的`gradle`，下载完成后不要解压

这里以`gradle-4.5.1-all`为例，找到`.gradle`的`dists`目录，这里以 `macos`为例：

```
Finder -> 前往文件夹 -> 输入~/.gradle -> wrapper -> dists
```

![hole-gradle-dists](/img/hole/hole-gradle-dists.jpg)

**refreshing gradle project**

有时间我们编译项目时，会卡在`refreshing gradle project`，参照上面，自行安装`gradle-xxx-all`的包

**Error:All flavors must now belong to a named flavor dimension**

在`defaultConfig`中加：`flavorDimensions 'default'`

```groovy
android {
  defaultConfig {
        multiDexEnabled = true
        flavorDimensions 'default'
  }
}
```

**Unable to find a matching configuration**

在`buildTypes`下的`config`加入`matchingFallbacks`

```groovy
buildTypes {
    release {
       //...
    }
    debug {
       //...
    }
    innerTest {
        //...
        matchingFallbacks = ['debug', 'release']
    }
}
```

**error style attribute not found @android:attr**

去掉`@`符

```java
<style name="PopupAnimation" parent="@android:Animation">
  <item name="@android:windowEnterAnimation">@anim/push_bottom_in</item>
  <item name="@android:windowExitAnimation">@anim/push_bottom_out</item>
</style>
==>
<style name="PopupAnimation" parent="android:Animation">
  <item name="android:windowEnterAnimation">@anim/push_bottom_in</item>
  <item name="android:windowExitAnimation">@anim/push_bottom_out</item>
</style>
```

