---
title: java反射
date: 2019-03-18 11:18:07
tags: [java,Reflection,反射]
---

#### 一、java 的反射机制

Java 反射机制在程序**运行时**，对于**任意一个类**，都能够知道这个类的所有属性和方法；对于**任意一个对象**，都能够调用它的任意一个方法和属性。这种 **动态的获取信息** 以及 **动态调用对象的方法** 的功能称为 **java 的反射机制**。

注：反射机制很重要的一点就是“运行时”，其使得我们可以在程序运行时加载、探索以及使用编译期间完全未知的 `.class` 文件。

<!--more-->

<br/>



#### 二、获取类的信息

```java
public class FatherClass {
    public String mFatherName;
    public int mFatherAge;

    public void printFatherMsg(){}
}
```

```java
public class SonClass extends FatherClass{

    private String mSonName;
    protected int mSonAge;
    public String mSonBirthday;

    public void printSonMsg(){
        System.out.println("Son Msg - name : "
                + mSonName + "; age : " + mSonAge);
    }

    private void setSonName(String name){
        mSonName = name;
    }

    private void setSonAge(int age){
        mSonAge = age;
    }

    private int getSonAge(){
        return mSonAge;
    }

    private String getSonName(){
        return mSonName;
    }
}
```

```java
private static void printFields(){
    //1.获取并输出类的名称
    Class mClass = SonClass.class;
    System.out.println("类的名称：" + mClass.getName());

    //2.1 获取所有 public 访问权限的变量
    // 包括本类声明的和从父类继承的
    Field[] fields = mClass.getFields();

    //2.2 获取所有本类声明的变量（不问访问权限）
    //Field[] fields = mClass.getDeclaredFields();

    //3. 遍历变量并输出变量信息
    for (Field field : fields) {
        
        //获取访问权限(修饰符)并输出
        int modifiers = field.getModifiers();
        System.out.print(Modifier.toString(modifiers) + " ");
        
        //输出变量的类型及变量名
        System.out.println(field.getType().getName()
                 + " " + field.getName());
    }
}
```
调用 `getFields()` 方法，输出 SonClass 类以及其所继承的父类
```
  类的名称：obj.SonClass
  public java.lang.String mSonBirthday
  public java.lang.String mFatherName
  public int mFatherAge
```
<br/>



调用` getDeclaredFields() `， 输出 SonClass 类的所有成员变量，不问访问权限

```
  类的名称：obj.SonClass
  private java.lang.String mSonName
  protected int mSonAge
  public java.lang.String mSonBirthday
```

<br/>

注：

- 获取变量信息：`getFields()` 、`getDeclaredFields()`、`getField()` 、`getDeclaredField()`
- 获取方法信息：`getMethods()`、`getDeclaredMethods()`、`getMethod()`、`getDeclaredMethod()`

<br/>



#### 三、私有变量和方法

```java
public class TestClass {

    private String MSG = "Original";

    private void privateMethod(String head , int tail){
        System.out.print(head + tail);
    }

    public String getMsg(){
        return MSG;
    }
}
```

```java
private static void getPrivateMethod() throws Exception{
    //1. 获取 Class 类实例
    TestClass testClass = new TestClass();
    Class mClass = testClass.getClass();

    //2. 获取私有方法
    //第一个参数为要获取的私有方法的名称
    //第二个为要获取方法的参数的类型，参数为 Class...，没有参数就是null
    //方法参数也可这么写 ：new Class[]{String.class , int.class}
    Method privateMethod =
            mClass.getDeclaredMethod("privateMethod", String.class, int.class);

    //3. 开始操作方法
    if (privateMethod != null) {
        //获取私有方法的访问权
        //只是获取访问权，并不是修改实际权限
        privateMethod.setAccessible(true);

        //使用 invoke 反射调用私有方法
        //privateMethod 是获取到的私有方法
        //testClass 要操作的对象
        //后面两个参数传实参
        privateMethod.invoke(testClass, "Java Reflect ", 666);
    }
}
```

```java
private static void modifyPrivateFiled() throws Exception {
    //1. 获取 Class 类实例
    TestClass testClass = new TestClass();
    Class mClass = testClass.getClass();

    //2. 获取私有变量
    Field privateField = mClass.getDeclaredField("MSG");

    //3. 操作私有变量
    if (privateField != null) {
        //获取私有变量的访问权
        privateField.setAccessible(true);

        //修改私有变量，并输出以测试
        System.out.println("Before Modify：MSG = " + testClass.getMsg());

        //调用 set(object , value) 修改变量的值
        //privateField 是获取到的私有变量
        //testClass 要操作的对象
        //"Modified" 为要修改成的值
        privateField.set(testClass, "Modified");
        System.out.println("After Modify：MSG = " + testClass.getMsg());
    }
}
```

注：

- `setAccessible(true)` 方法，是获取私有方法或变量的访问权限
- 用 `invoke()` 方法来调用方法

<br/>



#### 四、获得 Class 对象

##### 1、使用 Class 类的 `forName` 静态方法

```java
Class<?> cls = Class.forName("java.lang.String");
```

##### 2、直接获取某一个类的 class

```java
Class<?> klass = int.class;
```

##### 3、调用某个对象的 `getClass()` 方法

```java
StringBuilder str = new StringBuilder("123");
Class<?> klass = str.getClass();
```

注：

- `Class<T>`在实例化的时候，`T`要替换成具体类
- `Class<?>`它是个通配泛型，`?`可以代表任何类型

<br/>



#### 五、创建实例

- 使用Class对象的`newInstance()`方法来创建Class对象对应类的实例

  ```java
  Class<?> c = String.class;
  Object str = c.newInstance();
  ```

- 先通过Class对象获取指定的`Constructor`对象，再调用`Constructor`对象的`newInstance()`方法

  ```java
  //获取String所对应的Class对象
  Class<?> c = String.class;
  
  //获取String类带一个String参数的构造器
  Constructor constructor = c.getConstructor(String.class);
  
  //根据构造器创建实例
  Object obj = constructor.newInstance("23333");
  System.out.println(obj);
  ```

  注：`getConstructor()` 获取构造器对象

<br/>

