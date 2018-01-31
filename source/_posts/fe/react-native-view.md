---
title: react-native-view
date: 2016-08-22 16:22:47
tags: fe
---

### View

最基本的构建UI视图的容器，`View` 支持flexbox、style、一些触摸事件和访问性控制，`View`直接映射到`native`视图，不管在什么平台，不管它是UIView，<div>，android.view，等等...

`View`设计成嵌套其它视图，可以有0到任何类型的children

> View的设计初衷是和StyleSheet搭配使用，这样可以使代码更清晰并且获得更高的性能。尽管内联样式也同样可以使用

### 合成触摸事件

### Props

#### style

`backfaceVisibility` ReactPropTypes.oneOf(['visible', 'hidden'])
`backgroundColor` color
`borderBottomColor` color
`borderBottomLeftRadius` ReactPropTypes.number
`borderBottomRightRadius` ReactPropTypes.number
`borderBottomWidth` ReactPropTypes.number
`borderColor` color
`borderLeftColor` color
`borderLeftWidth` ReactPropTypes.number
`borderRadius` ReactPropTypes.number
`borderRightColor` color
`borderRightWidth` ReactPropTypes.number
`borderStyle` ReactPropTypes.oneOf(['solid', 'dotted', 'dashed'])
`borderTopColor` color
`borderTopLeftRadius` ReactPropTypes.number
`borderTopRightRadius` ReactPropTypes.number
`borderTopWidth` ReactPropTypes.number
`borderWidth` ReactPropTypes.number
`opacity` ReactPropTypes.number
`overflow` ReactPropTypes.oneOf(['visible', 'hidden'])
