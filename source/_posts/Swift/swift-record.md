---
title: swift-record
date: 2016-06-22 15:03:30
tags: ios
---

### extension

声明一个扩展使用关键字`extension`

```swift
extension SomeType{
  //加到SomeType的新功能写到这里
}
```

### indirect

使用indirect来标识一个枚举是可以递归调用的

### required

在类的构造器前添加`required`修饰符表明所有该类的子类都必须实现该构造器，在子类重写父类的必要构造器时，必须在子类的构造器前也添加`required`修饰符，这是为了保证继承链上子类的构造器也是必要构造器。在重写父类的必要构造器时，不需要添加`override`修饰符

### mutating

一般情况下，值类型的属性不能在它的实例方法中被修改，`mutating`使得方法可以从方法内部改变它的属性；并且它做的任何改变在方法结束时还会保留在原始结构中。方法还可以给它隐含的`self`属性赋值一个全新的实例，这个新实例在方法结束后将替换原来的实例。要使用`mutating`方法，将关键字`mutating`放到方法的`func`关键字之前就可以了

### unowned

使用`unowned`标识无主引用

### weak

使用`weak`标识弱引用

### 类型属性

在结构体和枚举中可以使用`static`来修饰静态变量，在类中可以使用`static`和`class`来修饰，使用`class`修饰来允许子类重写父类的实现方法