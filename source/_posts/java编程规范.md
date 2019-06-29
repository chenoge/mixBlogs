---
title: java编程规范
date: 2019-06-29 19:25:50
tags: [java]
---

#### 一、编程规约

##### (一) 命名规约 

- 【强制】类名使用 UpperCamelCase 风格，必须遵从驼峰形式，但以下情形例外：（领域模型
  的相关命名）`DO / DTO / VO / DAO` 等。

  **正例**：`MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion`

  **反例**：`macroPolo / UserDo / XMLService / TCPUDPDeal / TAPromotion`



- 【强制】抽象类命名使用 Abstract 或 Base 开头；异常类命名使用 Exception 结尾；测试类命
  名以它要测试的类的名称开始，以 Test 结尾。

<!--more-->



- 【强制】中括号是数组类型的一部分，数组定义如下：`String[] args`;

  **反例**：请勿使用` String args[]`的方式来定义



- 【强制】POJO 类中的任何布尔类型的变量，都不要加 is，否则部分框架解析会引起序列化错
  误。

  反例：定义为基本数据类型 `boolean isSuccess`；的属性，它的方法也是 `isSuccess()`，RPC
  框架在反向解析的时候，“以为”对应的属性名称是 success，导致属性获取不到，进而抛出异常。



- 【强制】包名统一使用小写，点分隔符之间有且仅有一个自然语义的英语单词。包名统一使用
  单数形式，但是类名如果有复数含义，类名可以使用复数形式。

  正例： 应用工具类包名为 `com.alibaba.mpp.util`、类名为 `MessageUtils`（此规则参考 spring
  的框架结构）



- 【推荐】如果使用到了**设计模式**，建议在类名中体现出具体模式。

  说明：将设计模式体现在名字中，有利于阅读者快速理解架构设计思想。

  正例：

  ```java
  public class OrderFactory;
  public class LoginProxy;
  public class ResourceObserver;
  ```



- 接口和实现类的命名有两套规则：

  - 【强制】对于 Service 和 DAO 类，基于 SOA 的理念，暴露出来的服务一定是接口，内部
    的实现类用 Impl 的后缀与接口区别。

    **正例**：`CacheServiceImpl` 实现 `CacheService` 接口。

    

  - 推荐】 如果是形容能力的接口名称，取对应的形容词做接口名（通常是–able 的形
    式）。

    **正例**：`AbstractTranslator` 实现 `Translatable`。



- 【参考】各层命名规约：

  - A) Service/DAO 层方法命名规约
    - 1） 获取单个对象的方法用 get 做前缀。
    - 2） 获取多个对象的方法用 list 做前缀。
    - 3） 获取统计值的方法用 count 做前缀。
    - 4） 插入的方法用 save（推荐）或 insert 做前缀。
    - 5） 删除的方法用 remove（推荐）或 delete 做前缀。
    - 6） 修改的方法用 update 做前缀。

  

  - B) 领域模型命名规约
    - 1） 数据对象：xxxDO，xxx 即为数据表名。
    - 2） 数据传输对象：xxxDTO，xxx 为业务领域相关的名称。
    - 3） 展示对象：xxxVO，xxx 一般为网页名称。
    - 4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO。



##### (二) 常量定义

- 【强制】不允许出现任何魔法值（即未经定义的常量）直接出现在代码中。

  反例： 

  ```java
  String key="Id#taobao_"+tradeId；
  cache.put(key, value);
  ```



- 【强制】long 或者 Long 初始赋值时，必须使用大写的 L，不能是小写的 l，小写容易跟数字 1
  混淆，造成误解。

  说明：`Long a = 2l; `写的是数字的 21，还是 Long 型的 2?



##### (三) OOP 规约

- 【强制】Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用 equals。

  正例：` "test".equals(object);` 

  反例： `object.equals("test");`

  说明：推荐使用 `java.util.Objects#equals` （JDK7 引入的工具类）



- 【强制】所有的相同类型的**包装类**对象之间值的比较，全部使用 equals 方法比较。

  说明：对于 `Integer var=?`在`-128` 至 `127` 之间的赋值，Integer 对象是在 `IntegerCache.cache` 产生，会复用已有对象，这个区间内的 Integer 值可以直接使用`==`进行判断，但是这个区间之
  外的所有数据，都会在堆上产生，并不会复用已有对象，这是一个大坑，推荐使用 equals 方
  法进行判断。



- 【强制】构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。



- 【推荐】setter 方法中，参数名称与类成员变量名称一致，`this.成员名=参数名`。在
  getter/setter 方法中，尽量不要增加业务逻辑，增加排查问题难度。

  反例：

  ```java
  public Integer getData(){
      if(true) {
          return data + 100;
      } else {
          return data - 100;
      }
  }
  ```



- 【推荐】循环体内，字符串的联接方式，使用 StringBuilder 的 append 方法进行扩展。

  反例：

  ```java
  String str = "start";
  for(int i=0; i<100; i++){
      str = str + "hello";
  }
  ```

  说明：反编译出的字节码文件显示每次循环都会 new 出一个 StringBuilder 对象，然后进行
  append 操作，最后通过 toString 方法返回 String 对象，造成内存资源浪费。



##### (四) 控制语句 

- 【强制】在一个 switch 块内，每个 case 要么通过 `break/return` 来终止，要么注释说明程序将继续执行到哪一个 case 为止；在一个 switch 块内，都必须包含一个 default 语句并且放在
  最后，即使它什么代码也没有。



- 【推荐】除常用方法（如 getXxx/isXxx）等外，不要在条件判断中执行复杂的语句，以提高可读性。

  正例：

  ```java
  //伪代码如下
  InputStream stream = file.open(fileName, "w"); 
  if (stream != null) {
      …
  }
  ```

  反例：

  ```java
  if (file.open(fileName, "w") != null)) {
      …
  } 
  ```



- 【推荐】循环体中的语句要考量性能，以下操作尽量移至循环体外处理，如**定义对象、变量、**
  **获取数据库连接**，进行不必要的 **try-catch 操作**（这个 try-catch 是否可以移至循环体外）。



##### (五) 注释规约

- 【强制】所有的**类**都必须添加创建者信息。



- 【强制】所有的**抽象方法**（包括接口中的方法）必须要用 javadoc 注释、除了返回值、参数、
  异常说明外，还必须指出该方法做什么事情，实现什么功能。

  说明：如有实现和调用注意事项，请一并说明。



- 【强制】方法内部单行注释，在被注释语句上方另起一行，使用`//注释`。方法内部多行注释使
  用`/* */注释	`，注意与代码对齐。



##### (六) 其它

- 【强制】在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度。
  说明：不要在方法体内定义：`Pattern pattern = Pattern.compile(规则);`



- 【强制】获取当前毫秒数：`System.currentTimeMillis(); `而不是 `new Date().getTime();
  `

  说明：如果想获取更加精确的纳秒级时间值，用 `System.nanoTime`。在 JDK8 中，针对统计时
  间等场景，推荐使用 Instant 类。

<br/>



#### 二、异常日志

##### (一) 异常处理 

- 【强制】不要捕获 Java 类库中定义的继承自 `RuntimeException` 的运行时异常类，如：
  `IndexOutOfBoundsException / NullPointerException`，这类异常由程序员**预检查**来规避，保证程序健壮性。

  正例：`if(obj != null) {...}`

  反例：`try { obj.method() } catch(NullPointerException e){…}`



- 【强制】异常不要用来做流程控制，条件控制，因为异常的处理效率比条件分支低。



- 【强制】对大段代码进行 try-catch，这是不负责任的表现。catch 时请分清**稳定代码**和**非稳定代码**，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的 catch 尽可能进行区分异常类型，再做对应的异常处理。



- 【强制】有 try 块放到了事务代码中，catch 异常后，如果需要回滚事务，一定要注意手动回滚事务。



- 【强制】finally 块必须对资源对象、流对象进行关闭，有异常也要做 try-catch。
  说明：如果 JDK7，可以使用 try-with-resources 方法。



- 【强制】不能在 finally 块中使用 return，finally 块中的 return 返回后方法结束执行，不会再执行 try 块中的 return 语句。



##### (二) 日志规约 

- .【强制】应用中不可直接使用日志系统（Log4j、Logback）中的 API，而应依赖使用日志框架
  SLF4J 中的 API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一。

  ```java
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  private static final Logger logger = LoggerFactory.getLogger(Abc.class); 
  ```



- 【推荐】可以使用 warn 日志级别来记录用户输入参数错误的情况，避免用户投诉时，无所适
  从。注意日志输出的级别，error 级别只记录系统逻辑出错、异常、或者重要的错误信息。如非必要，请不要在此场景打出 error 级别，避免频繁报警。



说明：大量地输出无效日志，不利于系统性能提升，也不利于快速定位错误点。纪录日志时请
思考：这些日志真的有人看吗？看到这条日志你能做什么？能不能给问题排查带来好处？

<br/>



#### 三、MYSQL 规约

##### (一) 建表规约 

- 【强制】表名不使用复数名词。

  说明：表名应该仅仅表示表里面的实体内容，不应该表示实体数量，对应于 DO 类名也是单数
  形式，符合表达习惯。



- 【强制】唯一索引名为 `uk_`字段名；普通索引名则为 `idx_`字段名。

  说明：`uk_ 即 unique key`；`idx_ 即 index `的简称。



- 【强制】小数类型为 decimal，禁止使用 float 和 double。

  说明：float 和 double 在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不
  正确的结果。如果存储的数据范围超过 decimal 的范围，建议将数据拆成整数和小数分开存储。



- 【强制】varchar 是可变长字符串，不预先分配存储空间，长度不要超过 5000，如果存储长度大于此值，定义字段类型为 TEXT，独立出来一张表，用主键来对应，避免影响其它字段索引效率。



- 【强制】表必备三字段：id, gmt_create, gmt_modified。

  说明：其中 id 必为主键，类型为 `unsigned bigint`、单表时自增、步长为 1；分表时改为从
  TDDL Sequence 取值，确保分表之间的全局唯一。`gmt_create, gmt_modified` 的类型均为
  `date_time` 类型。



- 【推荐】单表行数超过` 500 万行`或者`单表容量超过 2GB`，才推荐进行分库分表。

  说明：如果预计三年后的数据量根本达不到这个级别，请不要在创建表时就分库分表。
  反例：某业务三年总数据量才 2 万行，却分成 1024 张表，问：你为什么这么设计？答：分 1024
  张表，不是标配吗？



- 【参考】合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是**提升检索速度**。

  正例：人的年龄用 `unsigned tinyint`（表示范围 0-255，人的寿命不会超过 255 岁）；海龟就必须是 `smallint`，但如果是太阳的年龄，就必须是 `int`；如果是所有恒星的年龄都加起来，那么就必须使用 `bigint`。



##### (二) SQL 规约

- 【强制】不要使用 `count(列名)或 count(常量)`来替代 `count(*)`，`count(*)`就是 SQL92 定义的标准统计行数的语法，**跟数据库无关，跟 NULL 和非 NULL 无关**。

  说明：`count(*)`会统计值为 NULL 的行，而 `count(列名)`不会统计此列为 NULL 值的行。



- 【强制】在代码中写分页查询逻辑时，若 count 为 0 应直接返回，避免执行后面的分页语句。



- 【强制】count(distinct col) 计算该列除 NULL 之外的不重复数量。注意 `count(distinct
  col1, col2) `如果其中一列全为 NULL，那么即使另一列有不同的值，也返回为 0。



-  【强制】当某一列的值全是 NULL 时，**count(col)的返回结果为 0，但 sum(col)的返回结果为**
  **NULL**，因此使用 sum()时需注意 NPE 问题。

  正例：可以使用如下方式来避免 sum 的 NPE 问题：`SELECT IF(ISNULL(SUM(g)),0,SUM(g)) FROM
  table;`



- 【强制】使用 `ISNULL()`来判断是否为 NULL 值。

  注意：**NULL 与任何值的直接比较都为 NULL**。

  说明：

  ```
  1） NULL<>NULL 的返回结果是 NULL，不是 false。
  2） NULL=NULL 的返回结果是 NULL，不是 true。
  3） NULL<>1 的返回结果是 NULL，而不是 true。
  ```



- 【强制】不得使用**外键**与**级联**，一切外键概念必须在应用层解决。

  说明：（概念解释）学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外键。
  如果更新学生表中的 student_id，同时触发成绩表中的 student_id 更新，则为**级联更新**。**外键与级联更新适用于单机低并发，不适合分布式、高并发集群**；级联更新是强阻塞，存在数据库更新风暴的风险；外键影响数据库的插入速度。



- 【强制】禁止使用存储过程，存储过程难以调试和扩展，更没有移植性。



- 【强制】IDB 数据订正时，删除和修改记录时，要先 select，避免出现误删除，确认无误才能
  提交执行。



- 【参考】所有的字符存储与表示，均以 utf-8 编码，那么**字符计数方法**注意：

  说明：

  ```
  SELECT LENGTH("阿里巴巴")； 返回为 12
  SELECT CHARACTER_LENGTH("阿里巴巴")； 返回为 4
  如果要使用表情，那么使用 utfmb4 来进行存储，注意它与 utf-8 编码。
  ```



##### (三) 服务器规约

- 【推荐】高并发服务器建议调小 TCP 协议的 time_wait 超时时间。

  说明：操作系统默认 240 秒后，才会关闭处于 time_wait 状态的连接，在高并发访问下，服务
  器端会因为处于 time_wait 的连接数太多，可能无法建立新的连接，所以需要在服务器上调小
  此等待值。

  正例：在 linux 服务器上请通过变更`/etc/sysctl.conf` 文件去修改该缺省值（秒）：

  ```
  net.ipv4.tcp_fin_timeout = 30
  ```



#### 四、安全规约

- 【强制】用户敏感数据禁止直接展示，必须**对展示数据脱敏**。

  说明：支付宝中查看个人手机号码会显示成：`158****9119`，隐藏中间 4 位，防止隐私泄露。



-  【强制】可被用户直接访问的功能必须进行权限控制校验。

  说明：防止没有做权限控制就可随意访问、操作别人的数据，比如查看、修改别人的订单。



- 【强制】用户请求传入的任何参数必须做有效性验证。

  说明：忽略参数校验可能导致：

  - page size 过大导致内存溢出
  - 恶意 order by 导致数据库慢查询
  - 正则输入源串拒绝服务 ReDOS
  - 任意重定向
  - SQL 注入
  - Shell 注入
  - 反序列化注入



- 【强制】禁止向 HTML 页面输出未经安全过滤或未正确转义的用户数据。



- 【强制】Web 应用必须正确配置 Robots 文件，非 SEO URL 必须配置为禁止爬虫访问。



- 【强制】在使用**平台资源**，譬如短信、邮件、电话、下单、支付，必须实现正确的防重放限制，
  如数量限制、疲劳度控制、验证码校验，避免被滥刷、资损。

  说明：如注册时发送验证码到手机，如果没有限制次数和频率，那么可以利用此功能骚扰到其
  它用户，并造成短信平台资源浪费。



- 【推荐】发贴、评论、发送即时消息等用户生成内容的场景必须实现防刷、文本内容违禁词过滤等风控策略。





