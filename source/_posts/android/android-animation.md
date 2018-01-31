---
title: android-animation
date: 2016-05-19 10:54:29
tags: android
---

### 动画描述
| XML配置     | Java 类             | 描述           | 动画类型       |
| :-------- | :----------------- | ------------ | ---------- |
| alph      | AlphaAnimation     | 渐变透明度动画效果    | 渐变动画       |
| scale     | ScaleAnimation     | 渐变尺寸伸缩动画效果   | 伸缩动画       |
| translate | TranslateAnimation | 画面转换位置移动动画效果 | 画面转换动画(位移) |
| rotate    | RotateAnimation    | 画面转移旋转动画效果   | 画面旋转动画     |

<!-- More -->

### 插入器

| 属性                                 | 描述    |
| ---------------------------------- | ----- |
| accelerate_decelerate_interpolator | 加速-减速 |
| accelerate_interpolator            | 加速    |
| decelerate_interpolator            | 减速    |

### XML配置

```java
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <alpha //渐变
        android:duration="1000" //动画时长，单位毫秒
        android:fromAlpha="0.0" //动画起始时的透明度 0.0表示完全透明
        android:toAlpha="1.0" /> //动画结束时的透明度 1.0表示完全不透明
          
     <scale //伸缩
        android:duration="1000" //动画时长，单位毫秒
        android:fillAfter="false" //当设置为true,该动画转化在动画结束后被应用
        android:fromXScale="0.0" //动画起始时 X坐标上的伸缩尺寸 0表示收缩到没有
        android:fromYScale="0.0" //动画起始时 Y坐标上的伸缩尺寸 1表示正常无伸缩
        android:interpolator="@android:anim/accelerate_decelerate_interpolator" //插入器
        android:pivotX="50%" //动画相对于物件的X坐标的开始位置,从0%~100%取值，50%为中点位置
        android:pivotY="50%" //动画相对于物件的Y坐标的开始位置
        android:toXScale="1.4" //动画起始时 X坐标上的伸缩尺寸 小于1表示收缩
        android:toYScale="1.4" /> //动画起始时 Y坐标上的伸缩尺寸 大于1表示放大
          
     <translate //位置转移
        android:duration="2000" //动画时长，单位毫秒
        android:fromXDelta="30" //动画开始时 X坐标的位置
        android:fromYDelta="30" //动画开始时 Y坐标的位置
        android:toXDelta="-80" //动画结束时 X坐标的位置
        android:toYDelta="300" /> //动画结束时 Y坐标的位置
          
      <rotate
        android:duration="3000" //动画时长，单位毫秒
        android:fromDegrees="0" //起始角度 
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"//插入器
        android:pivotX="50%" //动画相对于物件的X坐标的开始位置,从0%~100%取值，50%为中点位置
        android:pivotY="50%" //动画相对于物件的Y坐标的开始位置
        android:toDegrees="+350" /> //结束角度，可以大于360度
</set>
```