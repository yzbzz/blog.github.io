---
title: Kotlin委托
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

<!--more-->

### 委托属性

kotlin库提供了如下常见的属性类型

- lazy properties(延迟属性):  该值只在首次访问时计算;
- observable properties(可观察属性):  监听器会收到该属性变化时的通知;
- 把属性存储在`map`中, 而不是每个属性都使用单独字段存储.

为了涵盖这些(或其他)情况，Kotlin支持委托的属性：

```kotlin
class Foo {
    var p: String by Delegate()
}
```

语法：`val/var <property name>: <Type> by <expression>`，使用`by`关键字来实现委托。

按照约定，Delegate类必须具有`getValue`和（`setValue` — 对于var属性)方法。编译器创建一个隐藏的辅助属性，并使用委托对象的实例进行实始化，初始属性`p`会委托给该实例。对应的`get()`、`set()`会调用实例的`getValue()`和`setValue`方法。它们可以是成员函数，也可以是扩展函数。为了让例子看起来更简洁，这里我们省略掉了参数：

下面是演化过程

```kotlin
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
class Foo {
    private val delegate = Delegate()
    val p: String
    	set(value: String) = delegate.setValue(..., value)
    	get() = delegate.getValue(...)
}

==>

class Foo {
    var p: String by Delegate()
}

val foo = Foo()
val oldValue = foo.p
foo.p = newValue
```

#### 使用委托属性：惰性初始化和"by lazy()"

我们有一个耗时的方法`loadEmails`，用来从数据库检索电子邮件，我们希望只有在首次访问时才加载，并且只加载一次，我们可以使用下面的方法：

```kotlin
fun loadEmails(person: Person): List<Email> {
    println("load emails for ${person.name}")
    return listOf(/*...*/)
}
class Person(val name: String) {
    private var _emails: List<Email>? = null
    
    val emails: List<Email>
        get() {
            if (_emails == null) {
                _emails = loadEmails(this)
            }
            return _emails
        }
}
val p = Person("Alice")
p.emails // Load emails for Alice --> 第一次访问会加载邮件
p.emails
```

这个代码有点啰唆，且这个实现不是线程安全的。Kotlin提代了更好的解决方案

```kotlin
class Person(val name: String) {
    val emails by lazy { loadEmails(this) }
}
```

`lazy`函数返回一个对象，该对象具有一个名为`getValue`且签名正确的方法，因此可以把它与`by`关键字一起使用来创建一个委托属性。`lazy`的参数是一个`lambda`，可以调用它来初始化这个值。默认情况下，`lazy`函数是线程安全的，如里需要，可以设置其他选项来告诉它要使用哪个锁，或者完全避开同步，如果该类永远不会在多线程环境中使用

#### 实例委托属性

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

如果你想能够截获一个赋值并做一些条件判断，可以使用`vetoable`代替`observable`

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

