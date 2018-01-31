---
title: Stream
date: 2016-05-19 14:52:26
tags: [java,stream]
---

# 概述

本篇会介绍Steam类中的一组方法，每个方法都对应集合上的一种操作

## 迭代

定义一个集合

```
List<String> numbers = Arrays.asList("1", "2", "3");
```

外部迭代

```java
// 使用for循环
for (String number : numbers) {
    System.out.println(number);
}
// 迭代器
Iterator<String> iterator = numbers.iterator();
while (iterator.hasNext()){
    String number = iterator.next();
    System.out.println(number);
}
```

内部迭代（Stream）

```java
numbers.stream().forEach(action -> System.out.println(action));
```

<!-- More -->

## 实现机制

> Stream操作分为==惰性求值方法==和==及早求值方法==

看下面一个例子

```java
numbers.stream().filter(predicate -> predicate.equals("2")); //返回值Stream<String>
numbers.stream().filter(predicate -> predicate.equals("2")).count(); //返回值int
```

对于第1种，这行代码并未做什么实际性的工作，filter只刻画出了Stream，但没有产生新的集合。像filter这样只描述Stream，最终不产生新集合的方法叫作==惰性求值方法==；对于第2种的count这样最终会从Stream产生值的方法叫作==及早求值方法==

```java
// 由于使用了惰性求值，没有输出数字
Stream<String> stream = numbers.stream().filter(predicate -> {
    System.out.println(predicate);
    return predicate.equals("2");
});
// 调用Count，输出了数字
stream.count(); // 输出了1、2、3
```

> 判断一个操作是惰性求值还是及早求值很简单：只需看它的返回值。如果返回值是Stream，那么是惰性求值；如果返回值是另一个值或为空，那么就是及早求值。整个过程和==建造者模式==有共通之处。建造者模式使用一系列操作设置属性和配置，最后调用一个==build==方法，这时，对象才被真正的创建。

## 常用的流操作

### collect(toList())

```java
List<String> collected = Stream.of("1", "2", "3").collect(Collectors.toList());
```

### map

map操作符用于将一个流中的值转换成一个新的流

```java
// 先调用Stream的of方法生成一个Stream流，再调用map方法把Stream流中的(a,b,c)转成大写
List<String> collected = Stream.of("a", "b", "c")
  .map(string -> string.toUpperCase()).collect(Collectors.toList());

// 等价于
List<String> collected = new ArrayList<String>();
for (String string : Arrays.asList("a", "b", "c")) {
    collected.add(string.toUpperCase());
}
```

### filter

遍历数据并检查其中的元素时，可尝试使用Stream中的filter操作符

```java
// 下面代码的意思是从Stream流中取大于3的元素
List<Integer> collected = Stream.of(1, 2, 3, 4, 5, 6)
  .filter(predicate -> (predicate > 3)).collect(Collectors.toList());
```

### flatmap

flatmap方法可用Stream替换值，然后将多个Stream连接成一个Stream

```java
// 把2个list合并成一个
List<Integer> together = Stream.of(Arrays.asList(1, 2), Arrays.asList(3, 4))
  .flatMap(mapper -> mapper.stream()).collect(Collectors.toList());
System.out.println(together); // 输出[1,2,3,4]

// 先计算2个list的个数，再把2个list的个数合并成一个list
List<Integer> together = Stream.of(Arrays.asList(1, 2), Arrays.asList(3, 4, 5))
  .flatMap(mapper -> Stream.of(mapper.size())).collect(Collectors.toList());
System.out.println(together); // 输出[2,3]
```

### max和min

Stream上常用的操作之一是求最大值和最小值。Stream API中的max和min操作足以解决这一问题。

定义一个Track类和集合：

```java
public class Track {
  
	public String name;
	public int count;

	public Track(String name, int count) {
		this.name = name;
		this.count = count;
	}

	public int getLength() {
		return name.length();
	}
}
```

```java
List<Track> tracks = Arrays.asList(
				new Track("Bakai", 524), 
				new Track("Violets for Your Furs", 378),
				new Track("Time",451));
```

求Track

```java
// 求name最小的Track
Track shortestTrack = tracks.stream()
.min(Comparator.comparing(track -> track.getLength())).get();
// 求name最大的Track
Track shortestTrack = tracks.stream()
  .max(Comparator.comparing(track -> track.getLength())).get();
```

> 调用max和min，会返回一个Optional对象。Optional它代表一个可能存在也可能不存在的值。如果Stream为空，那么该值不存在，如果不为空，则该值存在。调用get方法可以取出Optional对象中的值

### reduce

reduce操作可以实现从一组值中生成一个值

```java
// 使用reduce求和
int count = Stream.of(1, 2, 3, 4, 5).reduce(0, (acc, element) -> acc + element);
System.out.println(count); // 返回15 1+2+3+4+5
```

> Lambda表达式的返回值是最新的acc，是上一轮acc的值和当前元素相加的结果。reduce的类型是之前Lambda文章介绍过的BinaryOperator。[点此查看Lambda](http://yzbzz.github.io/2016/05/19/java8-lambda/)

### 整合操作

下面的例子演示了找出一年级的所有同学

```java
public class Classes {

	public String name;
	public List<Student> students = new ArrayList<Classes.Student>();

	public static class Student {
		public String name;

		public Student(String name) {
			this.name = name;
		}
		
		@Override
		public String toString() {
			return "name: " + name;
		}
	}

	public static void main(String[] args) {

		Classes classesOne = new Classes();
		classesOne.name = "一年级";
		classesOne.students.addAll(
				Arrays.asList(new Student("张三"), new Student("李四")));

		Classes classesTwo = new Classes();
		classesTwo.name = "二年级";
		classesTwo.students.addAll(
				Arrays.asList(new Student("王五"), new Student("赵六")));

		List<Classes> classes = new ArrayList<Classes>();
		classes.add(classesOne);
		classes.add(classesTwo);
		
		List<Student> students = classes.stream().
				filter(predicate -> predicate.name.equalsIgnoreCase("一年级")).
				flatMap(mapper -> mapper.students.stream()).
				collect(Collectors.toList());
		for (Student student : students) {
			System.out.println(student.toString());
		}
	}
}
```

计算学生名字长度

```java
IntSummaryStatistics intSummaryStatistics =classesTwo.students.stream()
				.mapToInt(mapper -> mapper.name.length())
				.summaryStatistics();
System.out.println(intSummaryStatistics.getAverage()); // 2.0
System.out.println(intSummaryStatistics.getCount()); // 2
System.out.println(intSummaryStatistics.getMax()); // 2
System.out.println(intSummaryStatistics.getMin()); // 2
System.out.println(intSummaryStatistics.getSum()); // 4
```

