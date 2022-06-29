---
title: Hibernate学习笔记
date: 2022-06-05 20:48:28
tags: ['Hibernate','Java']
categories: Java
---

> 本次学习是基于`Hibernate5.x`进行的，并且使用的是Hibernate映射文件的方式，因为我确实没有找到比较的新的资源:dog:，啊这...就先做一个入门吧

## 概念

### 什么是Hibernate？

一个框架，一个Java领域的持久化框架，一个`ORM`框架

### 什么是持久化？

- 狭义的理解：“持久化”仅仅指把对象永久的保存到数据库中

- 广义的理解：“持久化”包括和数据库相关的各种操作
  - 保存：把对象永久的保存到数据库中
  - 更新：更新数据库中对象（记录）的状态
  - 删除：从数据库中删除一个对象
  - 查询：根据特定的查询条件，把符合条件的一个或多个对象从数据库加载到内存中
  - 加载：根据特定的**`OID`**，把一个对象从数据库加载到内存中
    - **`OID`**：为了在系统中找到所需对象，需要为每一个对象分配一个唯一的标识号，在数据库中称之为主键，在对象术语中，则叫做对象标识（`Object-identifier-OID`）

### 什么是`ORM`？

`ORM`（Object/Relation Mapping）:**对象/关系**映射

- `ORM`主要解决对象-关系的映射

  - | 面向对象概念 | 面向关系概念   |
    | ------------ | -------------- |
    | 类           | 表             |
    | 对象         | 表的行（记录） |
    | 属性         | 表的列（字段） |

- `ORM`的思想：将关系数据库中表的记录映射成对象，以对象的形式展现，我们**可以把对数据库的操作转换为对对象的操作**

- `ORM`采用**元数据**来描述对象-关系映射细节，元数据通常采用XML格式，并且存放专门的对象-关系映射文件中

  - 元数据：描述数据的数据，例如描述类与表的关系的文件就成为元数据文件

## Hibernate开发步骤

### 搭建hibernate环境

1. 创建实体类

   ```java
   package com.sko.entity;
   
   /**
    * @author skorodovska
    * @create 2022-06-29-22:30
    */
   public class User {
       private Integer uid;
       private String username;
       private String password;
       private String address;
   
       public Integer getUid() {
           return uid;
       }
   
       public void setUid(Integer uid) {
           this.uid = uid;
       }
   
       public String getUsername() {
           return username;
       }
   
       public void setUsername(String username) {
           this.username = username;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   }
   ```

2. 配置实体类和数据库表一一对应关系（`xxx.hbm.xml`）

   ```xml
   <!DOCTYPE hibernate-mapping PUBLIC
           "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
           "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
   <hibernate-mapping>
       <!--类和表的映射-->
       <class name="com.sko.entity.User" table="t_user">
           <!--id标签
           name属性：实体类里面ID名称
           column属性：生成的表字段名称-->
           <id name="uid" column="uid">
               <!--id增长策略
               native：生成表ID值就是主键自增-->
               <generator class="native"/>
           </id>
           <!--属性映射-->
           <property name="username" column="username"/>
           <property name="password" column="password"/>
           <property name="address" column="address"/>
       </class>
   </hibernate-mapping>
   ```

3. 创建Hibernate配置文件（`hibernate.cfg.xml`）

   ```xml
   <?xml version='1.0' encoding='utf-8'?>
   <!DOCTYPE hibernate-configuration PUBLIC
       "-//Hibernate/Hibernate Configuration DTD//EN"
       "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
   <hibernate-configuration>
     <session-factory>
       <!-- 第一部分：配置数据库信息（必须的） -->
       <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
       <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/mybase</property>
       <property name="hibernate.connection.username">root</property>
       <property name="hibernate.connection.password">123456</property>
   
       <!-- 第二部分：配置hibernate信息（可选的） -->
       <!-- 输出底层sql语句 -->
       <property name="hibernate.show_sql">true</property>
       <!-- 输出底层sql语句格式化 -->
       <property name="hibernate.format_sql">true</property>
       <!-- 自动创建表 需要配置之后
           update：有表更新，没有表创建-->
       <property name="hibernate.hbm2ddl.auto">update</property>
       <!-- hibernate的方言
            让hibernate框架识别不同数据库的语句-->
       <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
   
       <!-- 第三部分：把映射文件放到核心配置文件中（必须的） -->
       <mapping resource="com/sko/entity/User.hbm.xml"/>
     </session-factory>
   </hibernate-configuration>
   ```

### 实现添加操作

1. 加载hibernate核心配置文件
2. 创建`SessionFactory`对象
3. 使用`SessionFactory`对象创建session对象
4. 开启事务
5. 具体逻辑CRUD操作
6. 提交事务
7. 关闭资源

