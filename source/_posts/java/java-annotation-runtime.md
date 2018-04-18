---
title: java 运行时注解(RetentionPolicy.RUNTIME)
date: 2018-04-13 16:07:34
tags: [java, Annotation]
---

### 前言

对于运行时注解`RetentionPolicy.RUNTIME`，我们可以通过反射的方式来获取注解的相关信息。根据注解中的`Target`，我们可以从`Constructor`、`CLASS`、`Field`、`Method`获取注解对象，基本方法都差不多。

| Target                  | 获取Annotation                | 描述                        |
| ----------------------- | ----------------------------- | --------------------------- |
| ElementType.TYPE        | `Class<?>`. getAnnotation()   | 通过Class对象获取注解       |
| ElementType.CONSTRUCTOR | `Constructor`.getAnnotation() | 通过Constructor对象获取注解 |
| ElementType.FIELD       | `Field`.getAnnotation()       | 通过Field对象获取注解       |
| ElementType.METHOD      | `Method`.getAnnotation()      | 通过Method对象获取注解      |
| ...                     | ...                           | ...                         |

一些常见的获取注解的方法

```java
/**
 * 获取指定类型的注解
 */
public <A extends Annotation> A getAnnotation(Class<A> annotationType);

/**
 * 获取所有注解，如果有的话
 */
public Annotation[] getAnnotations();

/**
 * 获取所有注解，忽略继承的注解
 */
public Annotation[] getDeclaredAnnotations();

/**
 * 指定注解是否存在该元素上，如果有则返回true，否则false
 */
public boolean isAnnotationPresent(Class<? extends Annotation> annotationType);

/**
 * 获取Method中参数的所有注解
 */
public Annotation[][] getParameterAnnotations();
```

### 定义运行时注解

**自定义注解**

```java
// 适用于类、接口(包含注解类型)或enum
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface ClassAnnotation {
	String value();
}

// 适用于构造函数
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.CONSTRUCTOR)
public @interface ConstructorAnnotation {
	int id();
	String value();
}

// 适用于字段(包含enum实例)
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface FiledAnnotation {
	int[] value();
}

// 适用于方法
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MethodAnnotation {
	String value();
}
```

在注解中定义`成员(属性)`时它的类型必须是 8 种基本数据类型外加 类、接口、注解及它们的数组(下面例子中的`int id()`、`String value()`)。注解中`成员(属性)`可以有默认值，默认值需要用 `default` 关键值指定。

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.CONSTRUCTOR)
public @interface ConstructorAnnotation {
	int id() default 0;
	String value() default "ConstructorAnnotation";
}
```

**如何使用**

```java
@ClassAnnotation("this is class annotation")
public class TestAnnotation {

	@ConstructorAnnotation(id = 1, value = "this is constructor annotation")
	public TestAnnotation() {
	}

	@ConstructorAnnotation
	public TestAnnotation(int id, String msg) {
	}

	@FiledAnnotation({ 1, 2 })
	public int id = 1;

	@FiledAnnotation(3)
	public String field = "field";

	@MethodAnnotation("this is method annotation")
	public String method() {
		return "method";
	}
}
```

注解赋值的方式是在注解的括号内以` value=”” `形式，多个属性之前用`,`隔开。如果希望使用注解的默认值，可以像以述例子中一样直接使用`@ConstructorAnnotation`或`@ConstructorAnnotation()`

```java
@ConstructorAnnotation
public TestAnnotation(int id, String msg) {
}
==>
@ConstructorAnnotation()
public TestAnnotation(int id, String msg) {
}
```

如果注解只包含一个成员，并且成员定义的名称为`value()`，则可以省略`value`关键字

例如上面定义的`FiledAnnotation`和`MethodAnnotation`，都使用了`value()`名称

```java
public @interface FiledAnnotation {
	int[] value();
}
public @interface MethodAnnotation {
	String value();
}
```

则可以省略`value`关键字，下面的调用方式是等同的

```java
// 对于字段
@FiledAnnotation(value = { 1, 2 })
public int id = 1;
==>
@FiledAnnotation({ 1, 2 })
public int id = 1;

// 对于方法
@MethodAnnotation(value = "this is method annotation")
public String method() {
    return "method";
}
==>
@MethodAnnotation("this is method annotation")
public String method() {
    return "method";
}
```

### 获取运行时注解信息

因为我们定义的是`RetentionPolicy.RUNTIME`的注解，所以我们可以在运行时拿到注解的信息**(需要反射获取)**

```java
// 获取@ClassAnnotation的信息
public static void printClassAnnotation(Class<?> clazz) {
    StringBuilder sb = new StringBuilder();
    sb.append("Class注解:");
    ClassAnnotation classAnnotation = clazz
        .getAnnotation(ClassAnnotation.class);
    if (null != classAnnotation) {
        sb.append("\n\t类名: ");
        sb.append(Modifier.toString(clazz.getModifiers())).append(" ")
            .append(clazz.getSimpleName());
        sb.append("\n\t注解值: ").append(classAnnotation.value());
    }
    System.out.println(sb.toString());
}

// 获取ConstructorAnnotation的信息
public static void printConstructorAnnotation(Class<?> clazz) {
    StringBuilder sb = new StringBuilder();
    sb.append("Constructor注解:");
    Constructor<?>[] constructors = clazz.getConstructors();
    for (Constructor<?> constructor : constructors) {
        ConstructorAnnotation constructorAnnotation = constructor
            .getAnnotation(ConstructorAnnotation.class);
        sb.append("\n\t构造函数名: ");
        sb.append(Modifier.toString(constructor.getModifiers())).append(" ")
            .append(constructor.getClass().getSimpleName());

        sb.append("\n\t构造函数参数: ");
        Parameter[] parameters = constructor.getParameters();
        for (Parameter parameter: parameters) {
            sb.append("\n\t\t");
            sb.append(parameter.getType().getSimpleName()).append(" ")
                .append(parameter.getName());
        }

        sb.append("\n\t注解值: ")
            .append("\n\t\tid: " + constructorAnnotation.id())
            .append("\n\t\tvalue: " + constructorAnnotation.value());
        sb.append("\n\t---------------");
    }
    System.out.println(sb.toString());
}

// 获取@FieldAnnotation的信息
public static void printFieldAnnotation(Class<?> clazz) {
    StringBuilder sb = new StringBuilder();
    sb.append("Filed注解:");
    Field[] fields = clazz.getFields();
    for (Field field : fields) {
        FiledAnnotation filedAnnotation = field
            .getAnnotation(FiledAnnotation.class);
        if (null != filedAnnotation) {
            sb.append("\n\t字段名: ");
            sb.append(Modifier.toString(field.getModifiers())).append(" ")
                .append(field.getType().getSimpleName()).append(" ")
                .append(field.getName());
            sb.append("\n\t注解值: ").append(
                Arrays.toString(filedAnnotation.value()));
        }
    }
    System.out.println(sb.toString());
}
// 获取@MethodAnnotation的信息
public static void printMethodAnnotation(Class<?> clazz) {
    StringBuilder sb = new StringBuilder();
    sb.append("Method注解:");
    Method[] methods = clazz.getMethods();
    for (Method method : methods) {
        MethodAnnotation methodAnnotation = method
            .getAnnotation(MethodAnnotation.class);
        if (null != methodAnnotation) {
            sb.append("\n\t方法名: ");
            sb.append(Modifier.toString(method.getModifiers())).append(" ")
                .append(method.getReturnType().getSimpleName()).append(" ")
                .append(method.getName());
            sb.append("\n\t注解值: ").append(methodAnnotation.value());
        }
    }
    System.out.println(sb.toString());
}
```

输出如下信息===>

```java
Class注解:
	类名: public TestAnnotation
	注解值: this is class annotation
Constructor注解:
	构造函数名: public Constructor
	构造函数参数: 
	注解值: 
		id: 1
		value: this is constructor annotation
	---------------
	构造函数名: public Constructor
	构造函数参数: 
		int arg0
		String arg1
	注解值: 
		id: 0
		value: ConstructorAnnotation
	---------------
Filed注解:
	字段名: public int id
	注解值: [1, 2]
	字段名: public String field
	注解值: [3]
Method注解:
	方法名: public String method
	注解值: this is method annotation
```