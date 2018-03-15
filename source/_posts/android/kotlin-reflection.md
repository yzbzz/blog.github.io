---
title: Kotlin反射
date: 2018-03-12 11:18:35
tags: [android, kotlin]
---

### 反射

在`Java`平台，使用反向功能需要单独的Jar包`(kotlin-reflect.jar)`。使用`Android Studio`开发`Kotlin`项目，会自动引入`kotlin-reflect.jar`

### 类引用

最基本的反射功能是获取kotlin类的运行时引用

以下示例为`Java`、`Kotlin`的实现

Java

```java
Class<?> myClass = MyClass.class;
Class<?> myClass = Class.forName("packageName.MyClass")
```

kotlin(如果调用的类是java类，需要添加`.java`后缀)

```kotlin
// 引用kotlin类
val c = MyClass::class
// 引用java类
val c = MyClass::class.java
```

### 绑定的类引用

我们可以使用`::class`语法获取对象的精确类的引用

```kotlin
open class Fruit()
class Apple: Fruit()
val fruit: Fruit = Apple()
println("fruit type is ${fruit::class.qualifiedName}") // fruit type is Apple
```

### 函数引用

可以通过下面的方式获取到函数

```kotlin
class Point(var x: Int, var y: Int) {
	fun getPointX(): Int {
		return x
	}

	fun printPointY() {
		println(y)
	}
}

// 第一种：可以使用::操作符获取函数
val getX = Point::getPointX
val getY = Point::printPointY
val p = Point(1, 2)
println(getX.invoke(p))
getY.invoke(Point(3, 4))

// 第二种：使用Java的反射方式
val getX = Point::class.java.getMethod("getPointX")
print(getX.invoke(Point(2, 3)))
```

我们可以使用`::`操作符把一个函数作为值传递给另一个函数

```kotlin
fun isOdd(x: Int) = x % 2 != 0
val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // 输出 [1, 3]
```

这里 `::isOdd` 是函数类型 `(Int) -> Boolean` 的一个值

`::`也可以用于重载函数

```kotlin
fun isOdd(x: Int) = x % 2 != 0
fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"

val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // refers to isOdd(x: Int)
```

或者，你也可以赋值给变量

```kotlin
val predicate: (String) -> Boolean = ::isOdd   // 引用到 isOdd(x: String)
```

如果我们需要使用一个类的成员，或者一个扩展函数，它需要是限定的。例如：`String::toCharArray`为`String`类提供的扩展函数:`String.() -> CharArray`

### 函数组合

考虑下面的函数

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}
```

它返回传递给它的两个函数的组合：`compose(f, g) = f(g(*))`。现在你可以这样调用它

```kotlin
fun length(s: String) = s.length

val oddLength = compose(::isOdd, ::length)
val strings = listOf("a", "ab", "abc")

println(strings.filter(oddLength)) // Prints "[a, abc]"
```

### 属性引用

我们可以使用`::`来访问属性

```kotlin
val x = 1
var y = 1

fun main(args: Array<String>) {
    println(::x.get()) // prints "1"
    println(::x.name)  // prints "x"
    
    ::y.set(2)
    println(y) // 输出 "2"
}
```

> 对于`val`，表达式`::x`返回`KProperty<Int>`类型的值，我们可以使用`get()`来取值，也可以直接使用`name`来取值。
>
> 对于`var`，表达式`::y`返回 `KMutableProperty`类型的值，提供`get()/set()`供我们调用

在没有参数的函数中，也可以使用属性引用：

```kotlin
val strs = listOf("a", "bc", "def")
println(strs.map(String::length)) // prints [1, 2, 3]
```

访问类的成员属性

```kotlin
class A(val p: Int)

fun main(args: Array<String>) {
    val prop = A::p
    println(prop.get(A(1))) // prints "1"
}
```

对于扩展属性

```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main(args: Array<String>) {
    println(String::lastChar.get("abc")) // prints "c"
}
```

### 与Java反射的互操作性

 例如，要查找一个用作 Kotlin 属性 getter 的 幕后字段或 Java方法，可以这样写

```kotlin
import kotlin.reflect.jvm.*
 
class A(val p: Int)
 
fun main(args: Array<String>) {
    println(A::p.javaGetter) // prints "public final int A.getP()"
    println(A::p.javaField)  // prints "private final int A.p"
}
```

获取对应于`Java`的`Kotlin`类，使用`.kotlin`扩展属性

```kotlin
fun getKClass(o: Any): KClass<Any> = o.javaClass.kotlin
```

### 构造函数引用

可以使用`::`操作符来引用构造函数

```kotlin
class Foo

fun function(factory: () -> Foo) {
    val x: Foo = factory()
}

function(::Foo)
```

### 绑定函数和属性引用

您可以引用特定对象的实例方法:

```kotlin
val numberRegex = "\\d+".toRegex()
println(numberRegex.matches("29")) // prints "true"
 
val isNumber = numberRegex::matches
println(isNumber("29")) // prints "true"

val strings = listOf("abc", "124", "a70")
println(strings.filter(numberRegex::matches)) // 输出“[124]”
```

你也可以进行赋值操作

```kotlin
val isNumber: (CharSequence) -> Boolean = numberRegex::matches
val matches: (Regex, CharSequence) -> Boolean = Regex::matches
```

属性引用也可以被绑定:

```kotlin
val prop = "abc"::length
println(prop.get())   // prints "3"
```

无需显式指定 `this` 作为接收者：`this::foo` 与 `::foo` 是等价的。