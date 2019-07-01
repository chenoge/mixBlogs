---
title: SpringBoot2-Hikari数据源配置
date: 2019-07-01 11:30:45
tags: [SpringBoot,Hikari]
---

#### 使用方式

在`pom.xml`中引入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

然后`SpringBoot application.properties`中配置数据源参数：

```
#datasource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ak_blog?autoReconnect=true&useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true
spring.datasource.username=root
spring.datasource.password=
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.maximum-pool-size=15
spring.datasource.hikari.auto-commit=true
spring.datasource.hikari.idle-timeout=30000
spring.datasource.hikari.pool-name=DatebookHikariCP
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.connection-test-query=SELECT 1
```

<!--more-->

**或者**增加`HikariCP`配置文件，设置连接池配置

```java
package pub.lichao.test.config;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;


/**
 * HikariCP连接池配置
 */
@Configuration
public class DataSourceConfig {

    @Value("${spring.datasource.url}")
    private String dataSourceUrl;

    @Value("${spring.datasource.username}")
    private String user;

    @Value("${spring.datasource.password}")
    private String password;

    @Bean
    public DataSource primaryDataSource() {
        HikariConfig config = new HikariConfig();
        
        //数据源
        config.setJdbcUrl(dataSourceUrl); 
        
         //用户名
        config.setUsername(user);
        
        //密码
        config.setPassword(password); 
        
        //是否自定义配置，为true时下面两个参数才生效
        config.addDataSourceProperty("cachePrepStmts", "true"); 
        
        //连接池大小默认25，官方推荐250-500
        config.addDataSourceProperty("prepStmtCacheSize", "250"); 
        
        //单条语句最大长度默认256，官方推荐2048
        config.addDataSourceProperty("prepStmtCacheSqlLimit", "2048");
        
        //新版本MySQL支持服务器端准备，开启能够得到显著性能提升
        config.addDataSourceProperty("useServerPrepStmts", "true"); 
        
        config.addDataSourceProperty("useLocalSessionState", "true");
        config.addDataSourceProperty("useLocalTransactionState", "true");
        config.addDataSourceProperty("rewriteBatchedStatements", "true");
        config.addDataSourceProperty("cacheResultSetMetadata", "true");
        config.addDataSourceProperty("cacheServerConfiguration", "true");
        config.addDataSourceProperty("elideSetAutoCommits", "true");
        config.addDataSourceProperty("maintainTimeStats", "false");

        HikariDataSource ds = new HikariDataSource(config);
        return ds;
    }
}
```

<br/>



#### 自定义配置

自定义数据源官网给出了两种方式：

先看官网给的配置文件：

```
app.datasource.jdbc-url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
app.datasource.maximum-pool-size=30
```

1）第一种是使用DataSourceBuilder来构造数据源：

```java
@Bean
@ConfigurationProperties("app.datasource")
public HikariDataSource dataSource() {
	return DataSourceBuilder.create().type(HikariDataSource.class).build();
}
```



2）第二种是使用DataSourceProperties来构造数据源：

```java
@Bean
@Primary
@ConfigurationProperties("app.datasource")
public DataSourceProperties dataSourceProperties() {
	return new DataSourceProperties();
}

@Bean
@ConfigurationProperties("app.datasource")
public HikariDataSource dataSource(DataSourceProperties properties) {
	return properties.initializeDataSourceBuilder().type(HikariDataSource.class).build();
}
```

<br/>



#### 双数据源配置

```
app.datasource.first.type=com.zaxxer.hikari.HikariDataSource
app.datasource.first.maximum-pool-size=30

app.datasource.second.url=jdbc:mysql://localhost/test
app.datasource.second.username=dbuser
app.datasource.second.password=dbpass
app.datasource.second.max-total=30
```

采用`DataSourceProperties`构造：

```java
@Bean
@Primary
@ConfigurationProperties("app.datasource.first")
public DataSourceProperties firstDataSourceProperties() {
	return new DataSourceProperties();
}

@Bean
@Primary
@ConfigurationProperties("app.datasource.first")
public DataSource firstDataSource() {
	return firstDataSourceProperties().initializeDataSourceBuilder().build();
}

@Bean
@ConfigurationProperties("app.datasource.second")
public DataSourceProperties secondDataSourceProperties() {
	return new DataSourceProperties();
}

@Bean
@ConfigurationProperties("app.datasource.second")
public DataSource secondDataSource() {
	return secondDataSourceProperties().initializeDataSourceBuilder().build();
}
```

<br/>



#### hikariCP连接池配置

```
主要配置

1、dataSourceClassName
这是DataSourceJDBC驱动程序提供的类的名称。请查阅您的特定JDBC驱动程序的文档以获取此类名称。请注意，如果您正在使用jdbcUrl“旧式”基于DriverManager的JDBC驱动程序配置，则不需要此属性 。 默认值：无

2、jdbcUrl
该属性指示HikariCP使用“基于DriverManager的”配置。我们认为基于DataSource的配置由于各种原因（参见下文）是优越的，但对于许多部署来说，几乎没有显着差异。 在“旧”驱动程序中使用此属性时，您可能还需要设置该 driverClassName属性，但不要先尝试。 请注意，如果使用此属性，您仍然可以使用DataSource属性来配置您的驱动程序，实际上建议您使用URL本身中指定的驱动程序参数。 默认值：无

3、username
此属性设置从基础驱动程序获取连接时使用的默认身份验证用户名。请注意，对于DataSources，这通过调用DataSource.getConnection(*username*, password)基础DataSource 以非常确定的方式工作。但是，对于基于驱动程序的配置，每个驱动程序都不同。在基于驱动程序的情况下，HikariCP将使用此username属性来设置传递给驱动程序调用的user属性。如果这不是你所需要的，例如完全跳过这个方法并且调用。 默认值：无PropertiesDriverManager.getConnection(jdbcUrl, props)addDataSourceProperty("username", ...)

4、password
此属性设置从基础驱动程序获取连接时使用的默认身份验证密码。请注意，对于DataSources，这通过调用DataSource.getConnection(username, *password*)基础DataSource 以非常确定的方式工作。但是，对于基于驱动程序的配置，每个驱动程序都不同。在基于驱动程序的情况下，HikariCP将使用此password属性来设置传递给驱动程序调用的password属性。如果这不是你所需要的，例如完全跳过这个方法并且调用。 默认值：无PropertiesDriverManager.getConnection(jdbcUrl, props)addDataSourceProperty("pass", ...)
 

常用配置

5、autoCommit
此属性控制从池返回的连接的默认自动提交行为。它是一个布尔值。 默认值：true

6、 connectionTimeout
此属性控制客户端将等待来自池的连接的最大毫秒数。如果在没有可用连接的情况下超过此时间，则会抛出SQLException。最低可接受的连接超时时间为250 ms。 默认值：30000（30秒）

7、 idleTimeout
此属性控制允许连接在池中闲置的最长时间。 此设置仅适用于minimumIdle定义为小于maximumPoolSize。连接是否因闲置而退出，最大变化量为+30秒，平均变化量为+15秒。在超时之前，连接永远不会退出。值为0意味着空闲连接永远不会从池中删除。允许的最小值是10000ms（10秒）。 默认值：600000（10分钟）

8、 maxLifetime
此属性控制池中连接的最大生存期。正在使用的连接永远不会退休，只有在关闭后才会被删除。在逐个连接的基础上，应用较小的负面衰减来避免池中的大量消失。 我们强烈建议设置此值，并且应该比任何数据库或基础设施规定的连接时间限制短几秒。 值为0表示没有最大寿命（无限寿命），当然是idleTimeout设定的主题。 默认值：1800000（30分钟）

9、connectionTestQuery
如果您的驱动程序支持JDBC4，我们强烈建议您不要设置此属性。这是针对不支持JDBC4的“传统”驱动程序Connection.isValid() API。这是在连接从池中获得连接以确认与数据库的连接仍然存在之前将要执行的查询。再一次，尝试运行没有此属性的池，如果您的驱动程序不符合JDBC4的要求，HikariCP将记录一个错误以告知您。 默认值：无

10、minimumIdle
该属性控制HikariCP尝试在池中维护的最小空闲连接数。如果空闲连接低于此值并且连接池中的总连接数少于此值maximumPoolSize，则HikariCP将尽最大努力快速高效地添加其他连接。但是，为了获得最佳性能和响应尖峰需求，我们建议不要设置此值，而是允许HikariCP充当固定大小的连接池。 默认值：与maximumPoolSize相同

11、maximumPoolSize
此属性控制池允许达到的最大大小，包括空闲和正在使用的连接。基本上这个值将决定到数据库后端的最大实际连接数。对此的合理价值最好由您的执行环境决定。当池达到此大小并且没有空闲连接可用时，对getConnection（）的调用将connectionTimeout在超时前阻塞达几毫秒。 默认值：10

12、metricRegistry
该属性仅通过编程配置或IoC容器可用。该属性允许您指定池使用的Codahale / Dropwizard 实例MetricRegistry来记录各种指标。有关 详细信息，请参阅Metrics维基页面。 默认值：无

13、healthCheckRegistry
该属性仅通过编程配置或IoC容器可用。该属性允许您指定池使用的Codahale / Dropwizard 的实例HealthCheckRegistry来报告当前的健康信息。有关 详细信息，请参阅健康检查 wiki页面。 默认值：无

14、poolName
此属性表示连接池的用户定义名称，主要出现在日志记录和JMX管理控制台中以识别池和池配置。 默认：自动生成


不常用配置

15、 initializationFailTimeout
如果池无法成功初始化连接，则此属性控制池是否将“快速失败”。任何正数都取为尝试获取初始连接的毫秒数; 应用程序线程将在此期间被阻止。如果在超时发生之前无法获取连接，则会引发异常。此超时被应用后的connectionTimeout 期。如果值为零（0），HikariCP将尝试获取并验证连接。如果获得连接但未通过验证，将抛出异常并且池未启动。但是，如果无法获得连接，则会启动该池，但后续获取连接的操作可能会失败。小于零的值将绕过任何初始连接尝试，并且在尝试获取后台连接时，池将立即启动。因此，以后努力获得连接可能会失败。 默认值：1

16、isolateInternalQueries
此属性确定HikariCP是否在其自己的事务中隔离内部池查询，例如连接活动测试。由于这些通常是只读查询，因此很少有必要将它们封装在自己的事务中。该属性仅适用于autoCommit禁用的情况。 默认值：false

17、allowPoolSuspension
该属性控制池是否可以通过JMX暂停和恢复。这对于某些故障转移自动化方案很有用。当池被暂停时，呼叫 getConnection()将不会超时，并将一直保持到池恢复为止。 默认值：false

18、readOnly
此属性控制默认情况下从池中获取的连接是否处于只读模式。注意某些数据库不支持只读模式的概念，而其他数据库则在Connection设置为只读时提供查询优化。无论您是否需要此属性，都将主要取决于您的应用程序和数据库。 默认值：false

19、registerMbeans
该属性控制是否注册JMX管理Bean（“MBeans”）。 默认值：false

20、catalog
该属性设置默认目录为支持目录的概念数据库。如果未指定此属性，则使用由JDBC驱动程序定义的默认目录。 默认：驱动程序默认

21、connectionInitSql
该属性设置一个SQL语句，在将每个新连接创建后，将其添加到池中之前执行该语句。如果这个SQL无效或引发异常，它将被视为连接失败并且将遵循标准重试逻辑。 默认值：无

22、driverClassName
HikariCP将尝试通过DriverManager仅基于驱动程序来解析驱动程序jdbcUrl，但对于一些较旧的驱动程序，driverClassName还必须指定它。除非您收到明显的错误消息，指出找不到驱动程序，否则请忽略此属性。 默认值：无

23、transactionIsolation
此属性控制从池返回的连接的默认事务隔离级别。如果未指定此属性，则使用由JDBC驱动程序定义的默认事务隔离级别。如果您有针对所有查询通用的特定隔离要求，请仅使用此属性。此属性的值是从不断的名称Connection 类，如TRANSACTION_READ_COMMITTED，TRANSACTION_REPEATABLE_READ等 默认值：驱动程序默认

24、 validationTimeout
此属性控制连接测试活动的最长时间。这个值必须小于connectionTimeout。最低可接受的验证超时时间为250 ms。 默认值：5000

25、 leakDetectionThreshold
此属性控制在记录消息之前连接可能离开池的时间量，表明可能存在连接泄漏。值为0意味着泄漏检测被禁用。启用泄漏检测的最低可接受值为2000（2秒）。 默认值：0

26、 dataSource
此属性仅通过编程配置或IoC容器可用。这个属性允许你直接设置DataSource池的实例，而不是让HikariCP通过反射来构造它。这在一些依赖注入框架中可能很有用。当指定此属性时，dataSourceClassName属性和所有DataSource特定的属性将被忽略。 默认值：无

27、schema
该属性设置的默认模式为支持模式的概念数据库。如果未指定此属性，则使用由JDBC驱动程序定义的默认模式。 默认：驱动程序默认

28、 threadFactory
此属性仅通过编程配置或IoC容器可用。该属性允许您设置java.util.concurrent.ThreadFactory将用于创建池使用的所有线程的实例。在一些只能通过ThreadFactory应用程序容器提供的线程创建线程的有限执行环境中需要它。 默认值：无

29、 scheduledExecutor
此属性仅通过编程配置或IoC容器可用。该属性允许您设置java.util.concurrent.ScheduledExecutorService将用于各种内部计划任务的实例。如果为ScheduledThreadPoolExecutor 实例提供HikariCP，建议setRemoveOnCancelPolicy(true)使用它。 默认值：无
```