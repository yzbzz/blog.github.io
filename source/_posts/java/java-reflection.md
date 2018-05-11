---
title: Java反射
date: 2018-04-27 16:35:46
tags: java
---

### 前言

在Java中，我们主要通过反射(Reflection)来在运行时来获取类或对象的相关信息

- 获取对象所属的类
- 构造类的对象
- 获取类的构造函数对象
- 获取类的成员变量，包括`private` 修饰的变量
- 调用类的方法，包括`private`修饰的方法

<!-- More -->

### 基本运用

在使用反射时，我们会先获取`Class对象`，再通过`Class对象`获取对象常用的操作类：`Constructor(构造函数)`、`Field(字段)`、`Method(方法)`

#### Class类

**`Class`代表类对象，可以通过以下几种方式获取**

```java
// 第一种
Class<?> cls1 = MyClass.class;

// 第二种
MyClass myClass = new MyClass();
Class<?> cls2 = myClass.getClass();

// 第三种，forName方法的参数需要填完整的包名和类名
Class<?> cls3 = null;
try {
    cls3 = Class.forName("reflection.MyClass");
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

**获取类名称**

```java
Class<?> myClass = MyClass.class;
// 返回类的名称(包含包名): reflection.MyClass
myClass.getName() 
// 返回类的名称(不包含包名): MyClass
myClass.getSimpleName()
```

**创建实例**

```java
Class<?> cls = MyClass.class;
// 调用newInstance()方法获取实例对象时，类必需提供一个公有的无参数的构造函数
MyClass myClass = cls.newInstance();
```

**Class类的一些常用方法**

```java
// 获取父类
public native Class<? super T> getSuperclass();
// 获取实现的所有接口
public Class<?>[] getInterfaces();
```



#### Constructor类

**`Constructor`表示类的构造器对象，可以通过以下方式获取。其中类似`getDeclaredConstructor`的方法返回不限修饰符的构造函数，`getConstructor`的方法表示只返回`public`修饰的构造函数**

部分源码:

```java
// 返回公有(public修饰)构造函数对象列表，不包含除public修饰的构造函数 
public Constructor<?>[] getConstructors();

// 返回所有构造函数对象列表，不限修饰符(包含private等修饰的构造函数)
public Constructor<?>[] getDeclaredConstructors();

// 根据给定的参数类型返回构造函数，如果获取的构造函数不是public修饰的，会报NoSuchMethodException
public Constructor<T> getConstructor(Class<?>... parameterTypes);
    
// 根据给定的参数类型返回不限修饰符的构造函数
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes);
```

示例：

```java
public class MyClass {
	public MyClass() {} // 第一个
	public MyClass(int arg) {} // 第二个
	protected MyClass(String arg) {} // 第三个
	private MyClass(int arg1, String arg2) {} // 第四个
}
Class<?> myClass = MyClass.class;

// 返回公有(public修饰)构造函数对象列表，不包含除public修饰的构造函数(2个对象)
Constructor[] constructors = myClass.getConstructors() 
    
// 返回所有构造函数对象列表，包含private等修饰的构造函数(4个对象)
Constructor[] declaredConstructors = myClass.getDeclaredConstructors() 
    
// 报NoSuchMethodException，因为getConstructor只能获取public修饰的构造函数，这里的第四个构造是private修饰的
Constructor<?> constructor = myClass.getConstructor(int.class, String.class);

// 返回第四个构造函数
Constructor<?> declaredConstructor = myClass.getDeclaredConstructor(int.class, String.class);
```

**创建实例**

我们可以使用`newInstance`方法创建实例，这里有一点要注意的是：`newInstance`方法需要修饰符为`public`的构造函数来调用，否则会创建实例时会报错。看下面的例子

```java
Class<?> myClass = MyClass.class;

Constructor<?> intConstructor = myClass.getConstructor(int.class);
// 正常获取，因为参数为int的构造函数的访问级别是public的
MyClass myClass1 = (MyClass) intConstructor.newInstance(1);

Constructor<?> intAndStringConstructor = myClass.getDeclaredConstructor(int.class, String.class);
// 报IllegalAccessException, 因为参数为int, String的构造函数的访问级别是private的
MyClass myClass2 = (MyClass) intAndStringConstructor.newInstance(1, "hello");
```

**Constructor对象的一些常用方法**

```java
Constructor<?> constructor = myClass.getDeclaredConstructor(int.class, String.class);

// 获取构造函数的访问级别(public,private等),可以调用Modifier类的一些方法(isPublic/isPrivate)来判断访问级别
int modifiers = constructor.getModifiers();

// 获取构造函数的参数数量
int count = constructor.getParameterCount();

// 获取构造函数的参数列表
Parameter[] Parameteres = constructor.getParameters();
```

### Field类

**`Field`表示类的字段对象，可以通过以下方式获取。其中类似`getDeclaredField`的方法返回不限修饰符的字段，`getField`的方法表示只返回`public`修饰的字段。`getField`会从当前类开始，一直找到最顶级的父类(Object)为止**

部分源码：

```java
// 只返回public修饰的字段, 包括父类的字段
public Field[] getFields();

// 返回本类的所有字段(不包括父类)，不限修饰符
public Field[] getDeclaredFields();

// 通过给定的字段名称(包括父类)，获取Field对象，如果字段名称不存在或Field对象不是public修饰的，会报NoSuchFieldException
public Field getField(String name);

// 通过给定的字段名称(不包括父类)，获取不限修饰符的字段
public Field getDeclaredField(String name)
```

示例：

```java
public class FatherClass {
	public String fatherName = "fatherName";
}
public class MyClass extends FatherClass {
	public static String TAG = "tag";
	public int age;
	protected String name;
	private String address;
}

Class<?> myClass = MyClass.class;

// 只返回public修饰的字段(TAG, age, fatherName), 包括父类的public字段。FatherClass继承至Object，但Object没有字段，所以只会返回MyClass和FatherClass的所有public字段
Field[] fields = myClass.getFields();

// 返回本类的所有字段(不包括父类)，不限修饰符(TAG, age, name ,address)
Field[] declaredFields = myClass.getDeclaredFields();

// 报NoSuchFieldException，因为address字段是private修饰的
Field addressField = myClass.getField("address");

// 通过给定的字段名称，获取Field对象，返回address字段对象
Field addressDeclaredField = myClass.getDeclaredField("address");
```

**`Field`一些常用的方法**

`Field`提供了`get`/`set`方法用于获取、设置字段的值。同时提供了基本数据类型的`get`/`set`方法，用于简化操作。下面是截取的`Field`类的部分代码

`Get`/`Set`方法

```java
// get方法
public native Object get(Object obj) 
// 基本数据类的get方法
public native boolean getBoolean(Object obj)
public native byte getByte(Object obj)
public native char getChar(Object obj)
public native int getInt(Object obj)
public native double getDouble(Object obj)
...
    
// set方法
public native void set(Object obj, Object value)
// 基本数据类的set方法
public native void setBoolean(Object obj, boolean z)
public native void setInt(Object obj, int i)
public native void setDouble(Object obj, double d)
...
```

需要注意的是

1.对于非静态字段来说，调用`get/set`方法，`Object`参数需要传实例对象。对于静态字段来说，可以传实例对象，也可以传null，因为实例对象对静态字段来说，没有意义。

2.对于非公有字段(private/protected)等，在进行访问的时候需要先调用`Field`类的`setAccessible(true)`

示例

```java
Class<?> myClass = MyClass.class;
MyClass cls = (MyClass) myClass.newInstance();

// 对于静态字段
Field tagFiled = myClass.getDeclaredField("TAG");
// 下面的代码是等价的
String tag1 = (String) tagFiled.get(null);
String tag2 = (String) tagFiled.get(cls);

// 对于public修改的字段
Field ageFiled = myClass.getDeclaredField("age");
// 对于get, 下面的代码是等价的
int age1 = (int) ageFiled.get(cls);
int age2 = ageFiled.getInt(cls); 
// 对于set, 下面的代码是等价的
ageFiled.setInt(cls, 3);
ageFiled.set(cls, 5);

// 对于private修饰的字段
Field addressFiled = myClass.getDeclaredField("address");
// 需要先调用addressFiled.setAccessible(true)，否则会报IllegalAccessException，因为address字段是private修饰的
addressFiled.setAccessible(true);
String address = (String) addressFiled.get(cls);
```

其它方法

```java
// 返回字段的访问级别(public/private等)，可以通过Modifier类来进行一些操作
public int getModifiers()
// 返回字段的名称
public String getName()
// 返回字段的类型(int, String, double等)
public Class<?> getType()
// 返回字段所在的类
public Class<?> getDeclaringClass()
```

### Method类

**`Method`表示类的方法对象，可以通过以下方式获取。和`Constructor`/`Field`相似，提供`getDeclaredMethod`的方法返回不限修饰符的方法，`getMethod`的方法表示只返回`public`修饰的方法。`getMethod`会从当前类开始，一直找到最顶级的父类(Object)为止**

部分源码：

```java
// 只返回public修饰的方法, 包括父类的方法
public Method[] getMethods();

// 返回本类的所有方法(不包括父类)，不限修饰符
public Method[] getDeclaredMethods();

// 通过给定的方法名称和参数类型(包括父类)，获取Method对象，如果找不到方法或Method对象不是public修饰的，会报NoSuchMethodException
public Method getMethod(String name, Class<?>... parameterTypes)
    
// 通过给定的方法名称(不包括父类)，获取不限修饰符的Method对象
public Method getDeclaredMethod(String name, Class<?>... parameterTypes)
```

示例：

```java
public class MyClass {
	public int getAge() {
		return 1;
	}

	private String getName() {
		return "张三";
	}

    public String getSomething(String something) {
		return something;
	}
    
	public static String getAddress() {
		return "北京";
	}
    
    private void excuteCommand(String command) {
		if (command.equals("1")) {
			System.out.println("执行命令1");
		} else if (command.equals("2")) {
			System.out.println("执行命令2");
		} else {
			System.out.println("找不到命令");
		}
	}
}
Class<?> myClass = MyClass.class;

// 不仅会返回本类的public方法，还会返回Object类的public方法，因为MyClass是继承Object类的
Method[] methods = myClass.getMethods();

// 返回本类的所有方法(getAge, getName, getAddress)，不限修饰符
Method[] declaredMethods = myClass.getDeclaredMethods();

// 通过给定的方法名称和参数类型，获取Method对象
Method ageMethod = myClass.getMethod("getAge", null);
Method notifyMethod = myClass.getMethod("notify", null);
Method somethingMethod = myClass.getMethod("getSomething", String.class);

// 报NoSuchMethodException，因为getName方法是private修饰的
Method nameMethod = myClass.getMethod("getName", null);
```

**Method类的一些常用方法**

`Method`类提供`invoike`方法，来执行方法

源码：

```java
public Object invoke(Object obj, Object... args)
```

和`Field`类似，对于非`public`修饰的方法，需要先调用`setAccessible(true)`

示例：

```java
Class<?> myClass = MyClass.class;
MyClass cls = (MyClass) myClass.newInstance();

Method commandMethod = myClass.getDeclaredMethod("excuteCommand", String.class);
// 需要先调用commandMethod.setAccessible(true)，否则会报IllegalAccessException，因为excuteCommand方法是private修饰的
commandMethod.setAccessible(true);
commandMethod.invoke(cls, "1"); // 执行命令1
commandMethod.invoke(cls, "3"); // 找不到命令
```

其它常用方法

```java
// 返回方法的访问级别(public/private等)，可以通过Modifier类来进行一些操作
public int getModifiers()
// 返回方法的名称
public String getName()
// 返回方法的返回类型(void, int, String, double等)
public Class<?> getReturnType()
// 返回方法所在的类
public Class<?> getDeclaringClass()
// 返回参数列表的类型数组
public Class<?>[] getParameterTypes()
// 返回参数列表数量
public int getParameterCount()
```

