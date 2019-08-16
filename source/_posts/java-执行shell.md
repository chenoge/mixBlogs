---
title: java-执行shell
date: 2019-08-16 20:15:26
tags: [java,shell]
---

#### 执行shell

有时候我们在Linux中运行Java程序时，需要调用一些Shell命令和脚本。而`Runtime.getRuntime().exec()`方法给我们提供了这个功能，而且`Runtime.getRuntime()`给我们提供了以下几种exec()方法：

````java
// 在单独的进程中执行指定的字符串命令
Process exec(String command) 
 
// 在单独的进程中执行指定命令和变量
Process exec(String[] cmdarray) 

// 在指定环境的独立进程中执行指定命令和变量
Process exec(String[] cmdarray, String[] envp) 
 
// 在指定环境和工作目录的独立进程中执行指定的命令和变量
Process exec(String[] cmdarray, String[] envp, File dir)  

// 在指定环境的单独进程中执行指定的字符串命令
Process exec(String command, String[] envp)  
 
// 在有指定环境和工作目录的独立进程中执行指定的字符串命令
Process exec(String command, String[] envp, File dir)
````

- 如果参数中如果没有envp参数或设为null，表示调用命令将在当前程序执行的环境中执行

- 如果没有dir参数或设为null，表示调用命令将在当前程序执行的目录中执行

<!--more-->

<br/>



##### `Process exec(String command, String[] envp, File dir)`

```java
private void callCMD(String tarName, String fileName, String...workspace) {
    try {
        String cmd = "tar -cf" + tarName + " " + fileName;
        // String[] cmd = {"tar", "-cf", tarName, fileName};
        File dir = null;
        if (workspace[0] != null) {
            dir = new File(workspace[0]);
            System.out.println(workspace[0]);
        }
        process = Runtime.getRuntime().exec(cmd, null, dir);
        // process = Runtime.getRuntime().exec(cmd);
        int status = process.waitFor();
        if (status != 0) {
            System.err.println("Failed to call shell's command : " + status);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

注意：如果把命令放到一个`String[]`中时，必须把命令中每个部分作为一个元素存在`String[]`中，或者是**把命令按照空格符分割**得到的`String[]`。

```java
String[] cmd = {"tar", "-cf", tarName, fileName};		//right
String[] cmd = {"tar -cf", tarName, fileName};			//error
```

<br/>



##### 捕获调用输出信息

```java
BufferedReader input = new BufferedReader(
    new InputStreamReader(process.getInputStream())
);
```

<br/>