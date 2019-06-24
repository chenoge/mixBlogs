---
title: Jsoup获取节点方法
date: 2019-06-24 13:58:34
tags: [Jsoup]
---

#### JSoup应用的主要类

```
1、org.jsoup.Jsoup类
  Jsoup类是任何Jsoup程序的入口点，并将提供从各种来源加载和解析HTML文档的方法。

2、org.jsoup.nodes.Document类
  该类表示通过Jsoup库加载HTML文档。可以使用此类执行适用于整个HTML文档的操作。
  
3、org.jsoup.nodes.Element类
HTML元素是由标签名称，属性和子节点组成。 使用Element类，您可以提取数据，遍历节点和操作HTML。
```

<br/>



#### Jsoup类重要方法

```
static Connection connect(String url)
创建并返回URL的连接

static Document parse(File in, String charsetName)
将指定的字符集文件解析成文档

static Document parse(String html)
将给定的html代码解析成文档

static String clean(String bodyHtml, Whitelist whitelist)
从输入HTML返回安全的HTML，通过解析输入HTML并通过允许的标签和属性的白名单进行过滤
```

<br/>



#### 获取兄弟节点

```
firstElementSibling()这个方法是获取节点的第一个兄弟节点，这个方法返回的是Element类型

lastElementSibling():这个方法是获取节点的最后一个兄弟节点，返回的是Element类型

previousElementSibling():这个方法是获取节点的上一个兄弟节点，返回的是Element类型

previousSibling():这个方法也是获取节点的上一个兄弟节点，不过返回数据是Node类型

nextElementSibling():这个方法是获取节点的下一个兄弟节点，返回的是Element类型

nextSibling():这个方法同样是获取节点的下一个兄弟节点，返回数据是Node类型

siblingElements():这个方法是获取节点的所有兄弟节点。返回的是List<Element>类型

siblingNodes():这个方法也是获取节点的所有兄弟节点，但返回的是List<Node>类型
```

<br/>



#### 应用实例

```java
// 从URL加载文档，使用Jsoup.connect()方法从URL加载HTML
try {
    Document document = Jsoup.connect("http://www.yiibai.com").get();
    System.out.println(document.title());
} catch (IOException e) {
    e.printStackTrace();
}
```

```java
// 使用Jsoup.parse()方法从文件加载HTML
try {
    Document document = Jsoup.parse(new File("D:/temp/index.html"), "utf-8");
    System.out.println(document.title());
} catch (IOException e) {
    e.printStackTrace();
}
```

```java
// 使用Jsoup.parse()方法从字符串加载HTML
try {
    String html = "<html><head><title>First parse</title></head>" +
        "<body><p>Parsed HTML into a doc.</p></body></html>";
    Document document = Jsoup.parse(html);
    System.out.println(document.title());
} catch (IOException e) {
    e.printStackTrace();
}
```

```java
// 要获取网页中的所有链接
try {
    Document document = Jsoup.parse(new File("D:/temp/index.html"), "utf-8");
    Elements links = document.select("a[href]");
    for (Element link: links) {
        System.out.println("link : " + link.attr("href"));
        System.out.println("text : " + link.text());
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

