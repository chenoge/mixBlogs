---
title: java注解
date: 2019-03-18 10:03:39
tags: [java,Annotation,注解]
---

#### 一、认识注解

`JDK1.5`之后内部提供的三个注解

- `@Deprecated` 意思是**废弃的，过时的**
- `@Override` 意思是**重写、覆盖**
- `@SuppressWarnings` 意思是“**压缩警告**

```java

public class AnnotationTest {
    // 这里就是注解，称为压缩警告，这是JDK内部自带的一个注解
    // 一个注解就是一个类，在这里使用了这个注解就是创建了SuppressWarnings类的一个实例对象
    @SuppressWarnings(":deprecation")
    public static void main(String[] args) {
        System.runFinalizersOnExit(true);
        //这里的runFinalizersOnExit()方法画了一条横线表示此方法已经过时了，不建议使用了
    }

	//这也是JDK内部自带的一个注解，意思就是说这个方法已经废弃了，不建议使用了
    @Deprecated 
    public static void sayHello(){
        System.out.println("hello world");
    }

	//这也是JDK1.5之后内部提供的一个注解，意思就是要重写(覆盖)JDK内部的toString()方法
    @Override
    public String toString(){
        return "hello world";
    }
}
```

注：注解就相当于一个你的源程序要调用一个类，在源程序中应用某个注解，得事先准备好这个注解类

<!--more-->

<br/>



#### 二、自定义注解及其应用

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

/**
 * 这是一个自定义的注解(Annotation)类，在定义注解(Annotation)类时使用了另一个注解类Retention
 * 在注解类上使用另一个注解类，那么被使用的注解类就称为元注解
 */

@Retention(RetentionPolicy.RUNTIME)
//Retention注解决定MyAnnotation注解的生命周期

@Target({ ElementType.METHOD, ElementType.TYPE })
//Target注解决定MyAnnotation注解可以加在哪些成分上，如加在类、属性、方法身上等成分

/*
 * @Retention(RetentionPolicy.SOURCE)
 * 这个注解的意思是让MyAnnotation注解只在java源文件中存在，编译成.class文件后注解就不存在了
 
 * @Retention(RetentionPolicy.CLASS)
 * 这个注解的意思是让MyAnnotation注解在java源文件(.java文件)中存在，
 * 编译成.class文件后注解也还存在，被MyAnnotation注解类标识的类被类加载器加载到内存中后
 * MyAnnotation注解就不存在了
 */

/*
 * 这里是在注解类MyAnnotation上使用另一个注解类，这里的Retention称为元注解。
 * Retention注解括号中的"RetentionPolicy.RUNTIME"意思是让MyAnnotation这个注解的
 * 生命周期一直程序运行时都存在
 */
public @interface MyAnnotation {}
```

##### 把自定义的注解加到某个类上：

```java
@ MyAnnotation 
public class AnnotationUse{}
```

<br/>



##### 用`反射`测试进行测试`AnnotationUse`的定义上是否有`@MyAnnotation`

```java
// 这里是将新创建好的注解类MyAnnotation标记到AnnotaionTest类上
@MyAnnotation

public class AnnotationUse {
    public static void main(String[] args) {
        // 这里是检查Annotation类是否有注解，这里需要使用反射才能完成对Annotation类的检查
        if (AnnotationUse.class.isAnnotationPresent(MyAnnotation.class)) {
            
            /*
             * MyAnnotation是一个类，这个类的实例对象annotation是通过反射得到的
             * 一旦在某个类上使用了@MyAnnotation，那么
             * 这个MyAnnotation类的实例对象annotation就会被创建出来了
             */
            MyAnnotation annotation = (MyAnnotation) AnnotationUse.class
                .getAnnotation(MyAnnotation.class);
            
            // 打印MyAnnotation对象，这里输出的结果为：@cn.itcast.day2.MyAnnotation()
            System.out.println(annotation);
            
        }
    }
}
```

<br/>



#### 三、元注解（meta-annotation）

`Java5.0`定义了4个标准的`meta-annotation`类型，它们被用来提供对其它 annotation类型作说明。

- `@Target`
- `@Retention`
- `@Documented`
- `@Inherited`

<br/>

##### `1、@Target`

`@Target`说明了Annotation所修饰的对象范围（**被描述的注解可以用在什么地方**）

- `CONSTRUCTOR`:用于描述构造器
- `FIELD`:用于描述域
- `LOCAL_VARIABLE`:用于描述局部变量
- `METHOD`:用于描述方法
- `PACKAGE`:用于描述包
- `PARAMETER`:用于描述参数
- `TYPE`:用于描述类、接口(包括注解类型) 或enum声明

```java
@Target( { ElementType.METHOD, ElementType.TYPE })
// @Target(ElementType.FIELD)
```

<br/>

##### `2、@Retention`

`@Retention`定义了该Annotation被保留的时间长短。

- `SOURCE`:在源文件中有效（即源文件保留）
- `CLASS`:在class文件中有效（即class保留）
- `RUNTIME`:在运行时有效（即运行时保留）

```java\
@Retention(RetentionPolicy.RUNTIME)
```

##### 2.1 声明周期

**一个注解的生命周期有三个阶段：java源文件是一个阶段，class文件是一个阶段，内存中的字节码是一个阶段**。javac把`源文件`编译成`.class文件`时，有可能去掉里面的注解，类加载器把`.class文件`加载到内存时也有可能去掉里面的注解。

<br/>

##### `2.2 Java API`中是这样定义的`@Deprecated`的

```java
@Documented
@Retention(value=RUNTIME)
public @interface Deprecated
```

<br/>

##### `2.3 Java API中`是这样定义的`@Override`的

```java
@Target(value=METHOD)
@Retention(value=SOURCE)
public @interface Override
```

`@Override`是给`javac`看的，编译完以后就`@Override`注解就没有价值了

<br/>

##### `2.4 Java API`中是这样定义的`@SuppressWarnings`的

```
@Target(value={TYPE,FIELD,METHOD,PARAMETER,CONSTRUCTOR,LOCAL_VARIABLE})
@Retention(value=SOURCE)
public @interface SuppressWarnings
```

`@SuppressWarnings`也是给`javac`看的

<br/>

##### `3、@Documented`

`@Documented`用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如`javadoc`此类的工具文档化。Documented是一个标记注解，没有成员。

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Column {
    public String name() default "fieldName";
    public String setFuncName() default "setField";
    public String getFuncName() default "getField"; 
    public boolean defaultDBValue() default false;
}
```

<br/>

##### `4、@Inherited`

`@Inherited` 元注解是一个标记注解，`@Inherited`阐述了某个被标注的类型是被继承的。如果一个使用了`@Inherited`修饰的annotation类型被用于一个class，则这个annotation将**被用于该class的子类**。

<br/>



#### 四、为注解增加属性

注解可以看成是一种特殊的类，既然是类，那自然可以为类添加属性

##### **1、添加属性**

语法：`类型 属性名() default 默认值;` || `类型 属性名();` 

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
// Retention注解决定MyAnnotation注解的生命周期

@Target({ ElementType.METHOD, ElementType.TYPE })
public @interface MyAnnotation {
    // String color();
    String color() default "blue"; // 为属性指定缺省值
    String value(); // 定义一个名称为value的属性
}
```

<br/>

##### **2、应用属性**

```java
@MyAnnotation(color = "red") //应用MyAnnotation注解的color属性
public class MyAnnotationTest {
    public static void main(String[] args) {
        // 用反射方式获得注解对应的实例对象后，在通过该对象调用属性对应的方法
        MyAnnotation annotation = (MyAnnotation) MyAnnotationTest.class
            .getAnnotation(MyAnnotation.class);
        System.out.println(annotation.color()); //输出red
    }
}
```

<br/>

##### **3、value属性**

如果一个注解中有一个名称为`value`的属性，且你只想设置value属性，那么可以省略掉`“value=”`部分

```java
@SuppressWarnings("deprecation")
@MyAnnotation("hello world")
```

<br/>

