---
title: Kotlin详解集合与操作符
date: 2018-03-14 10:06:08
tags: [android, kotlin]
---

[TOC]

### 前言

kotlin的集合分为不可变集合和可变集合

Kotlin 的 `List<out T>` 只提供只读操作如 `size`、`get`等的接口。和 Java 类似，它继承自 `Collection<T>` 进而继承自 `Iterable<T>`。改变 list 的方法是由 `MutableList<T>` 加入的。这一模式同样适用于 `Set<out T>/MutableSet<T>` 及 `Map<K, out V>/MutableMap<K, V>`。详见下表

| 不可变     | 可变              |
| :--------- | ----------------- |
| Iterable   | MutableIterable   |
| Collection | MutableCollection |
| List       | MutableList       |
| Set        | MutableSet        |
| Map        | MutableMap        |

`List`和`Set`的基本用法

```kotlin
val numbers: MutableList<Int> = mutableListOf(1, 2, 3)
val readOnlyView: List<Int> = numbers
println(numbers)        // 输出 "[1, 2, 3]"
numbers.add(4)
println(readOnlyView)   // 输出 "[1, 2, 3, 4]"
readOnlyView.clear()    // -> 不能编译

val strings = hashSetOf("a", "b", "c", "c")
assert(strings.size == 3)
```

### 总数操作符

**any**

只要有一个元素符合判断条件，则返回`true`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.any { it % 2 == 0} // true
list.any { it > 10 } // false
```

**all**

只有全部的元素符合判断条件，则返回`true`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.all { it < 10 } // true
list.all { it % 2 == 0} // false
```

**count**

返回符合判断条件的元素总数

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.count { it % 2 == 0 } // 3
```

**fold**

给定一个初始值，在初始值的基础上，通过给定的函数来累计所有元素。

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.fold(4) { total, next -> total + next } // 4+1+2+3+4+5+6 = 25
list.fold(4) { total, next -> total * next } // 4*1*2*3*4*5*6 = 2880
```

**foldRight**

与`fold`一样，只是顺序是从最后一项到第一项

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.foldRight(4) { total, next -> total + next } // 25
```

**forEach**

遍历的所有的元素，并执行给定的操作

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.forEach { println(it) }
```

**forEachIndexed**

与`forEach`类似，但是我们同时可以得到元素的`index`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.forEachIndexed { (index, value) -> println("position $index contains a $value") }
```

**max/min**

返回最大/最小的一项，如果没有则返回`null`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.max() // 6
list.min() // 1
```

**maxBy/minBy**

根据给定的函数返回最大/最小的一项，如果没有则返回`null`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.maxBy { -it } // 1
list.minBy { -it } // 6
```

**none**

如果没有任何元素与给定的函数匹配，则返回`true`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.none { it % 7 == 0 } // true
```

**reduce**

和`fold`一样，但是没有一个初始值。通过给定的函数来累计所有元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.reduce { total, next -> total + next } // 21
```

**reduceRight**

和`reduce`一样，但是顺序是从最后一项到第一项

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.reduceRight { total, next -> total + next } // 21
```

**sum**

返回所有元素的和

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.sum() // 21
```

**sumBy**

返回所有元素通过给定的函数转换之后的数据总和

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.sumBy { it % 2 } // 3
list.sumBy { it + 1 } // 27
```

### 过滤操作符

**drop**

返回包括去掉前`n`个元素的所有元素的列表

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.drop(4) // listOf(5, 6)
```

**dropWhile**

这个函数有点难理解，我们需要结合一下源码来看：

```kotlin
public inline fun <T> Iterable<T>.dropWhile(predicate: (T) -> Boolean): List<T> {
    var yielding = false
    val list = ArrayList<T>()
    for (item in this)
        if (yielding)
            list.add(item)
        else if (!predicate(item)) {
            list.add(item)
            yielding = true
        }
    return list
}
```
调用`dropWhile`时，先遍历集合，如果集合中的元素不符合`predicate`函数的规则，则从当前元素开始，所有元素依次添加到新集合`list`并返回，如果集合中的元素都符合规则，则一个都不添加。我们以下面的例子来分析：

```kotlin
val list1 = listOf(1, 2, 3, 4, 5, 6)
list1.dropWhile { it < 3 } // listOf(3, 4, 5, 6)
```

在上面的例子中，我们定义的函数规则是当前元素要小于3`{it < 3}`，当集合遍历到`3`时，因为3是不小于3的，不符合函数的规则，所以从`3`开始，往后的元素都被添加到了新集合中。

再来看一个例子，我们把集合中的`6`放到最前面

```kotlin
val list2 = listOf(6, 1, 2, 3, 4, 5)
list2.dropWhile { it < 3} // listOf(6, 1, 2, 3, 4, 5)
```

遍历集合时，因为第一个元素是`6`，不符合`{it < 3}`，所要`6`及之后的元素都被添加到了新集合中

**dropLastWhile**

返回根据给定函数从最后一项开始去掉指定元素的列表

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.dropLastWhile { it > 4 } // listOf(1, 2, 3, 4)
```

**filter**

返回符合给定条件的元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.filter { it % 2 == 0 } // listOf(2, 4, 6)
list.filter { it < 3 } // listOf(1, 2)
```

**filterNot**

返回所有不符合给定函数条件的元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.filterNot { it % 2 == 0} // listOf(1, 3, 5)
```

**filterNotNull**

返回集合中非`null`的函数

```kotlin
val list = listOf(1, 2, 3, 4, 5, null)
println(list.filterNotNull()) // listOf[1, 2, 3, 4, 5]
```

**slice**

返回指定区域的元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.slice(0..3) // listOf(1, 2, 3, 4)
list.slice(listOf(1, 3, 4)) // listOf(2, 4, 5)
```

> `(0..3)`：表示取出第0个到第3个之间的元素
>
> `listOf(1, 3, 4)`：表示取出下标为1、3、4的元素

**take**

返回从第一个开始的n个元素(返回前n个元素)

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.take(2) // listOf[1, 2]
```

**takeLast**

返回从最后一个开始的n个元素(返回后n个元素)

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.take(2) // listOf[5, 6]
```

**takeWhile**

返回从第一个开始符合给定函数条件的元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.takeWhile { it < 3 } // listOf[1, 2]
```

### 映射操作符

**flatMap**

遍历所有的元素，为每个元素创建一个集合，最后把所有的集合放在一个集合中。

什么意思呢，就是你可以把每一个元素转化成一个新的`list`，最后这些`list`会合并成一个大的`list`。我们通过代码来分析：

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.flatMap { listOf('a' + it) } // listOf(b, c, d, e, f, g)
```

上面的例子中，我们通过函数`listOf('a'+ it)`把每一个元素都转换成了新的list。因为我们有6个元素，所以会生成6个list`(listOf(b)、listOf(c)、listOf(d)、listOf(e)、listOf(f)和listOf(g))`，最后会遍历这6个`list`，把这6个`list`中的每一项添加到一个新的`list`中。当然你也可以为`listOf('a' + it)`添加多个元素，看下面的例子：

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.flatMap { listOf(it, 'a' + it) } // listOf(1, b, 2, c, 3, d, 4, e, 5, f, 6, g)
```

**groupBy**

返回一个根据给定函数分组后的map，把函数的返回值作为`key`，符合条件的所有元素作为`value`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
// 返回为 mapOf("odd" to listOf(1, 3, 5), "even" to listOf(2, 4, 6)}
list.groupBy { if (it % 2 ==0) "even" else "odd" } 
```

**map**

返回一个每一个元素根据给定的函数转换所组成的List(把元素列表中的每一项进行相应计算或转成其它你想要的任何格式)。

把元素中的每一项乘2：

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.map { it * 2 } // listOf(2, 4, 6, 8, 10, 12)
```

把元素中的每一项转成字母：

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.map { 'a' + it } // listOf(b, c, d, e, f, g)
```

**mapIndexed**

和`map`一样，但是我们可以同时得到元素的`index`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.mapIndexed { index, it -> index + it } // listOf(1, 3, 5, 7, 9, 11)
```

**mapNotNull**

和`map`一样，但是会过滤掉为`null`的元素

```kotlin
class Person(val name: String? = null)
val list = listOf(Person(), Person("Alice"))
// map
list.map { it.name } // listOf(null, "Alice")
// mapNotNull
list.mapNotNull { it.name } // listOf("Alice")
```

### 元素操作符

**contains**

如果的元素可以在集合中找到，则返回true

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.contains(2) // true
```

**elementAt**

返回给定index对应的元素，如果index数组越界则会抛出`IndexOutOfBoundsException`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.elementAt(1) // 2
```

**elementAtOrElse**

返回给定index对应的元素，如果index数组越界则会返回给定函数的返回值，函数中的参数为给定的index。如下图所示：`it`为传入的10

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.elementAtOrElse(10, { 2 * it }) // 20
```

**elementAtOrNull**

返回给定index对应的元素，如果index数组越界则会返回null

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.elementAtOrNull(10) // null
```

**first**

返回符合给定函数条件的第一个元素，如果没有符合抛出`java.util.NoSuchElementException`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.first() // 1
list.first { it % 2 == 0 } // 2
```

**firstOrNull**

返回符合给定函数条件的第一个元素，如果没有符合则返回null

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.firstOrNull() // 1
list.firstOrNull { it % 7 == 0 }) // null
```

**indexOf**

返回指定元素的第一个index，如果不存在，则返回`-1`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.indexOf(4) // 3
```

**indexOfFirst**

返回第一个符合给定函数条件的元素的index，如果没有符合则返回`-1`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.indexOfFirst { it % 2 == 0 } // 1
```

**indexOfLast**

返回最后一个符合给定函数条件的元素的index，如果没有符合则返回`-1`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.indexOfLast { it % 2 == 0 } // 5
```

**last**

返回符合给定函数条件的最后一个元素

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.last { it % 2 == 0 } // 6
```

**lastIndexOf**

返回的指定元素的最后一个index，如果不存在，则返回`-1`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.lastIndexOf(2) // 1
list.lastIndexOf(7) // -1
```

**single/singleOrNull**

返回符合给定函数的单个元素，如果没有符合或者超过一个，`single`抛出异常，`singleOrNull`返回`null`

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.single { it % 5 == 0 } // 5
list.single { it % 2 == 0 } // java.lang.IllegalArgumentException
list.singleOrNull { it % 2 == 0 } // null
```

### 生产操作符

**partition**

把一个集合通过给定的函数分为两个集合，符合条件的作为一个，不符合的作为另一个

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.partition { it % 2 == 0 } // Pair(listOf(2, 4, 6), listOf(1, 3, 5))
```

**plus**

使用`+`号操作符把两个集合合并到一个集合中

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list + listOf(7, 8 ) // listOf(1, 2, 3, 4, 5, 6, 7, 8)
```

**zip(返回值 List<Pair<T, R>>)**

返回由`Pair`组成的List，每个`Pair`由两个集合中相同index的元素组成。这个返回的List的大小是由最小的那个集合决定

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.zip(listOf(7, 8)) // listOf(Pair(1, 7), Pair(2, 8))
```

**unzip**

从包含`Pair`的List中生成包含List的`Pair` 。有点拗口，看代码

```kotlin
val list = listOf(Pair(5, 7), Pair(6, 8))
list.unzip() // Pair(listOf(5, 6), listOf(7, 8))
```

看上面的代码，还是有点难理解，我们需要结合源码来看

```kotlin
public fun <T, R> Iterable<Pair<T, R>>.unzip(): Pair<List<T>, List<R>> {
    val expectedSize = collectionSizeOrDefault(10)
    val listT = ArrayList<T>(expectedSize)
    val listR = ArrayList<R>(expectedSize)
    for (pair in this) {
        listT.add(pair.first)
        listR.add(pair.second)
    }
    return listT to listR
}
```

`unzip`方法中，会定义两个集合，一个集合会添加`list`元素`Pair(5,7), Pair(6,8)`的`first`，出就是`5、6`，另一个集合会添加`second`，也就是`7、8`

### 顺序操作符

**reversed**

返回一个倒序的list

```kotlin
val list = listOf(1, 2, 3, 4, 5, 6)
list.reversed() // listOf(6, 5, 4, 3, 2, 1)
```

**sorted**

把list进行生序排列

```kotlin
val list = listOf(1, 3, 2, 5, 4, 6)
list.sorted() // listOf(1, 2, 3, 4, 5, 6)
```

**sortedBy**

把不符合条件的元素按照在集合中之前的顺序放在集合的最前面，把符合条件的元素依次放在后面

```kotlin
val list = listOf(7, 6, 3, 2, 5, 4, 1)
list.sortedBy { it > 2 } // listOf(2, 1, 7, 6, 3, 5, 4)
```

看代码，因为`2,1`不符合条件，所以把`2,1`按之前的顺序放到最前面，`7,6,3,5,4`仿照之前的顺序依次放到后面

**sortedDescending**

把list进行降序排列

```kotlin
val list = listOf(7, 4, 3, 1, 5, 6, 2)
list.sortedDescending() // listOf(7, 6, 5, 4, 3, 2, 1)
```

**sortedByDescending**

和`sortedBy`类似，只不过是把符合条件的放在集合的前面，不符合的放在集合的后面

```kotlin
val list = listOf(7, 4, 3, 1, 5, 6, 2)
list.sortedByDescending { it > 3 } // listOf(7, 4, 5, 6, 3, 1, 2)
```

> 参考: 《kotlin-for-android-developers》《kotlin in action》

