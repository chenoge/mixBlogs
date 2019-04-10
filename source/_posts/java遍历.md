---
title: java遍历
date: 2019-04-10 22:06:08
tags: [for,Collection,Iterator]
---

#### 遍历数组的传统方式

```java
int[] integers = { 1， 2， 3， 4 };
for (int j = 0; j < integers.length; j++) {
    int i = integers[j];
    System.out.println(i);
}
```

<br/>



#### 遍历Collection对象的传统方式

```java
/* 建立一个Collection */
String[] strings = { "A"， "B"， "C"， "D" };
Collection stringList = java.util.Arrays.asList(strings);

for (Iterator itr = stringList.iterator(); itr.hasNext();) {
    Object str = itr.next();
    System.out.println(str);
}
```

<br/>



#### for(:)循环-foreach

```java
int[] integers = { 1， 2， 3， 4 };
for (int i: integers) {
    System.out.println(i);
}
```

```java
String[] strings = { "A"， "B"， "C"， "D" };
Collection list = java.util.Arrays.asList(strings);
for (Object str: list) {
    System.out.println(str);
}
```

<br/>



#### 遍历Map

```java
Map < Integer, Integer > map = new HashMap < Integer, Integer > ();

//遍历map中的键 
for (Integer key: map.keySet()) {
    System.out.println("Key = " + key);
}

//遍历map中的值 
for (Integer value: map.values()) {
    System.out.println("Value = " + value);
}
```

<br/>



