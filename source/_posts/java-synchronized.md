---
title: java-synchronized
date: 2019-08-06 21:21:57
tags: [java,synchronized]
---

#### synchronized

`synchronized`关键字最主要有以下3种应用方式：

- **修饰实例方法**，作用于当前实例加锁，进入同步代码前**要获得当前实例的锁**
- **修饰静态方法**，作用于当前类对象加锁，进入同步代码前**要获得当前类对象的锁**
- 修饰代码块，指定加锁对象，对给定对象加锁，进入同步代码库前**要获得给定对象的锁**

<!--more-->

<br/>



##### 作用于实例方法

```java
public class AccountingSync implements Runnable {
    // 共享资源(临界资源)
    static int i = 0;

    /**
     * synchronized 修饰实例方法
     */
    public synchronized void increase() {
        i++;
    }

    @Override
    public void run() {
        for (int j = 0; j < 1000000; j++) {
            increase();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        AccountingSync instance = new AccountingSync();
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i); // 2000000
    }
}
```

<br/>



##### 作用于静态方法

```java
public class AccountingSync implements Runnable {
    static int i = 0;

    /**
     * 作用于静态方法,锁是当前class对象,也就是
     * AccountingSyncClass类对应的class对象
     */
    public static synchronized void increase() {
        i++;
    }

    /**
     * 非静态,访问时锁不一样不会发生互斥
     */
    public synchronized void increaseByObj() {
        i++;
    }

    @Override
    public void run() {
        for (int j = 0; j < 1000000; j++) {
            // increaseByObj(); // 1292067
            increase(); // 2000000
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        //new新实例
        Thread t1 = new Thread(new AccountingSync());
        
        //new新实例
        Thread t2 = new Thread(new AccountingSync());
        
        //启动线程
        t1.start();
        t2.start();

        t1.join();
        t2.join();
        System.out.println(i); // 2000000
    }
}
```

<br/>



##### 同步代码块

```java
public class AccountingSync implements Runnable {
    static AccountingSync instance = new AccountingSync();
    static int i = 0;
    @Override
    public void run() {
        //省略其他耗时操作....
        //使用同步代码块对变量i进行同步操作,锁对象为instance
        synchronized(instance) {
            for (int j = 0; j < 1000000; j++) {
                i++;
            }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i); // 2000000
    }
}
```

