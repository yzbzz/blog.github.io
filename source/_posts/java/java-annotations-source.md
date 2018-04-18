---
title: java 源码注解(RetentionPolicy.SOURCE)
date: 2018-04-13 16:00:56
tags: [java, Annotation]
---

### 前言

通过之前的文章[Java 注解介绍](/2018/04/12/java/java-annotations/)，我们了解到源码注解(`RetentionPolicy.SOURCE`)的生命周期只存在Java源文件这一阶段，是3种生命周期中最短的注解。当在Java源程序上加了一个注解，这个Java源程序要由javac去编译，javac把java源文件编译成.class文件，在编译成class时会把Java源程序上的源码注解给去掉。需要注意的是，在编译器处理期间源码注解还存在，即`注解处理器Processor` 也能处理源码注解，编译器处理完之后就没有该注解信息了。

### 定义源码注解

在`Android`中，我们经常会用到下拉刷新的功能，通常会有四种状态

- PULL_FROM_START：下拉刷新
- PULL_FROM_END：上拉加载
- BOTH：同时设置下拉刷新和上拉加载
- DISABLED：禁用下拉刷新和上拉加载

为了限制程序只能使用上面4种状态，我们一般会定义一个`Enum`枚举作为参数，这样就能限定参数的取值范围

```java
public class PullToRefresh {
	private Mode mMode =  Mode.PULL_FROM_START;
    
	public void setMode(Mode mode) {
		mMode = mode;
	}

	public Mode getMode() {
		return mMode;
	}

	public void doSomething() {
		if (mMode == Mode.PULL_FROM_START) {
		} else if (mMode == Mode.PULL_FROM_END) {
		} else if (mMode == Mode.BOTH) {
		} else if (mMode == Mode.DISABLED) {
		}
	}
    
	public enum Mode {
		PULL_FROM_START, PULL_FROM_END, BOTH, DISABLED
	}
}

// 通过以下的方式调用
PullToRefresh pullToRefresh = new PullToRefresh();
pullToRefresh.setMode(Mode.BOTH);
```

根据google官网的描述，使用枚举会比常量占用更多的内存。这个时候我们就可以自己定义一个注解来处理上面的问题

```java
public class PullToRefresh {

	public static final int PULL_FROM_START = 1;
	public static final int PULL_FROM_END = 2;
	public static final int BOTH = 3;
	public static final int DISABLED = 4;

	private static int sMode = PULL_FROM_START;

	public void setMode(@Mode int mode) {
		sMode = mode;
	}

	public int getMode() {
		return sMode;
	}

	public void doSomething() {
		if (sMode == PULL_FROM_START) {
		} else if (sMode == PULL_FROM_END) {
		} else if (sMode == BOTH) {
		} else if (sMode == DISABLED) {
		}
	}

	@Retention(RetentionPolicy.SOURCE)
	@Target(ElementType.PARAMETER)
	@IntDef({PULL_FROM_START,PULL_FROM_END,BOTH,DISABLED})
	public @interface Mode {
	}
}

// 通过以下的方式调用
PullToRefresh pullToRefresh = new PullToRefresh();
pullToRefresh.setMode(PullToRefresh.BOTH);
```

其中的`@IntDef`为`Android`提供的源码注解，用于表示取值范围，在`android.support.annotation`包下

```java
@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.ANNOTATION_TYPE})
public @interface IntDef {
    /** Defines the allowed constants for this element */
    long[] value() default {};

    /** Defines whether the constants can be used as a flag, or just as an enum (the default) */
    boolean flag() default false;
}
```

类似的还有`@StringDef`

```java
@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.ANNOTATION_TYPE})
public @interface StringDef {
    /** Defines the allowed constants for this element */
    String[] value() default {};
}
```

### 结束语

总的来说，了解了源码注解，我们可以更规范的写代码，避免的很多问题的发生