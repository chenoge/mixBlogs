---
title: java匿名内部类
date: 2019-03-29 16:57:37
tags: [java,内部类,匿名内部类]
---

#### 匿名内部类

```
new 父类构造器（参数列表）| 实现接口（）{  
    //匿名内部类的类体部分  
}
```

```java
button.addActionListener(new ActionListener() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("button clicked");
    }
});
```

<br/>

1. 使用匿名内部类时，我们**必须且只能**继承一个类或者实现一个接口
2. 匿名内部类中是**不能定义构造函数**的
3. 匿名内部类中**不能存在任何的静态成员变量和静态方法**
4. 匿名内部类为局部内部类，所以局部内部类的所有限制同样对匿名内部类生效
5. 匿名内部类不能是抽象的，它必须要**实现继承的类或者实现的接口的所有抽象方法**

##### 匿名内部类初始化

利用构造代码块能够达到为匿名内部类创建一个构造器的效果

```java
return new InnerClass() {
    int age_;
    String name_;
    //构造代码块完成初始化工作
    {
        if (0 < age && age < 200) {
            age_ = age;
            name_ = name;
        }
    }
    public String getName() {
        return name_;
    }

    public int getAge() {
        return age_;
    }
};
```

<!--more-->

注：内部类编译成功后，它会产生一个class文件，该class文件与外部类并不是同一class文件，仅仅只保留对外部类的引用。

<br/>





