---
title: SpringDataJPA-Query注解
date: 2019-03-19 20:27:21
tags: [Query,Modifying,Transactional]
---

`@Query`注解查询适用于所查询的数据无法通过关键字查询得到结果的查询。这种查询可以摆脱像关键字查询那样的约束，将查询直接在相应的接口方法中声明，结构更为清晰，这是`Spring Data`的特有实现。

<br/>

##### 一、索引参数

索引参数：索引值从1开始，查询中`?X`个数需要与方法定义的参数个数相一致，并且顺序也要一致。

```java
@Query("SELECT p FROM Person p WHERE p.lastName = ?1 AND p.email = ?2")
List<Person> testQueryAnnotationParams1(String lastName, String email);
```

注释：上面代码中的`?1 、?2`表示参数的占位符，需要和方法中所传递的参数顺序一致。`X`是从`1`开始。

<br/>

##### 二、命名参数

命名参数：可以定义好参数名，赋值时使用`@Param("参数名")`，而不用管顺序-推荐使用

```java
// 为@Query注解传递参数的方式1：命名参数
@Query("SELECT p FROM Person p WHERE p.lastName = :lastName AND p.email = :email")
List<Person> testQueryAnnotationParams2(
    @Param("email") String email, 
    @Param("lastName") String lastName);
```

注释：`:lastName` 、`:email `表示为参数命名，方法中所传递的参数使用`@Param`注解标识命名参数。这种方式不用管参数的顺序。

<br/>

<!--more-->

##### 三、原生SQL进行查询

```java
@Query(value = "SELECT count(id) FROM jpa_persons", nativeQuery = true)
long getTotalCount();
```

```java
 @Query(nativeQuery = true,
        value = "select * from user where name LIKE CONCAT('%', :file_name, '%')")
List<User> findByFileName(@Param("file_name") String file_name);
```

```java
 @Query(nativeQuery = true, value = "select * from user where name LIKE %:file_name%")
List<User> findByFileName(@Param("file_name") String file_name);
```



注释：

- 当设置`nativeQuery=true`即可以使用`原生SQL`进行查询
- `like`查询：`LIKE CONCAT('%', :file_name, '%')`

<br/>

##### 四、`@Modifying`注解

1. 在`@Query`注解中编写`JPQL`实现`DELETE`和`UPDATE`操作的时候必须加上`@modifying`注解，以通知`Spring Data `这是一个`DELETE`或`UPDATE`操作。
2. `UPDATE`或者`DELETE`操作需要使用**事务**，此时需要定义`Service`层，在`Service`层的方法上添加`@Transactional`。
3. 注意`JPQL`不支持`INSERT`操作。

```java
@Transactional
@Modifying
@Query("UPDATE Person p SET p.email = :email WHERE p.id = :id")
void updatePersonEmail(@Param("id") Integer id, @Param("email") String email);
```

<br/>

#### 五、EntityManager

```java
public class EpsmSubjectRepositoryImpl {
    @PersistenceContext
    EntityManager entityManager;

    public List < EpsmSubject > findEpsmSubjectList(
        Long catalogId, String subjectName, Long start, Long pageSize) {
        
        // 构造sql语句
        StringBuffer sql = new StringBuffer("SELECT * FROM t_epsm_subject where 1 = 1 ");

        if (catalogId != null) {
            sql.append(" and catalog_id = :catalogId ");
        }

        if (StringUtils.isNotBlank(subjectName)) {
            sql.append(" and subject_name like CONCAT('%', :subjectName, '%')");
        }

        sql.append(" order by created_time DESC");

        if (start != null && pageSize != null) {
            sql.append(" limit :start , :pageSize");
        }

        Query query = entityManager.createNativeQuery(sql.toString(), EpsmSubject.class);

        // 填充查询参数

        if (catalogId != null) {
            query.setParameter("catalogId", catalogId);
        }

        if (StringUtils.isNotBlank(subjectName)) {
            query.setParameter("subjectName", subjectName);
        }

        if (start != null && pageSize != null) {
            query.setParameter("start", start);
            query.setParameter("pageSize", pageSize);
        }

        return query.getResultList();
    }
}
```

<br/>