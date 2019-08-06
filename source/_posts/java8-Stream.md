---
title: java8-Stream
date: 2019-08-06 21:49:21
tags: [java8,Stream]
---

#### 流(Stream)

流是 Java8 中 API 的新成员，它允许你以**声明式**的方式**处理数据集合**。

<!--more-->

<br/>

- 只能遍历一次

  流只能遍历一次，**不允许遍历已消费的流**，否则会抛出一个异常，但**可以从原始数据重新获取一条新的流**

  ```java
  List<String> title = Arrays.asList("Is", "Java8", "In", "Action");
  Stream<String> s = title.stream();
  s.forEach(System.out::println);
  s.forEach(System.out::println); // 再次遍历，会报错
  ```

- ##### 支持并行处理

  只需要加上 `.parallel()` 

```java
public class StreamParallelDemo {

    /** 总数 */
    private static int total = 100000000;

    public static void main(String[] args) {
        long num = Runtime.getRuntime().availableProcessors();
        System.out.println(String.format("本计算机的核数：%d", num));

        // 产生1000w个随机数(1 ~ 100)，组成列表
        Random random = new Random();
        List < Integer > list = new ArrayList < > (total);

        for (int i = 0; i < total; i++) {
            list.add(random.nextInt(100));
        }

        long prevTime = getCurrentTime();
        list.stream().reduce((a, b) - > a + b).ifPresent(System.out::println);
        long time = getCurrentTime() - prevTime;
        System.out.println(String.format("单线程计算耗时：%d", time));

        // 只需要加上 .parallel() 就行了
        prevTime = getCurrentTime();
        list.stream().parallel().reduce((a, b) - > a + b).ifPresent(System.out::println);
        time = getCurrentTime() - prevTime;
        System.out.println(String.format("多线程计算耗时：%d", time));
    }
    
    private static long getCurrentTime() {
        return System.currentTimeMillis();
    }
}

// 本计算机的核数：8

// 655028378
// 单线程计算耗时：4159

// 655028378
// 多线程计算耗时：540
```

<br/>



#### 创建Stream

```java
List<Integer> list = new ArrayList<>();
Stream<Integer> stream = list.stream(); // 串行流
Stream<Integer> integerStream = list.parallelStream(); // 并行流
```

```java
Integer[] integers ={};
Stream<Integer> stream1 = Arrays.stream(integers);
```

```java
Stream<String> stream2 = Stream.of("aaa", "bbb");
```

<br/>



##### filter

Stream 接口支持 **filter** 方法，接受一个返回 boolean 的函数作为参数，并返回一个包含所有符合该条件的流

```java
List<String> words = Arrays.asList("wmyskxz", "say", "wow", "to", "everybody");
words.stream()
    .filter(word -> word.startsWith("w"))
    .collect(Collectors.toList());
```

<br/>



##### distinct

流还支持一个叫做 **distinct** 的方法，它会返回一个元素各异的流

```java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 2, 1, 3, 4);
numbers.stream()
       .filter(integer -> integer % 2 == 0)
       .distinct()
       .forEach(System.out::println);
// ==============输出：===============
// 2
// 4
```

<br/>



##### limit

流支持 **limit(n)** 方法，该方法会返回一个不超过给定长度的流，所需长度需要作为参数传递给 limit。如果流是有序的，则最多会返回前 n 个元素。

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
numbers.stream()
       .filter(integer -> integer % 2 == 0)
       .limit(3)
       .forEach(System.out::println);
// ==============输出：===============
// 2
// 4
// 6
```

<br/>



##### skip

流还支持 **skip(n)** 方法，返回一个扔掉了前 n 个元素的流。如果流中元素不足 n 个，则返回一个空流。**请注意 litmit 和 skip 是互补的**。

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
numbers.stream()
       .filter(integer -> integer % 2 == 0)
       .skip(2)
       .forEach(System.out::println);
// ==============输出：===============
// 6
// 8
```

<br/>



##### map

流支持 map 方法，**接受一个函数作为参数**。这个函数会被应用到每个元素身上，并将其映射成一个新的函数

```java
public static void main(String[] args) {
    List<Words> numbers = Arrays.asList(
        new Words("c++"),
        new Words("java"),
        new Words("golang")
    );
    numbers.stream()
        .map(Words::getContent)
        .map(String::length)
        .forEach(System.out::println);
}

@Data
@AllArgsConstructor
private static class Words {
    private String content;
}
// ==============输出：===============
// 3
// 4
// 6
```

<br/>



##### flatMap

流的扁平化

```java
List<String> words = Arrays.asList("Hello", "World");
words.stream()
     .map(s -> s.split(""))
     .flatMap(Arrays::stream)
     .distinct()
     .collect(Collectors.toList())
     .forEach(System.out::println);
// ==============输出：===============
// H
// e
// l
// o
// W
// r
// d
```

<br/>



##### 查找和匹配

Stream API 通过 `allMatch、anyMatch、noneMatch、findFirst 和 findAny `方法提供了查看数据集中的某些元素是否匹配一个给定的属性。

```java
// anyMatch
List<Integer> numbers = Arrays.asList(1, 2, 3);
if (numbers.stream().anyMatch(i -> i % 2 == 0)) {
    System.out.println("集合里有偶数!");
}
```

```java
// allMatch
List<Integer> numbers = Arrays.asList(2, 2, 4);
if (numbers.stream().allMatch(i -> i % 2 == 0)) {
    System.out.println("集合里全是偶数!");
}
```

```java
// findFirst
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
Optional<Integer> firstSquareDivisibledByThree =
        numbers.stream()
               .map(x -> x * x)
               .filter(x -> x % 3 == 0)
               .findFirst();
System.out.println(firstSquareDivisibledByThree.get());
```

<br/>



##### reduce

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
System.out.println(sum);
// ==============输出：===============
// 15
```

其中 reduce 接受了两个参数：

- 一个初始值，这里是 0
- 一个是 `BinaryOperator<T>` ，来用产生一个新值，这里我们用的是 `lambda (a, b) -> a + b`

<br/>