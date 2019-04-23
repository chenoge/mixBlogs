---
title: java遍历
date: 2019-04-10 22:06:08
tags: [for,Collection,Iterator]
---

#### Collection对象-遍历器

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



#### for(:)循环

```java
String[] strings = { "A"， "B"， "C"， "D" };
Collection list = java.util.Arrays.asList(strings);
for (String str: list) { System.out.println(str);}
// for (String str: strings) { System.out.println(str);}
```

<!--more-->

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



#### 数组转`Set`或`List`

```java
// CollectionUtils.addAll()
import org.apache.commons.collections.CollectionUtils;   
String[] strArray = {"aaa", "bbb", "ccc"};
List strList = new ArrayList();
Set strSet = new HashSet();
CollectionUtils.addAll(strList, strArray);  
CollectionUtils.addAll(strSet, strArray);

// Arrays.asList()
import java.util.Arrays;
String[] strArray = {"aaa", "bbb", "ccc"};
List strList = Arrays.asList(strArray);
```

<br/>



#### Set和List转数组

```java
// Collection的toArray()方法
// Object[] toArray();
String[] strArray = {"aaa", "bbb", "ccc"};
List strList = Arrays.asList(strArray);

Object[] obj = strList.toArray();
for (int i = 0; i < obj.length; i++) {
    String str = (String) obj[i];
    System.out.println(str);
}
```





