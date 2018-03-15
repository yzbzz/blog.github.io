---
title: Kotlin注解
date: 2018-03-12 11:44:40
tags: [android, kotlin]
---

### 前言

关于注解可以参考以面文章：

[ 秒懂，Java 注解 （Annotation）你可以这样学](http://blog.csdn.net/briblue/article/details/73824058)

<!--more-->

### 注解声明

注解是将元数据附加到代码的方法。

Java

```kotlin
public @interface Fancy {}
```

kotlin

```kotlin
annotation class Fancy
```

注解类型

- @Target：指定注解的作用域(类、函数、属性、表达式等)
  - AnnotationTarget.CLASS：用作类
  - AnnotationTarget.FIELD：用作字段
  - AnnotationTarget.FUNCTION：用作方法
  - AnnotationTarget.PROPERTY：用作属性
- @Retention：注解的存活时间(SOURCE、BINARY、RUNTIME)
  - AnnotationRetention.BINARY：注解只被保留到编译进行的时候，它并不会被加载到 JVM 中
  - AnnotationRetention.RUNTIME：注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们
  - AnnotationRetention.SOURCE：注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视
- @Repeatable：允许在单个元素上多次使用该注解
- @MustBeDocumented：指定该注解是公有 API 的一部分，并且应该包含在生成的 API 文档中显示的类或方法的签名中。

```kotlin
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION,
        AnnotationTarget.VALUE_PARAMETER, AnnotationTarget.EXPRESSION)
@Retention(AnnotationRetention.SOURCE)
@MustBeDocumented
annotation class Fancy
```

### 用法

```kotlin
@Fancy class Foo {
    @Fancy fun baz(@Fancy foo: Int): Int {
        return (@Fancy 1)
    }
}
```

对类的主构造函数进行标解，这时的类不能省略`constructor`关键字

```kotlin
class Foo @Inject constructor(dependency: MyDependency) {
    // ……
}
```

对属性访问器进行注解

```kotlin
class Foo {
    var x: MyDependency? = null
        @Inject set
}
```

### 构造函数

注解的构造函数可以带参数

```kotlin
annotation class Special(val why: String)
@Special("example") class Foo {}
```

允许的参数类型有：

- 对应Java的原生类型(Int、Long等等)
- 字符串
- 类(`Foo::class`)
- 枚举
- 其他注解
- 上面已列类型的数组

注解参数不可以为空类型，因为`JVM`不支持将`null`作为注解属性的值

如果注解用作另一个注解的参数，则其不能以`@`字符作为前缀

```kotlin
annotation class ReplaceWith(val expression: String)
annotation class Deprecated(
        val message: String,
        val replaceWith: ReplaceWith = ReplaceWith(""))
@Deprecated("This function is deprecated, use === instead", ReplaceWith("this === other"))
```

如果需要将一个类作为注解的参数，请使用`Kotlin`的类。Kotlin 编译器会自动将其转换为 Java 类，以便 Java 代码能够正常看到该注解和参数 。

```kotlin
import kotlin.reflect.KClass
annotation class Ann(val arg1: KClass<*>, val arg2: KClass<out Any>)
@Ann(String::class, Int::class) class MyClass
```

### Lambada

注解也可以用于`lambda`。它们会应用于生成 lambda 表达式体的 `invoke()` 方法上

```kotlin
annotation class Suspendable
val f = @Suspendable { Fiber.sleep(10) }
```

