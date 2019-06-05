---
title: Java-ElasticSearch增删改查
date: 2019-06-05 17:57:29
tags: [Java,ElasticSearch]
---

##### Index(增加）

```java
public class IndexTest {
    public static void main(String[] argv) {
        Settings settings = ImmutableSettings.settingsBuilder()
            //指定集群名称  
            .put("cluster.name", "elasticsearch")
            //探测集群中机器状态  
            .put("client.transport.sniff", true).build();
        
        /* 
         * 创建客户端，所有的操作都由客户端开始，这个就好像是JDBC的Connection对象 
         * 用完记得要关闭 
         */
        Client client = new TransportClient(settings)
            .addTransportAddress(new InetSocketTransportAddress("192.168.1.106", 9300));
        String json = ESUtils.toJson(new LogModel());
        
        //在这里创建我们要索引的对象  
        IndexResponse response = client.prepareIndex("twitter", "tweet")
            //必须为对象单独指定ID  
            .setId("1")
            .setSource(json)
            .execute()
            .actionGet();
        //多次index这个版本号会变  
        System.out.println("response.version():" + response.version());
        client.close();
    }
}
```

<!--more-->

<br/>



#####  delete(删除）

```java
public class DeleteTest {
    public static void main(String[] argv) {
        Settings settings = ImmutableSettings.settingsBuilder()
            //指定集群名称  
            .put("cluster.name", "elasticsearch")
            //探测集群中机器状态  
            .put("client.transport.sniff", true).build();

        /* 
         * 创建客户端，所有的操作都由客户端开始，这个就好像是JDBC的Connection对象 
         * 用完记得要关闭 
         */
        Client client = new TransportClient(settings)
            .addTransportAddress(new InetSocketTransportAddress("192.168.1.106", 9300));

        //在这里创建我们要索引的对象  
        DeleteResponse response = client.prepareDelete("twitter", "tweet", "1")
            .execute()
            .actionGet();
        System.out.println(response.getId());
        System.out.println(ESUtils.toJson(response.getHeaders()));
    }
}
```

<br/>



##### GET(查询）

```java
public class GetTest {
    public static void main(String[] argv) {
        Settings settings = ImmutableSettings.settingsBuilder()
            //指定集群名称  
            .put("cluster.name", "elasticsearch")
            //探测集群中机器状态  
            .put("client.transport.sniff", true).build();
        
        /* 
         * 创建客户端，所有的操作都由客户端开始，这个就好像是JDBC的Connection对象 
         * 用完记得要关闭 
         */
        Client client = new TransportClient(settings)
            .addTransportAddress(new InetSocketTransportAddress("192.168.1.106", 9300));
       
        //在这里创建我们要索引的对象  
        GetResponse response = client.prepareGet("twitter", "tweet", "1")
            .execute()
            .actionGet();
        System.out.println("response.getId():" + response.getId());
        System.out.println("response.getSourceAsString():" + response.getSourceAsString());
    }
}
```

<br/>