---
title: react-native-text
date: 2016-08-22 16:47:44
tags: fe
---

### Text

一个用于显示文本的React组件

`Text`支持嵌套、样式和触摸事件

### Props

---
accessible bool

When set to true, indicates that the view is an accessibility element. The default value for a Text element is true.


See the Accessibility guide for more information.
---

ellipsizeMode enum('head', 'middle', 'tail', 'clip')

This can be one of the following values:

head - The line is displayed so that the end fits in the container and the missing text at the beginning of the line is indicated by an ellipsis glyph. e.g., "...wxyz"
middle - The line is displayed so that the beginning and end fit in the container and the missing text in the middle is indicated by an ellipsis glyph. "ab...yz"
tail - The line is displayed so that the beginning fits in the container and the missing text at the end of the line is indicated by an ellipsis glyph. e.g., "abcd..."
clip - Lines are not drawn past the edge of the text container.
The default is tail.

numberOfLines must be set in conjunction with this prop.

clip is working only for iOS
numberOfLines number

Used to truncate the text with an ellipsis after computing the text layout, including line wrapping, such that the total number of lines does not exceed this number.

This prop is commonly used with ellipsizeMode.

onLayout function

Invoked on mount and layout changes with

{nativeEvent: {layout: {x, y, width, height}}}

onLongPress function

This function is called on long press.

e.g., `onLongPress={this.increaseSize}>``

onPress function

This function is called on press.

e.g., `onPress={() => console.log('1st')}``
