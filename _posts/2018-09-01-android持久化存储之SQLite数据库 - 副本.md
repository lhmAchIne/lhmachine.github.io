---
title: android持久化存储之SQLite数据库
author: lhmachine
date: 2018-09-01 01:10
categories:
- android
tags:
- android
- 持久化存储
- 数据库
- SQLite
---

Android支持两种数据库的存储：1. SQLite；2.LitePal；其中SQLite是Android内置的一款轻量级关系型数据库。

# 1 创建数据库

1. 重写`SQLiteOpenHelper`类；
2. 重写类中的两个抽象方法：(1) `onCreate()`创建数据库；(2) `onUpgrade()`升级数据库；
3. `SQLiteOpenHelper`类提供了两个构造方法可供重写，一般使用*参数少*的构造方法，接收四个参数：(1) `Context`；(2) 数据库名；(3) 允许我们在查询数据的时候返回一个自定义的`Cursor`，一般传入`null`；(4) 当前数据库的版本号；
4. `SQLiteOpenHelper`类提供了两种实例方法：(1) `getReadableDatabase()`方法；(2) `getWritableDatabase()`方法。这两种方法都可以创建或者打开一个数据库，并返回一个可对数据库进行读写的对象。**区别**：当数据库不可写入的时候（如内存空间已满），`getReadableDatabase()`方法返回的对象将以**只读**的形式打开数据库，`getWritableDatabase()`方法则将出现**异常**。

示例代码如下，创建一张人员表，包含姓名、性别、电话等属性。

```java
public class MyDatabaseHelper extends SQLiteOpenHelper{
    
    public static final String CREATE_PERSON = "create table Person(name text, sex text, tel text)";
    
    private Context mContext;
    
    //重写构造方法
    public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version){
        super(context, name, factory, version);
        mContext = context;
    }
    
    //创建数据库
    @Override
    public void onCreate(SQLiteDatabase db){
        db.execSQL(CREATE_PERSON);
        Toast.makeText(mContext, "创建成功", Toast.LENGTH_SHORT).show();
    }
    
    //升级数据库
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion){
        
    }
}
```

# 2 升级数据库

在`onUpgrade()`方法对数据库进行升级；

# 3 CRUD操作

要想对数据库进行CRUD操作，需要先获取可对数据库读写的对象。代码如下：

```java
MyDatabaseHelper dbHelper = new MyDatabaseHelper(this, "data", null, 1);
SQLiteDatabase db = dbHelper.getWritableDatabase();
```

## Create——增加数据

两种方法：

(1) `insert()`方法

该方法接收三个参数：1. **表名**；2. 用于在未指定添加数据的情况下给某些可为空的列自动赋值为`NULL`，一般传入`NULL`；3. `ContentValues`对象，该对象提供了一系列的`put()`方法重载，用于向`ContentValues`中添加数据。

如向我们已经创建的人员表中添加数据，代码如下：

```java
ContentValues values = new ContentValues();
//开始组装数据
values.put("name","张三");
values.put("sex","男");
values.put("tel","13713713713");
db.insert("data", null, values);
```

(2) `excelSQL()`方法

该方法只接收一个参数，即SQL语句。

## Retrieve——查询数据

两种方法：

(1) `query()`方法

该方法接收7个参数：1. **表名**；2. **列名**；3. 指定**`where`的约束条件**；4. 为`where`中的占位符提供具体的值；5. 指定需要`group by`的列； 6. 对`group by`后的**结果进一步约束**；7. 指定查询结果的约束方式。

查询如下：

```java
Cursor cursor = db.query("Person", null, null, null, null, null, null);
if (cursor.moveToFirst()){
    do{
        //遍历Cursor对象
        String name = cursor.getString(cursor.getColumnIndex("name"));
        String sex = cursor.getString(cursor.getColumnIndex("sex"));
        String tel = cursor.getString(cursor.getColumnIndex("tel"));
    }while(cursor.moveToNext());
}
cursor.close();
```

(2) `rawQuery()`方法

该方法接收两个参数：1. **SQL语句**；2. SQL语句中**占位符的值**。返回一个**`Cursor`**对象。

## Update——更新数据

两种方法

(1) `update()`方法

该方法接收4个参数：1. **表名**；2. **ContentValues**对象，把更新数据组装其中；3. 约束——用于约束更新某一行或某几行中的数据；4. 约束——若不指定，更新所有行。

代码如下：

```java
ContentValues values = new ContentValues();
values.put("sex", "女");
db.update("Data", values, "name=?", new String[]{"张三"};
```

(2) `execSQL()`方法

参数为SQL语句。

## Delete——删除数据

两种方法：

(1) `delete()`方法

该方法接收三个参数：1. **表名**；2. 约束；3. 约束；

代码如下：

```java
db.delete("Data", "name = ?", new String[]{"张三"});
```

(2) `execSQL()`方法

参数为SQL语句。