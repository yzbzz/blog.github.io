---
title: android-gradle
date: 2018-02-07 10:20:42
tags: hole
---

**解决Kotlin和DataBinding冲突 Unresolved reference: databinding**

在主工程的`build.gradle`文件添加下面一行

```
dependencies {
    kapt "com.android.databinding:compiler:3.0.0"
}
```

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

**error:execution failed for task ':app:transformnativelibswithstripdebugsymbolfordebug'.**

删除`/sdk/ndk-bundle`

### Vivo 手机使用 Android studio安装APK失败的解决办法

在工程目录的`gradle.properties`中添加`android.injected.testOnly = false`即可

### 解决Android Studio在XML中使用自定义View输入自定义属性的时候没有任何提示的问题

```
我们在自定义View和自定义属性的时候，要保持自定义View的className和自定义属性的的declare-styleable名一样，根据这个约定，IDE才会自动提示
```

