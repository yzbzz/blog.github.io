---
title: kotlin委托
date: 2018-03-09 11:15:35
tags: [android, kotlin]
---

### 类委托

创建一个类`Derived`，实例`Base`接口，并将其所有公共方法委托给指定的对象。

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main(args: Array<String) {
    val b = BaseImpl(10)
    Derived(b).print() // prints 10
}
```

`Derived`超类列表中的`by-`子句表明，`b`将会在`Derived`对象的内部存储， 并且编译器将生成转发给 `b` 的所有 `Base` 的方法。

注意：`override`标注的方法会取代委托对象中的实现。如果你在`Derived`中添加了`override fun print() { print("abc") }`，程序会输入`abc`而不是`10`。

### 委托属性

**使用`by`关键字**

```kotlin
class Foo {
    private val delegate = Delegate()
    val p: Type
    	set(value: Type) = delegate.setValue(..., value)
    	get() = delegate.getValue(...)
}

class Delegate {
    operator fun getValue(...) { ... }
    operator fun setValue(..., value: Type) { ... }
}

==>

class Foo {
    var p: Type by Delegate()
}

val foo = Foo()
val oldValue = foo.p
foo.p = newValue


```

**使用委托属性：惰性初始化和"by lazy()"**

我们有一个耗时的方法`loadEmails`，我们希望只有在首次访问时才加载，并且只加载一次，我们可以使用下面的方法：

```kotlin
class Person(val name: String) {
    val emails by lazy { loadEmails(this) }
}
```

**实例委托属性**

```kotlin
class Person(val name: String, age: Int, salary: Int): PropertyChangeAware() {
    var age: Int by Delegates.observable(0) {
        prop, old, new ->
        println("$old -> $new")
    }
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}
```

如果你想能够截获一个赋值并否决它，可以使用`vetoable`代替`observable`

```kotlin
class Person(val name: String, age: Int, salary: Int): PropertyChangeAware() {
    var age: Int by Delegates.observable(0) {
        prop, old, new ->
        new > old
    }
}
```

**把属性储存在Map中**

```kotlin
class Person(val map: Map<String, Any?) {
    val name: String by map
    val age: Int by map
}

// 属性的名称自动用作map中的键
val person = Person(mapOf("name" to "John Doe", "age" to 25))
person.name
person.age
```

这也适用于`var`属性，只需把`map`换成`mutableMap`

```kotlin
class Person(val map: mutableMap<String, Any?) {
    var name: String by map
    var age: Int by map
}
```

