---
title: android持久化存储之LitePal数据库
author: lhmachine
date: 2018-09-05 17:37
categories:
- android
tags:
- android
- 持久化存储
- 数据库
- LitePal
---

Android同时支持另一种名为LitePal的数据库存储方式。LitePal是一款开源的Android数据库框架，采用对象关系映射(ORM)的模式，并将我们平时开发最常用的一些数据库功能进行了封装，使得不用编写一行SQL语句就可以完成各种建表和增删改查的操作。该项目开源地址如下:https://github.com/LitePalFramework/LitePal。

# 配置数据库

1. 编辑`app/build.gradle`文件，在`dependencies`闭包中添加如下内容：

   ```java
   compile 'org.litepal.android:core:1.3.2'
   ```

2. 在`app/src/main`下新建一个`assets`目录， 目录下新建一个`litepal.xml`文件，内容如下：

   ```java
   <?xml version="1.0" encoding="utf-8">
   <litepal>
   	<dbname value="数据库名"></dbname>
   	<version value="版本号"></version>
   	<list>
   	</list>
   </litepal>
   ```

   `<dbname>`标签用于指定**数据库名**，`<version>`标签用于指定**版本号**，`<list>`标签用于指定所有的映射模型。

3. 在`Manifest.xml`文件中，配置项目的`<application>`，如下

   ```java
   ...
   <application
   	android:name="org.litepal.LitePalApplication"
       ...>
   </application>
   ...
   ```

# 创建和升级数据库

## 创建

新建一个类，在`litepal.xml`中添加映射模型，如下所示。

```java
<list>
	<mapping class="完整类名"></mapping>
</list>
```

调用`Connector.getDatabase()`方法就是一次最简单的数据库操作。

## 升级

直接增加类，添加映射模型，或者修改类中代码。

# CRUD操作

## Create——添加数据

1. 创建一个数据类实例；
2. 调用类中的各种设置方法对数据进行设置；
3. 调用类集成自`DataSupport`类的`save()`方法即可。

## Update——更新数据

1. 新建一个数据类，设置要更新的数据；
2. 调用`updateAll()`方法，可以指定一个条件约束，如果不指定条件语句，则更新所有数据；

## Delete——删除数据

调用`DataSupport.deleteAll()`方法，该方法接收如下的参数：

1. 指定删除的表；
2. 约束条件；
3. 约束条件中占位符的值；

## Retrieve——查询数据

提供的有`DataSupport.findAll()`方法，直接返回的一个类的List集合，同时还有`DataSupport.findFirst()`和`DataSupport.findLast()`方法。

同时，`DataSupport`类还可以连缀查询来定制更多的查询功能：

1. `select()`方法：指定查询的哪几列数据；
2. `where()`方法：指定查询的约束条件；
3. `order()`方法：指定结果的排序方式；
4. `limit()`方法：指定查询结果的数量；
5. `offset()`方法：指定查询结果的偏移量；

也还可以调用`DataSupport.findBySQL()`方法来进行原生查询，第一个参数用于指定SQL语句，后面指定占位符的值，返回的是一个Cursor对象。