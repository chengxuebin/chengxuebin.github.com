---
layout: post
title: "命令行编译 Java"
category: "Java"
tags: ["java"]
---

#### 编译 Java

If you are using the Hibernate native API, then you need to use the Query#setComment method like this:

```java
List<Person> persons = (List<Person>) session.createQuery(
    "select p " +
    "from Person p ")
.setComment( "t95=firstdraft" )
.list();
```
#### 运行

If you are using JPA, then you can use a QueryHint:


```java
List<Person> persons = entityManager.createQuery(
        "select p " +
        "from Person p ", Person.class)
.setHint( "org.hibernate.comment", "t95=firstdraft" )
.getResultList();
```


[How to intercept and modify SQL queries with the Hibernate StatementInspector](https://vladmihalcea.com/hibernate-statementinspector/)