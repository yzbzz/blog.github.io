---
title: Kotlin区间
date: 2018-03-08 16:46:47
tags: [android, kotlin]
---

### 区间

#### rangeTo

`..`操作符实际上调用的是`rangeTo`函数，一般与`in`和`in`联用 

```
start..end -> start.rangeTo(end)
```

整型区间（`IntRange`、 `LongRange`、 `CharRange`）可以迭代。 编译器会无额外开销的将它们转换为类似 Java 的基于索引的 *for*-循环

#### in(闭区间)

```kotlin
// 1..5 范围是闭区间 [1,5]
if (i in 1..5) ==> if (i >= 1 && i <=5)
if (i !in 1..5) ==> if (i <= 1 && i >=5)
```

#### downTo

```kotlin
// 正序
for (i in 1..5) 

// 倒序
for (i in 5..1) // 不支持这种写法，什么都不输出
==>
for (i in 5 downTo 1)
```

#### step

```kotlin
// 正序
for (i in 1..5 step 2) ==> for (i = 1; i<=5 ; i+=2)
//倒序
for (i in downTo 5 step 2) ==> for (i = 5;i >= 1;i-=2)
```

#### until(左闭右开区间)

```kotlin
// 1 until 5 范围是闭区间 [1,5)
for (i in 1 until 5) println(i)
// 1234
```

#### reversed

```kotlin
val list = listOf(1, 2, 3)
println(list.reversed()) // [3, 2, 1]
```

#### last

返回数列的 `last` 值可能与原始数列的 `last` 值不同，以便保持不变式 `(last - first) % step == 0` 成立

```kotlin
(1..12 step 2).last == 11  // 值为 [1, 3, 5, 7, 9, 11] 的数列
(1..12 step 3).last == 10  // 值为 [1, 4, 7, 10] 的数列
(1..12 step 4).last == 9   // 值为 [1, 5, 9] 的数列
```

