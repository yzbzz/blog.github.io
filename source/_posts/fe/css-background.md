---
title: css-background
date: 2017-07-12 11:34:23
tags: fe
---

#### 本章包含以下内容

- background-size
- background-origin

<!-- More -->

**浏览器支持**

|                   | IE 9 + | Firefox 3.5 | Chrome | Safari | Opera |
| ----------------- | ------ | ----------- | ------ | ------ | ----- |
| background-size   | YES    | YES         | YES    | YES    | YES   |
| background-origin | YES    | YES         | YES    | YES    | YES   |

Internet Explorer 9+、Firefox、Chrome、Safari 以及 Opera 支持新的背景属性。

#### CSS3 background-size 属性

backgrond-size 属性规定背景图片的尺寸。您能够以像素或百分比规定尺寸。如果以百分比规定尺寸，那么尺寸相对于父元素的宽度和高度。

**例1：**

调整背景图片的大小：

```javascript
div {
  background:url(bg_flower.gif);
  -moz-background-size:63px 100px; /* 老版本的 Firefox */
  background-size:63px 100px;
  background-repeat:no-repeat;
}
```

**例2：**

对背景图片进行拉伸，使其完成填充内容区域：

```javascript
div {
  background:url(bg_flower.gif);
  -moz-background-size:40% 100%; /* 老版本的 Firefox */
  background-size:40% 100%;
  background-repeat:no-repeat;
}
```

#### CSS3 background-origin 属性

background-origin 属性规定背景图片的定位区域。

背景图片可以放置于 content-box、padding-box 或 border-box 区域。

![background-origin](../../img/css/background-origin.png)

**实例**

在 content-box中定位背景图片

```javascript
div {
  background:url(bg_flower.gif);
  background-repeat:no-repeat;
  background-size:100% 100%;
  -webkit-background-origin:content-box; /* Safari */
  background-origin:content-box;
}
```

#### CSS3 多重背景图片

CSS3 允许您为元素使用多个背景图像。

**实例**

为 body 元素设置两幅背景图片：

```javascript
body { 
	background-image:url(bg_flower.gif),url(bg_flower_2.gif);
}
```

#### 新的背景属性

| 属性                | 描述           | CSS  |
| ----------------- | ------------ | ---- |
| background-clip   | 规定背景的绘制区域。   | 3    |
| background-origin | 规定背景图片的定位区域。 | 3    |
| background-size   | 规定背景图片的尺寸。   | 3    |

**background-clip**

规定背景的绘制区域：

```javascript
div {
  background-color:yellow;
  background-clip:content-box;
}
```

语法

```javascript
// 默认border-box
background-clip: border-box|padding-box|content-box;
```

| 值           | 描述          |
| ----------- | ----------- |
| border-box  | 背景被裁剪到边框盒   |
| padding-box | 背景被裁剪到内边距框。 |
| content-box | 背景被裁剪到内容框。  |