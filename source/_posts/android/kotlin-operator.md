---
title: Kotlin操作符重载
date: 2018-03-02 16:42:35
tags: [android, kotlin]
---

### 操作符重载

#### 一元操作符

| 表达式   | 对应函数       |
| -------- | -------------- |
| +a       | a.unaryPlus()  |
| -a       | a.unaryMinus() |
| !a       | a.not()        |
| ++a, a++ | a.inc()        |
| --a, a-- | a.dec()        |

<!--more-->

示例：

```kotlin
data class Point(val x: Int, val y: Int)
operator fun Point.unaryMinus() = Point(-x, -y)
operator fun Point.inc() = Point(x + 1, y + 1)
var p = Point(10, 20)
println(-p) // Point(x=10, y=-20)
println(++p) // Point(x=11, y=21)
```

#### 二元操作符

| 表达式 | 对应函数                 |
| ------ | ------------------------ |
| a + b  | a.plus(b)                |
| a - b  | a.minus(b)               |
| a * b  | a.times(b)               |
| a / b  | a.div(b)                 |
| a % b  | a.rem(b)、a.mod(b)(弃用) |
| a..b   | a.rangeTo(b)             |

示例：

```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}
val p1 = Point(10, 20)
val p2 = Point(30, 40)
println(p1 + p2) // Point(x=40, y=60)
```

#### 没有用于位运算的特殊运算符

- shl — 带符号左移
- shr — 带符号右移
- ushr — 无符号右移
- and — 按位与
- or — 按位或
- xor — 按位异或
- inv — 按位取反

示例：

```kotlin
// kotlin
val num = 1 shl 4
println(num) // 16
==>
// java
int num = 1 << 4
```

#### in操作符

| 表达式  | 对应函数       |
| ------- | -------------- |
| a in b  | b.contains(a)  |
| a !in b | !b.contains(a) |

示例：

```kotlin
data class Rectangle(val upperLeft: Point, val lowerRight: Point)
operator fun Rectangle.contains(p: Point): Boolean {
    return p.x in upperLeft.x until lowerRight.x &&
    	p.y in upperLeft.y until lowerRight.y
}
val rect = Rectangle(Point(10, 20), Point(50, 50))
println(Point(20, 30) in rect) // true
```

#### 下标访问操作符(通过下标来访问元素：`get` 和 `set`)

| 表达式               | 对应函数                |
| -------------------- | ----------------------- |
| a[i]                 | a.get(i)                |
| a[i, j]              | a.get(i, j)             |
| a[i_1, …, i-n]       | a.get( i_1, ..., i_n)   |
| a[i] = b             | a.set(i, b)             |
| a[i, j] = b          | a.set(i, j, b)          |
| a[i_1, ..., i_n] = b | a.set(i_1, ..., i_n, b) |

示例：

```kotlin
// 一维
operator fun Point.get(index: Int): Int {
    return when(index) {
        0 -> x
        1 -> y
        else -> throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

val p = Point(10, 20)
println(p[1]) // 20

// 二维
data class MutablePoint(var x: Int, var y: Int)
operator fun MutablePoint.set(index: Int, value: Int) {
    when(index) {
        0 -> x = value
        1 -> y = value
        else -> 
        	throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}
val p = MutablePoint(10, 20)
p[1] = 42
println(p) // MutablePoint(x=10, y=42)
```

#### 调用操作符(invoke)

| 表达式         | 对应函数              |
| -------------- | --------------------- |
| a()            | a.invoke()            |
| a(i)           | a.invoke(i)           |
| a(i, j)        | a.invoke(i, j)        |
| a(i_i, …, i_n) | a.invoke(i_1, …, i_n) |

圆括号转换为调用带有适当数量参数的 `invoke`

示例：

```kotlin
class Greeter(val greeting: String) {
    operator fun invoke(name: String) {
        println("$greeting, $name!")
    }
}

val bavarianGreeter = Greeter("Servus") 
bavarianGreeter("Dmitry") // Servus, Dmitry!
==>
val bavarianGreeter = Greeter("Servus")("Dmitry") // Servus, Dmitry!
```

可以通过重写`invoke`实例工厂模式

```kotlin
object Any {
	operator fun invoke(name: String): String {
		return name
	}
	
	operator fun invoke(number: Int): Int {
		return number
	}
}
val string = Any("Hello World!");
val int = Any("1");
println("$string, $int") // Hello World! 1
```

#### 复合赋值运算符

| 表达式 | 对应函数                               |
| ------ | -------------------------------------- |
| a += b | a.plusAssign(b)                        |
| a -= b | a.minusAssign(b)                       |
| a *= b | a.timesAssign(b)                       |
| a /= b | a.divAssign(b)                         |
| a %= b | a.remAssign(b),  a.modAssign(b) (弃用) |

示例：

```kotlin
operator fun <T> MutableCollection<T>.plusAssign(element: T) {
	this.add(element)
}
val list = arrayListOf(1, 2)
list += 3
val newList = list + listOf(4, 5)
println(list) // [1, 2, 3]
println(newList) // [1, 2, 3, 4, 5]
```

#### 比较运算符(equals)

| 表达式 | 对应函数                         |
| ------ | -------------------------------- |
| a == b | a?.equals(b)  ?:  (b === null)   |
| a != b | !(a?.equals(b))  ?:  (b ===null) |

(等式校验 `==` 被转换为`equals`函数的调用，以及`null`的校验)

*注意*：`===` 和 `!==`（同一性检查）不可重载，因此不存在对他们的约定。

这个 `==` 操作符有些特殊：它被翻译成一个复杂的表达式，用于筛选 `null` 值。 `null == null` 总是 true，对于非空的 `x`，`x == null` 总是 false 而不会调用 `x.equals()`。

示例：

```kotlin
class Point(val x: Int, val y: Int) {
    override fun equals(obj: Any?): Boolean {
        if (obj === this) return true
        if (obj !is Point) return false
        return obj.x == x && obj.y == y
    }
}
println(Point(10, 20) == Point(10, 20)) // true
println(Point(10, 20) != Point(5, 5)) // true
println(null == Point(1, 2)) // false
```

| 表达式   | 函数名     |
| -------- | ---------- |
| +a       | unaryPlus  |
| -a       | unaryMinus |
| !a       | not        |
| ++a, a++ | inc        |
| —a, a--  | dec        |

示例：

```kotlin
operator fun Point.unaryMinus(): Point {
    return Point(-x, -y)
}
val p = Point(10, 20)
println(-p) // Point(x=10, y=-20)
```



#### 比较操作符(compareTo)

| 表达式 | 对应函数            |
| ------ | ------------------- |
| a > b  | a.compareTo(b) > 0  |
| a < b  | a.compareTo(b) < 0  |
| a >= b | a.compareTo(b) >= 0 |
| a <= b | a.compareTo(b) <= 0 |

所有的比较都转换为对 `compareTo` 的调用，这个函数需要返回 `Int` 值

示例：

```kotlin
class Person(val firstName: String, val lastName: String): Comparable<Person> {
    override fun compareTo(other: Person): Int {
        return compareValuesBy(this, other, Person::lastName, Person::firstName)
    }
}
val p1 = Person("Alice", "Smith")
val p2 = Person("Bob", "Johnson")
println(p1 < p2) // false
```

> 参考资料：
>
> [Kotlin in Action](https://www.manning.com/books/kotlin-in-action)
>
> [Operator overloading](http://kotlinlang.org/docs/reference/operator-overloading.html#assignments)

