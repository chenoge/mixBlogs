---
title: java-jdbc
date: 2019-07-01 11:07:12
tags: [java,jdbc]
---

#### JDBC基础

##### 什么是JDBC？

Java 数据库连接，（Java Database Connectivity，简称JDBC）是Java语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。JDBC也是Sun Microsystems的商标。它JDBC是面向关系型数据库的。

<br/>



##### JDBC操作步骤

```
第1步：注冊驱动 (仅仅做一次)

第2步：建立连接(Connection)

第3步：创建运行SQL的语句(Statement)

第4步：运行语句

第5步：处理运行结果(ResultSet)

第6步：释放资源
```

- DriverManager：这个类管理数据库驱动程序的列表，查看加载的驱动是否符合JAVA Driver API的规范。
- Connection：用于联系数据库的所有方法。与数据库中的所有的通信是通过唯一的连接对象。
- Statement：把创建的SQL对象，转而存储到数据库当中。
- ResultSet：它是一个迭代器，用于检索查询数据。
- SQLException：此类处理数据库应用程序中发生的任何错误。

<!--more-->

<br/>



##### 1、载入驱动

```
注冊驱动有三种方式：

　　1.Class.forName(“com.mysql.jdbc.Driver”);
　　推荐这样的方式，不会对详细的驱动类产生依赖

　　2. DriverManager.registerDriver(com.mysql.jdbc.Driver);
　　会对详细的驱动类产生依赖

　　3. System.setProperty(“jdbc.drivers”, “driver1:driver2”);
　　尽管不会对详细的驱动类产生依赖；但注冊不太方便。所以非常少使用
```

<br/>



##### 2、建立连接

```
通过Connection建立连接，Connection是一个接口类。其功能是与数据库进行连接（会话）。

建立Connection接口类对象：

　　Connection conn =DriverManager.getConnection(url, user, password);
　　
　　当中URL的格式要求为：JDBC:子协议:子名称//主机名:port/数据库名？属性名=属性值&…

　　如："jdbc:mysql://localhost:3306/test“

　　user即为登录数据库的username，如root

　　password即为登录数据库的密码，为空就填””
```

<br/>



##### 3、创建运行对象

```
　　运行对象Statement负责运行SQL语句。由Connection对象产生。

　　Statement st = connection.createStatement();

　　Statement接口类还派生出两个接口类PreparedStatement和CallableStatement，这两个接口类对象为我们提供了更加强大的数据訪问功能。

　　PreparedStatement能够对SQL语句进行预编译，这样防止了SQL注入 提高了安全性。

　　PreparedStatement ps=connection.prepareStatement( "update user set id=? where username=?”); 
　　
　　————sql语句中用“?”作为通配符，变量值通过参数设入：ps.setObject(1, object);

　　而且预编译结果能够存储在PreparedStatement对象中。当多次运行SQL语句时能够提高效率。

　　作为Statement的子类，PreparedStatement继承了Statement的全部函数。

 

　　CallableStatement接口

　　CallableStatement类继承了PreparedStatement类，他主要用于运行SQL存储过程。

　　在JDBC中运行SQL存储过程须要转义。

　　JDBC API提供了一个SQL存储过程的转义语法：

　　{call<procedure-name>[<arg1>,<arg2>, ...]}

　　procedure-name：是所要运行的SQL存储过程的名字

　　[<arg1>,<arg2>, ...]：是相相应的SQL存储过程所须要的參数
```

<br/>



##### 4、运行SQL语句

```
运行对象Statement 或 PreparedStatement 提供两个经常使用的方法来运行SQL语句。

executeQuery(Stringsql),该方法用于运行实现查询功能的sql语句。返回类型为ResultSet（结果集）。

如：ResultSet  rs =st.executeQuery(sql);


executeUpdate(Stringsql),该方法用于运行实现增、删、改功能的sql语句，返回类型为int，即受影响的行数。

如：int flag = st.executeUpdate(sql);
```

```java
String sql = "insert into user(sno,name,age)values(?,?,?)"; //定义一个要执行的SQL语句
PreparedStatement ps = (PreparedStatement) conn.prepareStatement(sql);
ps.setString(1, student.getSno()); //设置SQL语句的第一个参数
ps.setString(2, student.getName()); //设置SQL语句的第二个参数
ps.setInt(3, student.getAge()); //设置SQL语句的第三个参数
int i = ps.executeUpdate(); //执行SQL语句
```

<br/>



##### 5、处理运行结果

```
ResultSet对象负责保存Statement运行后所产生的查询结果，通过游标来操作。

游标就是一个可控制的、能够指向随意一条记录的指针。有了这个指针我们就能轻易地指出我们要对结果集中的哪一条记录进行改动、删除，或者要在哪一条记录之前插入数据。一个结果集对象中仅仅包括一个游标。

另外，借助ResultSetMetaData ，可以将数据表的结构信息都查出来。

ResultSetMetaData rsmd= resultSet.getMetaData();
```

```java
String sql = "SELECT * FROM user";
Statement statement = conn.createStatement(sql); //sql语句的执行对象
ResultSet row = statement.executeQuery(); //执行SQL语句
while (row.next()) {
    String loginName = row.getString("loginName");
    String userName = row.getString("userName");
    String password = row.getString("password");
    int sex = resultSet.getInt("sex");
    System.out.println(loginName + "-" + userName + "-" + password + "-" + sex);
}
```

<br/>



##### 6、释放资源

```
数据库资源不关闭，其占用的内存不会被释放，徒耗资源，影响系统。
```

<br/>



```java
package com.runoob.test;
 
import java.sql.*;
 
public class MySQLDemo {
 
    // MySQL 8.0 以下版本 - JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";  
    static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB";
 
    // MySQL 8.0 以上版本 - JDBC 驱动名及数据库 URL
    //static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";  
    //static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB?useSSL=false&serverTimezone=UTC";
 
 
    // 数据库的用户名与密码，需要根据自己的设置
    static final String USER = "root";
    static final String PASS = "123456";
 
    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        try{
            // 注册 JDBC 驱动
            Class.forName(JDBC_DRIVER);
        
            // 打开链接
            System.out.println("连接数据库...");
            conn = DriverManager.getConnection(DB_URL,USER,PASS);
        
            // 执行查询
            System.out.println(" 实例化Statement对象...");
            stmt = conn.createStatement();
            String sql;
            sql = "SELECT id, name, url FROM websites";
            ResultSet rs = stmt.executeQuery(sql);
        
            // 展开结果集数据库
            while(rs.next()){
                // 通过字段检索
                int id  = rs.getInt("id");
                String name = rs.getString("name");
                String url = rs.getString("url");
    
                // 输出数据
                System.out.print("ID: " + id);
                System.out.print(", 站点名称: " + name);
                System.out.print(", 站点 URL: " + url);
                System.out.print("\n");
            }
            // 完成后关闭
            rs.close();
            stmt.close();
            conn.close();
        }catch(SQLException se){
            // 处理 JDBC 错误
            se.printStackTrace();
        }catch(Exception e){
            // 处理 Class.forName 错误
            e.printStackTrace();
        }finally{
            // 关闭资源
            try{
                if(stmt!=null) stmt.close();
            }catch(SQLException se2){
            }// 什么都不做
            try{
                if(conn!=null) conn.close();
            }catch(SQLException se){
                se.printStackTrace();
            }
        }
        System.out.println("Goodbye!");
    }
}
```





