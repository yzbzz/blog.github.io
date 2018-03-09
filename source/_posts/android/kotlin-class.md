---
title: Kotlin类与对象
date: 2018-03-02 11:12:56
tags: [android, kotlin]
---

### 声明和初始化类

#### 类的定义

```kotlin
class User constructor (_nickname: String) {
    val nickname: String
    init {
        nickname = _nickname
    }
}
```

> 和`java`不同，在`java`中，初始化代码是写在构造方法里面的，而`kotlin`是写在`init`方法里。
>

在上面的例子中，不需要把初始化代码放在初始化语句块中，因为它可以与`nickname`属性的声明结合。如果主构造方法没有注解或可见性修饰符，同样可以去掉`constructor`关键字。

```kotlin
class User (_nickname: String) {
    val nickname = _nickname
}
```

前两个例子在类体中使用`val`关键字声明了属性。如果属性用相应的构造方法参数来初始化，代码可以通过把`val`关键字加在参数前的方式来进行简化。

```kotlin
class User (val _nickname: String)
```

我们也可以为构造方法参数声明一个默认值

```kotlin
class User (val _nickname: String, val isSubscribed: Boolean = true)
```

> 如果所有的构造方法都有默认值，编译器会生成一个额外的不带参数的构造方法来使用所有的默认值。

如果你的类具有一个父类，主构造方法同样需要初始化父类。可以通过在基类列表的父类引用中提供父类构造方法参数的方式来做到这一点。

```kotlin
open class User(val nickName: Stirng) { ...... }
class TwitterUser(nickName: String): User(nickName) { ...... }
```

#### 次构造函数

```kotlin
open class View {
    constructor (ctx: Context) {}
    constructor (ctx: Context, attr: AttributeSet) {}
    constructor (ctx: context, attr: AttributeSet, defStyle: Int): this(ctx, attr) {}
}
class MyButton: View {
    constructor (ctx: Context): super(ctx) {}
    constructor(ctx: Context, attr: AttributeSet): super(ctx, attr) {}
}
```

#### 类的实例

要创建一个类的实例，只需要直接调用构造方法，不需要使用`new`关键字：

```kotlin
val alice = User("Alice")
val bob = User("Bob", false)
```

#### 匿名对象

使用`object`关键字创建匿名对象

```kotlin
button.setOnClickListener(object : View.OnClickListener {
    override fun onClick(v: View?) {
    }
})
```
创建单独的类(不继承，不实现接口)

```kotlin
val pair = object {
    var first = 1
    var second = "a"
}
println("${pair.first} ${pair.second}") // 1 a
```

注意：匿名对象使用`public`修改符，那么匿名对象赋值的函数或属性的实际类型会是匿名对象声明的超类型，如果没声明超类型，会是`Any`。如下图，因为属性`pair`是用`public`，所以它的类型是`Any`，这样就访问不到里面的`first`和`second`

```kotlin
class Pair() {
	private fun getPair() = object {
		var first = 1
		var second = "a"
	}

	public val pair = object {
		val first = 1
		var second = "a"
	}

	fun get() {
		getPair().first
		// pair.first 这里会报错，因为属性pair是用public修饰的
	}
}
```

### 静态成员与方法

kotlin没有`static`关键字，需要使用`object`来代替

java

```kotlin
class User {
    static String TAG = ""; 
    static void getName() {}
    void getAge() {}
}
// User.TAG
// User.getName()
// new User().getAge()
```

kotlin

```kotlin
class User {    
    companion object {
        val TAG = ""    
    	fun getName() {}
    }
    fun getAge() {}
}
// User.TAG
// User.getName()
// User().getAge()
```

使用`companion object`包裹的代码，都是静态的，`java`代码调用时，需要加`companion`关键字

```java
// java
User.companion.TAG
```

还有一种，使用`object` 关键修饰类，这样类里的所有成员和方法都是静态的

```kotlin
object User {
    val TAG = ""
    fun getName() {}
}
// User.TAG
// User.getName()
// User.getAge()
```

#### 使用`companion object`实现单例模式

```kotlin
class SDKManager private constructor() {

    private object SingletonHolder {
        val instance = SDKManager()
    }

    companion object {
        val instance = SingletonHolder.instance
        // or
        fun get() = SingletonHolder.instance
    }
}

SDKManager.instance
SDKManager.get()
```

### 内部类和嵌套类：默认是嵌套类

**嵌套类不持有外部类的引用，而内部类持有**

|    类A在别一个类B中声明    |    在Java中    |  在Kotlin中   |
| :------------------------: | :------------: | :-----------: |
| 嵌套类(不存储外部类的引用) | static class A |    class A    |
|  内部类(存储外部类的引用)  |    class A     | inner class A |

**kotlin引用外部类实例**

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference() = this@Outer
    }
}
```
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

