---
title: java-FileInputStream和FileOutputStream
date: 2019-06-24 10:19:00
tags: [java,FileInputStream,FileOutputStream]
---

#### FileInputStream

FileInputStream流被称为文件字节输入流，意思指对文件数据以字节的形式进行读取操作如读取图片视频等。

```java
// 打开与File类对象代表的实际文件的链接来创建FileInputStream流对象
public FileInputStream(File file) throws FileNotFoundException{}

// 指定的字符串参数来创建File类对象，
// 再与File对象所代表的实际路径建立链接创建FileInputStream流对象
public FileInputStream(String name) throws FileNotFoundException
```

注：若File类对象的所代表的文件不存在、不是文件是目录、或者其他原因不能打开的话，则会抛出异常。

<!--more-->

<br/>



#### FileInputStream.read

```java
// 从输入流中读取一个字节返回int型变量，若到达文件末尾，则返回-1
public int read() throws IOException

// 从输入流中读取b.length个字节到字节数组中，返回读入缓冲区的总字节数，若到达文件末尾，则返回-1
public int read(byte[] b) throws IOException
```

<br/>



#### FileOutputStream

 FileOutputStream流是指文件字节输出流，专用于输出原始字节流如图像数据等，其继承OutputStream类，拥有输出流的基本特性。

```java
// 创建FileOutputStream流以写入数据到File对象所代表的文件
public FileOutputStream(File file) throws FileNotFoundException{}

// 第二个参数为true，则字节将写入文件的末尾而不是开头
public FileOutputStream(File file,boolean append) throws FileNotFoundException{}

// 创建FileOutputStream流以写入数据到指定路径所代表的文件
public FileOutputStream(String name) throws FileNotFoundException{}
```

注：若文件存在，但是是目录而不是文件，则会抛出`FileNotFoundException`异常

<br/>



#### FileOutputStream.write

```java
// 将指定的一个字节写入文件的输出流中，所以是一次写入一个字节
public void write(int b) throws IOException

// 将指定字节数组中的b.length个字节写入到输出流中
public void write(byte[] b) throws IOException {}

// 将从偏移量off开始的指定字节数组中的len个字节写入输出流中
public void write(byte[] b,int off,int len) throws IOException{}
```

<br/>



#### 文件复制

```java
// 实现文件复制的方法
public void copyFile(String src, String dest) {
    // 1.提供读入、写出的文件
    File file1 = new File(src);
    File file2 = new File(dest);
    
    // 2.提供相应的流
    FileInputStream fis = null;
    FileOutputStream fos = null;
    
    try {
        fis = new FileInputStream(file1);
        fos = new FileOutputStream(file2);
        
        // 3.实现文件的复制
        byte[] b = new byte[1024];
        int len;
        while ((len = fis.read(b)) != -1) {
            fos.write(b, 0, len);
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if (fos != null) {
            try {
                fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (fis != null) {
            try {
                fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```

