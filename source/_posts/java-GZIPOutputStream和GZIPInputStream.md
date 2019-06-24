---
title: java-GZIPOutputStream和GZIPInputStream
date: 2019-06-24 10:55:41
tags: [java,GZIPOutputStream,GZIPInputStream]
---

#### GZIPOutputStream类：压缩

```java
public GZIPOutputStream(OutputStream out) throws IOException {}
```

GZIPOutputStream只有一个方法用于压缩，就是带定长的write方法

```java
public static void compress(InputStream is, OutputStream os) throws Exception {

    GZIPOutputStream gos = new GZIPOutputStream(os);

    int count;
    byte data[] = new byte[BUFFER];
    while ((count = is.read(data, 0, BUFFER)) != -1) {
        gos.write(data, 0, count);
    }
    
    gos.finish();
    gos.flush();
    gos.close();
}
```

注：操作完成后，调用finish方法和flush方法

<!--more-->

<br/>



#### GZIPInputStream类：解压缩

```java
public GZIPInputStream(InputStream in) throws IOException {}
```

GZIPInputStream提供了一个带定长的read方法

```java
public static void decompress(InputStream is, OutputStream os) throws Exception {

    GZIPInputStream gis = new GZIPInputStream(is);

    int count;
    byte data[] = new byte[BUFFER];
    while ((count = gis.read(data, 0, BUFFER)) != -1) {
        os.write(data, 0, count);
    }

    gis.close();
}
```

<br/>



#### IO的体系

```
1.流的分类：
  按照数据流向的不同：输入流  输出流
  按照处理数据的单位的不同：字节流  字符流（处理的文本文件）
  按照角色的不同：节点流（直接作用于文件的）  处理流
 
 
2.IO的体系
  抽象基类            节点流（文件流）             缓冲流（处理流的一种）
  InputStream        FileInputStream           BufferedInputStream
  OutputStream       FileOutputStream          BufferedOutputStream
  Reader             FileReader                BufferedReader
  Writer             FileWriter                BufferedWriter
```

<br/>