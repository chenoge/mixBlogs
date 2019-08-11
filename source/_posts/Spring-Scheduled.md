---
title: Spring-Scheduled
date: 2019-05-12 18:15:48
tags: [Spring,Scheduled]
---

#### @Scheduled

- fixedDelay：上一次**执行完毕时间点**之后多长时间再执行，单位毫秒

```java
@Scheduled(fixedDelay = 5000)
```



- fixedRate：上一次**开始执行时间点**之后多长时间再执行，单位毫秒

```java
@Scheduled(fixedRate = 5000)
```



- initialDelay：**第一次**延迟多长时间后再执行

```java
@Scheduled(initialDelay=1000, fixedRate=5000) 
```

<!--more-->



- cron：有6或7个域（`[秒] [分] [时] [日] [月] [周] [年]`），每一个域代表一个含义。`[年]`不是必须的域，可以省略`[年]` 

```
@Scheduled(cron="0 0 0 1 * ?")
```

| 说明 | 必填 | 值                  | 通配符              |
| :--: | :--: | :------------------ | :------------------ |
|  秒  |  是  | 0 - 59              | ,  -  *  /          |
|  分  |  是  | 0 - 59              | ,  -  *  /          |
|  时  |  是  | 0 - 23              | ,  -  *  /          |
|  日  |  是  | 1 - 31              | ,  -  *  ?  /  L  W |
|  月  |  是  | 1 - 12 or JAN - DEC | ,  -  *  /          |
|  周  |  是  | 1 - 7  or SUN - SAT | ,  -  *  ?  /  L  # |
|  年  |  否  | 1970 - 2099         | ,  -  *  /          |

###### 通配符说明:

-  `*` 表示所有值。如：在分的字段上设置 `*`，表示每一分钟都会触发
-  `?` 表示不指定值。如：在每月的10号触发一个操作，但不关心是周几： `0 0 0 10 * ?`
-  `-` 表示区间。如：在小时上设置 `10-12`，表示 `10,11,12`点都会触发
-  `,` 表示指定多个值。如：在周字段上设置 `MON,WED,FRI` 表示周一，周三和周五触发
-  `/` 用于递增触发。如：在秒上面设置`5/15` 表示从5秒开始，每增15秒触发(`5,20,35,50`)
-  `L` 表示最后的意思。如：在日字段设置上，表示当月的最后一天
-  `W` 表示离指定日期的最近那个工作日(`周一至周五`)，如：日字段上置`15W`
-  `#` 序号，表示每月的第几个周几。如：在周字段上设置`6#3`表示在每月的第三个周六

<br/>



#### 线程问题

`@Scheduled`默认为**单线程**，开启多个任务时，任务的执行时机会受上一个任务执行时间的影响。

```java
@Component
public class ScheduledTasks {
    private SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd  HH:mm:ss");

    @Scheduled(fixedDelay = 1000)
    public void first() throws InterruptedException {
        System.out.println("第一个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
        // 模拟第一个任务执行的时间
        Thread.sleep(1000 * 2); 
    }

    @Scheduled(fixedDelay = 1000)
    public void second() {
        System.out.println("第二个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
    }
}

// 第一个定时任务开始 : 2019-08-11 15:27:32
// 线程 : pool-1-thread-1

// 第二个定时任务开始 : 2019-08-11 15:27:34
// 线程 : pool-1-thread-1

// 第一个定时任务开始 : 2019-08-11 15:27:35
// 线程 : pool-1-thread-1

// 第二个定时任务开始 : 2019-08-11 15:27:37
// 线程 : pool-1-thread-1

// 默认单线程，所有任务（不同任务类的任务）都运行在同一个线程内
// 任务的执行时机受（其本身或其他任务）执行时间的限制
```

<br/>



##### 多线程

在**任务类**上加`@EnableAsync`注解，在**任务方法**上加`@Async`注解

```java
@Component
@EnableAsync
public class ScheduledTasks {
    private SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd  HH:mm:ss");

    @Async
    @Scheduled(fixedDelay = 1000)
    public void first() throws InterruptedException {
        System.out.println("第一个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
        // 模拟第一个任务执行的时间
        Thread.sleep(1000 * 2); 
    }
    
    @Async
    @Scheduled(fixedDelay = 1000)
    public void second() {
        System.out.println("第二个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
    }
}

// 第一个定时任务开始 : 2019-08-11 15:36:42
// 线程 : SimpleAsyncTaskExecutor-17

// 第二个定时任务开始 : 2019-08-11 15:36:42
// 线程 : SimpleAsyncTaskExecutor-18

// 第一个定时任务开始 : 2019-08-11 15:36:43
// 线程 : SimpleAsyncTaskExecutor-20

// 第二个定时任务开始 : 2019-08-11 15:36:43
// 线程 : SimpleAsyncTaskExecutor-21

// 开启了多线程，任务的执行时机不受（其本身或其他任务）执行时间的限制，需要注意数据幂等性。
```

<br/>



##### ScheduleConfig

```java
@Configuration
public class ScheduleConfig implements SchedulingConfigurer {

   @Autowired
   private Executor executor;

   private final ThreadFactory threadFactory = new BasicThreadFactory.Builder().namingPattern("epsm-scheduled-task-%d").build();

   @Override
   public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
       taskRegistrar.setScheduler(executor);
   }

   @Bean(destroyMethod = "shutdown")
   public Executor taskExecutor() {
       return Executors.newScheduledThreadPool(50, threadFactory);
   }
}
```

```java
@Component
@EnableAsync
public class ScheduledTasks {
    private SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd  HH:mm:ss");
    
    @Scheduled(fixedDelay = 1000)
    public void first() throws InterruptedException {
        System.out.println("第一个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
        // 模拟第一个任务执行的时间
        Thread.sleep(1000 * 2); 
    }
    
    @Async
    @Scheduled(fixedDelay = 1000)
    public void second() {
        System.out.println("第二个定时任务开始: " + sdf.format(new Date()));
        System.out.println( "线程: " + Thread.currentThread().getName());
        System.out.println();
    }
}

// 第一个定时任务开始: 2019-08-11 18:35:24
// 线程: pool-2-thread-12

// 第二个定时任务开始: 2019-08-11 18:35:24
// 线程: pool-2-thread-11
    
// 第二个定时任务开始: 2019-08-11 18:35:25
// 线程: pool-2-thread-11

// 第二个定时任务开始: 2019-08-11 18:35:26
// 线程: pool-2-thread-4

// 第一个定时任务开始: 2019-08-11 18:35:27
// 线程: pool-2-thread-15

// 不加@Async，任务的执行时机只受其本身执行时间的限制
// 加@Async，任务的执行时机不受（其本身或其他任务）执行时间的限制
```

