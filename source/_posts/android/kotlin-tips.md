---
title: kotlin-tips
date: 2018-03-19 19:38:52
tags:
---

```kotlin
fun <T> test() : T {
    when(T){      // this won't work perhaps T::something? 
        is Int -> { ........ 
                   
==>
                   
inline fun <reified T> test() : T {
  when (T::class) {
    Int::class -> println("Int")
    else -> println("${T::class}")
```

