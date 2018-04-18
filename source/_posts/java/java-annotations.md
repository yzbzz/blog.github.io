---
title: Java 注解介绍
date: 2018-04-12 18:09:35
tags: [java, Annotation]
---

[TOC]



本文及相关注解文章部分摘自以下的博客，推荐大家阅读

[ 秒懂，Java 注解 （Annotation）你可以这样学](http://blog.csdn.net/briblue/article/details/73824058)

[Java注解基础概念总结](https://blog.csdn.net/github_35180164/article/details/52107204)

### 定义

注解(Annotaion)，也叫元数据。一种代码级别的说明。它是`JDK1.5`及以后版本引入的一特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明、注解。

作用分类：

- 编写文档：通过代码里标识的元数据生成文档【生成文档doc文档】。
- 代码分析：通过代码里标识的元数据对代码进行分析【使用反射】。
- 编译检查：通过代码里标识的元数据让编译器能实现基本的编译检查【Override】。

### 语法

示例：

```java
// 定义一个注解
@Retention(RetentionPolicy.CLASS)
@Target(ElementType.TYPE)
public @interface AnnotationsTest { 
    String value ();
}

// 使用
@AnnotationsTest(value = "abc")
class Foo {}
```

1. 以`@interface`关键字定义
2. 注解包含成员，成员以无参数的方法的形式被声明。其方法名和返回值定义了该成员的名字和类型，例如上述例子中的`String value()`
3. 成员赋值是通过@Annotation(name=value)的形式，例如上述例子中的`@Target(ElementType.TYPE)`
4. 注解需要标明注解的生命周期，注解的修饰目标等信息，这些信息是通过元注解实现

下面我们来分析一下`java.lang.annotation`中定义的`Target`注解：

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```

- 元注解@Retention，成员value的值为`RetentionPolicy.RUNTIME`
- 元注解@Target，成员value是个数组，用{}形式赋值，值为`ElementType.ANNOTATION_TYPE`
- 成员名称为value，类型为`ElementType[]`

另外，需要注意一下，如果成员名称是value，在赋值过程中可以简写。如果成员类型为数组，但是只赋值一个元素，则也可以简写。如上面的简写形式为：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
```

### 注解的类型

注解分为两类，一类是元注解(meta-annotation)，另外一类是普通注解

#### 元注解

> 所谓元注解就是修饰注解的注解。
> 拿到一个注解，如何知道它是否是元注解呢？需要看它的元注解（无论是元注解还是普通注解都是有元注解），如果看到这样的元注解：`@Target(ElementType.ANNOTATION_TYPE)`，那么此时这个注解一定是元注解。

**@Retention**

表示在什么级别保存该注解信息，可能的值在枚举类`ElementType`中

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    RetentionPolicy value();
}

// RetentionPolicy
public enum RetentionPolicy {
    SOURCE, // 注解只保留在源文件，当Java文件编译成class文件的时候，注解被遗弃
    CLASS, // 注解被保留到class文件，jvm加载class文件时候被遗弃。这是默认的生命周期
    /*
    * 注解不仅被保存到class文件中，jvm加载class文件之后，仍然存在，保存到class对象中，
    * 可以通过反射来获取
    */
    RUNTIME 
}
```

这3个生命周期对应表如下

| 名称                    | 生命周期              |
| ----------------------- | --------------------- |
| RetentionPolicy.SOURCE  | Java源文件(.java文件) |
| RetentionPolicy.CLASS   | .class文件            |
| RetentionPolicy.RUNTIME | 内存中的字节          |

> 首先要明确生命周期长度 **SOURCE < CLASS < RUNTIME** ，所以前者能作用的地方后者一定也能作用。一般如果需要在运行时去动态获取注解信息，那只能用 `RUNTIME `注解；如果要在编译时进行一些预处理操作，比如生成一些辅助代码（如 [ButterKnife](https://github.com/JakeWharton/butterknife)），就用 `CLASS`注解；如果只是做一些检查性的操作，比如` @Override` 和 `@SuppressWarnings`，则可选用 `SOURCE` 注解。

**@Target**

表示该注解用于什么地方，可能的值在枚举类`ElementType`中

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}

public enum ElementType {
    TYPE,  // 类、接口(包含注解类型)或enum声明
    FIELD, // 字段声明(包含enum实例)
    METHOD, // 方法声明
    PARAMETER, // 参数声明
    CONSTRUCTOR, // 构造器声明
    LOCAL_VARIABLE, // 局部变量声明
    ANNOTATION_TYPE, // 注解声明
    PACKAGE, // 包声明

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

**@Documented**

将此注解包含在 javadoc 中 ，它代表着此注解会被javadoc工具提取成文档。在doc文档中的内容会因为此注解的信息内容不同而不同。相当与@see,@param 等。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Documented {
}
```

**@Inherited**

允许子类继承父类中的注解

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Inherited {
}
```

**@Repeatable**

@Repeatable 是 Java 1.8 才加的，意为可重复，也就是注解的值可以同时取多个

示例，一个人



#### 普通注解

##### 基本内置注解

**@Override**

该注解提示子类复写父类中的方法

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

**@Deprecated**

这注解用来标记过时的元素。编译器在编译阶段遇到这个注解时会发出提醒警告，告诉开发者正在调用一个过时的元素（过时的方法、过时的类、过时的成员变量）

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})
public @interface Deprecated {
}
```

**@SuppressWarnings**

阻止警告的意思。之前说过调用被 @Deprecated 注解的方法后，编译器会警告提醒，而有时候开发者会忽略这种警告，他们可以在调用的地方通过 @SuppressWarnings 达到目的。

```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    String[] value();
}
```

##### 自定义注解

关于自定义注解，请移步

[java 自定义源码注解](/2018/01/25/study/study-bwh/)

[java 自定义编译时注解](/2018/01/25/study/study-bwh/)

[java 自定运行时注解](/2018/01/25/study/study-bwh/)