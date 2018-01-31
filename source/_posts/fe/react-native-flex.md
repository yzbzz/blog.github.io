---
title: react-native-flex
date: 2016-08-29 15:18:42
tags: fe
---

### 使用Flexbox布局

我们在React Native中使用flexbox规则来指定某个组件的子元素的布局。Flexbox可以在不同屏幕尺寸上提供一致的布局结构。

一般来说，使用`flexDirection`、`alignItems`、`justifyContent`三个样式属性就已经能满足大多数布局要求。

#### Flex Direction

在组件的`style`中指定`flexDirection`可以决定布局的**主轴**。子元素是应该沿着**水平轴**(row)方向排列，还是沿着**竖直轴**(column)方向排列呢？默认值是**竖直轴**(column)方向。

#### Justify Content

在组件的style中指定`justifyContent`可以决定其子元素沿着**主轴**的**排列方式**。子元素是应该靠近主轴的起始端还是末尾段分布呢？亦或应该均匀分布？对应的这些可选项有：`flex-start`、`center`、`flex-end`、`space-around`以及`space-between`

#### Align Items

在组件的style中指定`alignItems`可以决定其子元素沿着**次轴**（与主轴垂直的轴，比如若主轴方向为row，则次轴方向为column）的排列方式。子元素是应该靠近次轴的起始端还是末尾段分布呢？亦或应该均匀分布？对应的这些可选项有：`flex-start`、`center`、`flex-end`以及`stretch`。

> 注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸
