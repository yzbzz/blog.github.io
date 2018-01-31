---
title: css-border
date: 2017-07-11 17:28:04
tags: fe
---

#### 本章主要包含以下内容

```
边框>内阴影>背景图片>背景颜色>外阴影
```

- border-radius
- box-shadow
- border-image

<!-- More -->

**浏览器支持**

|               | IE 9 + | Firefox 3.5 | Chrome | Safari | Opera |
| ------------- | ------ | ----------- | ------ | ------ | ----- |
| border-radius | YES    | YES         | YES    | YES    | YES   |
| box-shadow    | YES    | YES         | YES    | YES    | YES   |
| border-image  | NO     | YES         | YES    | YES    | YES   |

> Internet Explorer 9+ 支持 border-radius 和 box-shadow 属性。
>
> Firefox、Chrome 以及 Safari 支持所有新的边框属性。
>
> 注释：对于 border-image，Safari 5 以及更老的版本需要前缀 -webkit-。
>
> Opera 支持 border-radius 和 box-shadow 属性，但是对于 border-image 需要前缀 -o-。

为了兼容各主流浏览器并支持这些主流浏览器的较低版本，在基于Webkit的Chrome和Safari等浏览器上使用box-shadow属性时，我们需要将属性的名称写成-webkit-box-shadow的形式。Firefox浏览器则需要写成-moz-box-shadow的形式。

```javascript
.box-radius {  
    //Firefox4.0-  
    -moz-box-radius: 25px;  

    //Safariand Google chrome10.0-  
    -webkit-box-radius:25px;  

    //Firefox4.0+、 Google chrome 10.0+ 、 Oprea10.5+ and IE9  
    box-radius:  25px;  // 角度
  	border: 2px solid; // 边框
}
.box-shadow {  
         //Firefox4.0-  
         -moz-box-shadow:投影方式 X轴偏移量 Y轴偏移量阴影模糊半径 阴影扩展半径 阴影颜色;  
  
         //Safariand Google chrome10.0-  
         -webkit-box-shadow:投影方式 X轴偏移量 Y轴偏移量阴影模糊半径 阴影扩展半径 阴影颜色;  
  
         //Firefox4.0+、 Google chrome 10.0+ 、 Oprea10.5+ and IE9  
         box-shadow:  投影方式 X轴偏移量 Y轴偏移量 阴影模糊半径 阴影扩展半径 阴影颜色;  
}  
```

#### border-radius （用于为元素添加圆角边框!）

**定义和用法**

border-radius 属性是一个简写属性，用于设置四个 `border-*-radius  ` 属性。

| 默认值           | 0                                 |
| ------------- | --------------------------------- |
| 继承性           | no                                |
| 版本            | CSS3                              |
| JavaScript语法： | *object*.style.borderRadius="5px" |

**语法**

```javascript
border-radius: 1-4 length|% / 1-4 length|%;
```

**注释**：按此顺序设置每个 radii 的四个值。如果省略 bottom-left，则与 top-right 相同。如果省略 bottom-right，则与 top-left 相同。如果省略 top-right，则与 top-left 相同。

| 值      | 描述          |
| ------ | ----------- |
| length | 定义圆角的形状     |
| %      | 以百分比定义圆角的形状 |

**例1：**

```javascript
border-radius: 2em;
```

等价于

```javascript
border-top-left-radius: 2em;
border-top-right-radius: 2em;
border-bottom-right-radius: 2em;
border-bottom-left-radius: 2em;
```

**例2**

```javascript
border-radius: 2em 1em 4em / 0.5em 3em;
```

等价于：

```javascript
border-top-left-radius: 2em 0.5em;
border-top-right-radius: 1em 3em;
border-bottom-right-radius: 4em 0.5em;
border-bottom-left-radius: 1em 3em;
```

#### box-shadow

> box-shadow: 投影方式 x轴偏移量 y轴偏移量 阴影模糊半径 阴影扩展半径 阴影颜色
>
> 阴影类型：此参数可选。如不设值，默认投影方式是外阴影；如取其唯一值“inset”，其投影为内阴影；
>
> X-offset:阴影水平偏移量，其值可以是正负值。如果值为正值，则阴影在对象的右边，其值为负值时，阴影在对象的左边
>
> Y-offset:阴影垂直偏移量，其值也可以是正负值。如果为正值，阴影在对象的底部，其值为负值时，阴影在对象的顶部；
>
> 阴影模糊半径：此参数可选，，但其值只能是为正值，如果其值为0时，表示阴影不具有模糊效果，其值越大阴影的边缘就越模糊；
>
> 阴影扩展半径：此参数可选，其值可以是正负值，如果值为正，则整个阴影都延展扩大，反之值为负值时，则缩小；
>
> 阴影颜色：此参数可选。如不设定颜色，浏览器会取默认色，但各浏览器默认取色不一致，特别是在webkit内核下的safari和chrome浏览器下表现为透明色，在Firefox/Opera下表现为黑色（已验证），建议不要省略此参数。

#### border-image （属性允许您规定用于边框的图片！）

```javascript
.box-image {  
    //Firefox4.0-  
    -moz-box-image:  url(border.png) 30 30 round;  

    //Safariand Google chrome10.0-  
    -webkit-box-image:  url(border.png) 30 30 round; 
  
  	// Opera
    -o-border-image: url(border.png) 30 30 round

    box-image: url(border.png) 30 30 round;  // 角度
}
```