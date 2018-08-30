---
title: android持久化存储之SharedPreferences
author: lhmachine
date: 2018-08-30 10:56
categories:
- android
tags:
- android
- 持久化存储
- SharedPreferences
---

不同于[文件存储](https://blog.lhmachine.cn/android/2018/07/02/android%E6%8C%81%E4%B9%85%E5%8C%96%E5%AD%98%E5%82%A8%E4%B9%8B%E6%96%87%E4%BB%B6%E5%AD%98%E5%82%A8/#)，`SharedPreferences`是使用**键值对**的方式来存储数据的。在保存一条数据时，需要给这条数据提供一个对应的键，这样在读取的时候可以通过键将对应的数据提取出来。并且，`SharedPreferences`还支持多种不同的数据类型存储。

# 1. `SharedPreferences`对象

Android中提供了三种方法获取`SharedPreferences`对象：

**（1） `Context`类的`getSharedPreferences()`方法**

该方法接收两个参数：

- 指定**`SharedPreferences`文件的名称**，存放在`/data/data/<package name>/shared-prefs`目录下；
- 指定**操作模式**，只有`MODE_PRIVATE`模式，表示只有当前的应用程序才可以进行读写。

**（2）`Activity`类中的`getPreferences()方法`**

该方法只接收**一个操作模式参数**，自动将当前活动的类名作为`SharePreferences`文件名。

**（3）`PreferenceManager`类中的`getDefaultSharedPreferences()`方法**

**静态**方法。接收一个**`Context`参数**，自动使用当前应用程序的包名作为前缀来命名`SharedPreferences`文件。

# 2. 数据存储

1. 调用`SharedPreferences`对象的`edit()`方法来获取`SharedPreferences.Editor`对象；
2. 向`SharedPreferences.Editor`对象中添加数据；
3. 调用`appy()`方法将添加的数据提交，从而完成数据的存储操作；

说明：`SharedPreferences.Editor`对象提供了`putString()`，`putInt()`，`putBoolean()`等方法，支持添加数据，这些方法接收两个参数，第一个为*键名*，第二个为*键值*。

代码如下：

```java
SharedPreferences.Editor editor = getSharedPreferences("data", MODE_PRIVATE).edit();
editor.putString("name", "Tom");
editor.apply();
```

# 3. 数据读取

`SharedPreferences`对象中提供了一系列的`get`方法或获取数据，如`getString()`，`getInt()`，`getBoolean()`等，此类方法接收两个参数，第一个为*键名*，第二个为*默认值*。

代码如下：

```java
SharedPreferences pref = getSharedPreferences("data", MODE_PRIVATE);
String name = pref.getString("name", "");
```

