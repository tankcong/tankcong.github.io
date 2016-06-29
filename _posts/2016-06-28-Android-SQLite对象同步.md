---
layout: post
title: Android SQLiteDatabase对象同步的重要性
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
    - Java
---


## SQLiteDatabase 对象同步

考虑以下代码：

```
    SQLiteDatabase db = ···; 
    db.beginTransaction();
    try {
         ...
         db.setTransactionSuccessful();
    } finally {
        db.endTransaction();
    }
```  

[SQLiteDatabase](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.4_r1.2/android/database/sqlite/SQLiteDatabase.java?av=f#637) 中：

```
private void beginTransaction(SQLiteTransactionListener transactionListener,
            boolean exclusive) {
        verifyDbIsOpen();
        lockForced(BEGIN_SQL);  //mLock.tryLock(···)  
       ...
}

```

-----------------------------------------

```
public void endTransaction() {
    ...
    try{ 
        ...
    } finally {

         unlockForced(); //mLock.unLock();
    }
    ...
}

```
-------------------------------------------

```
private final DatabaseReentrantLock mLock = new DatabaseReentrantLock(true);

```

-------------------------------------------

因此如果SQLiteDatabase对象不唯一，则无法同步数据库写操作。
考虑`INSERT`方法，如果db对象不唯一，则：

1. 若打开数据库时冲突，则报:
   ```
   >>  E/SQLiteDatabase(7851): Failed to open the database. closing it.
   >>  E/SQLiteDatabase(7851): android.database.sqlite.SQLiteDatabaseLockedException: database is locked
   ```
   **程序异常退出**；

2. 若在插入数据时冲突，则只报:
   ```
    E/SQLiteDatabase(8419): Error inserting data=4 name=test name
    E/SQLiteDatabase(8419): android.database.sqlite.SQLiteDatabaseLockedException: database is locked
    E/SQLiteDatabase(8419):  at android.database.sqlite.SQLiteStatement.native_executeSql(Native Method)
   ```
   **程序只会在日志输出错误，并且更新数据失败，应用得不到任何通知**；


### 结论

* 如果db对象唯一，则同步有效，在多线程下insert()数据时可以插入成功，但不保证顺序。
* 如果db对象唯一，采用transaction插入数据，则可以插入成功，并保证插入顺序，即在一个事物结束后再开启等待中的事物。


 