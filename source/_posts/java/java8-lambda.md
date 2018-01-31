---
title: Lambda 表达式
date: 2016-05-19 14:18:52
tags: [java,lambda]
---

# 概述

Java 8 的最大变化是引入了Lambda表达式 —— 一种紧凑的、传递行为的方式，接下来就了解一下什么是Lambda表达式。

> 为什么要叫这个名字呢？这多年前，在计算机出现之前，有位名叫`Alonzo Church`的逻辑学家，他想要证明什么样的数学函数是可以有效计算的(奇怪的是，当时已经存在了许多已知的函数，但是没有人知道怎么去计算它们的值)。他使用希腊字母的`lambda(𝛌)`来标记参数 。为什么使用`𝛌`?难道Church没有其它拉丁字母可用了吗？事实上经典的《数学原理》中使用"^"符号表示自由变量，这启发Church使用大写的`lambda "Λ"`来表示参数。但是最终，他选择换回到小写版本。于是从那时起，还有参数变量的表达式都被称为lambda表达式。

<!-- More -->

### 第一个Lambda表达式

> Lambda表达式适用于只包含一个方法的接口的情况，表如：Runnable接口，只包含一个run方法。

正常情况:

```java
new Thread(new Runnable() {
    @Override
    public void run() {
      System.out.println("Hello");
    }
}).start();
```

使用Lambda:

```java
new Thread(() -> System.out.println("Hello")).start();
new Thread(() -> {System.out.println("Hello");}).start();
```

> 你已经见到了 **Java** 中的  **lambda** 表达式的格式：参数、箭头 ->，以及一个表达式

### Lambda表达式的不同形式

```java
1.Runnable noArguments = () -> System.out.println("Hello World");
2.ActionListener oneArguments = event -> System.out.println("Button Clicked");
3.Runnable multiStatement = () -> {
  	  System.out.println("Hello");
  	  System.out.println("World");
  };
4.BinaryOperator<Long> add = (x, y) -> x + y;
5.BinaryOperator<Long> addExplicit = (Long x, Long y) -> x + y;
6.变种 System.out::println
```

1. Lambda表达式不包含参数，使用空括号表示没有参数

2. Lambda表达式包含且只包含一个参数，可省略参数的括号，如上面示例中的第2种情况

3. Lambda表达式不仅可以是一个表达式，而且也可以是一段代码块，使用大括号({})将代码块括起来，如上面示例中的第3种情况。

   注：只有一行代码的Lambda表达式也可使用大括号，但需要在表达式后面加上一个分号。

4. Lambda表达式也可以包含多个参数的方法，如示例中的第4种情况。这时就有必要思考怎么样去阅读该Lambda表达式。这行代码并不是将两个数字相加，而是创建一个函数 ，用来计算两个数字相加的结果。变量add的类型是BinaryOperator<Long>，它不是两个数字的和，而是将两个数字相加的那行代码。

5. 所有Lambda表达式的参数类型都是由编译器推断得出的。但有时也可以显示声明参数类型，此时就需要使用小括号将参数括起来，多个参数也是如此。如上面示例中的第5种情况

6. 详情请阅读下面的 **方法引用** 章节


> 你可以像对待方法参数一样向lambda表达式的参数添加注解或者final修饰符，如下：
>
> ```java
> (final String name) -> ...
> (@NonNull String name) -> ...
> ```
>
> 注意：在lambda表达式中，只在某些分支中返回值(其它分支没有返回值)是不合法的。例如:
>
> ```java
> (int x) -> { if (x > 0) return 1; }
> ```

### 引用值，而不是变量

> Lambda表示式中引用的局部变量，必须是final或既成事实上的final变量

什么是既成事实上的final变量呢？看看下面这种情况：

```java
String test = "hello";
new Thread(() -> System.out.println(test)).start();
```

对于变量test，虽然可以不用final修饰(编译不会报错)，但是只要你对test再赋值其它值，就会编译报错，这种情况就是既成事实的final变量

### 函数接口

> 函数接口是只有一个抽象方法的接口，用作lambda表达式的类型
>
> 你可能奇怪为什么函数接口必须只有一个抽象方法。难道接口中的方法不都是抽象的吗？事实上，接口经常会重新声明Object类中的方法，例如`toString`或者`clone`，而这些方法声明并不是抽象的。

使用Java编程，总会遇到很多函数接口，但 Java 开发工具包（JDK）提供的一组核心函数接口会频繁出现。如下：

| 接口                | 参数     | 返回类型    | 示例            |
| ----------------- | ------ | ------- | ------------- |
| Predicata<T>      | T      | boolean | 这张唱片已经发行了吗    |
| Consumer<T>       | T      | void    | 输出一个值         |
| Function<T>       | T      | R       | 获取Artist对象的名字 |
| Supplier<T>       | None   | T       | 工厂方法          |
| UnaryOperator<T>  | T      | T       | 逻辑非(!)        |
| BinaryOperator<T> | (T, T) | T       | 求两个数的乘积(*)    |

> 你可以在任意函数式接口上标注`@FunctionalInterface`注解，这样做有两个好处。首先，编译器会检查标注该注解的实体，检查它是否是只包含一个抽象方法的接口。别外，在 javadoc 页面也会包含一条声明，说明这个接口是一个函数式接口。
>
> 该注解并不要求强制使用。从概念上来讲，所有只包含一个抽象方法的接口都是函数式接口，但是使用`@FuncationalInterface`注解会让你的代码看上去更清楚。

最后，当一个 **lambda** 表达式被转换为一个函数接口的实例时，请注意处理检查期异常。如果  **lambda** 表达式中可能会抛出一个检查期异常，那么该异常需要在目标接口的抽象方法中进行声明。例如，以下表达式会产生一个错误：

```java
Runnable sleeper = () -> { System.out.println("Zzz"); Thread.sleep(1000); };
// 错误：Thread.sleep可以抛出一个检查期的 InterruptedException.
```

由于 **Runnable.run** 不能抛出任何异常，所有这个赋值不是合法的，有两种方法可以修正该问题。一种是在 **lambda** 表达式中捕获异常，别一种是将  **lambda** 表达式赋给一个其抽象方法可以抛出异常的接口。例如，Callable 接口的 **call** 方法可以抛出任何异常，因此，你可以将该 **lambda** 表达式赋给 Callable<Void> (如果你添加一条 "return null" 语句)。

### 方法引用 

 一共有四种类型的方法引用

| 类型        | 示例                                |
| --------- | --------------------------------- |
| 类静态方法引用   | ContainingClass::staticMethodName |
| 某个对象的方法引用 |                                   |
|           |                                   |



> 参考文献：《Java8函数式编程》