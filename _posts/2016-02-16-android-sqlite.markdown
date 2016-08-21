---
layout: post
title:  "Android SQLite 数据库CRUD操作笔记"
date:   2016-02-16
categories: android
---

### 数据库初始化

Android使用SQLiteOpenHelper对数据库操作进行了封装,使用的时候需自定义一个类并继承他.
onCreate方法中实现数据库的初始化,创建表等操作.

    public class MyDbHelper extends SQLiteOpenHelper {
        public MyDbHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
            super(context, name, factory, version);
        }

        public MyDbHelper(Context context, String name){
            this(context, name, null, 1);
        }

        @Override
        public void onCreate(SQLiteDatabase db) {
            db.execSQL("create table if not exists users("
                    + "id integer primary key,"
                    + "name varchar,"
                    + "age integer)");

        }

        @Override
        public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

        }
    }

### 数据库操作

#### 创建SQLiteOpenHelper对象

先定义一个SQLiteOpenHelper对象,一般在Application类的onCreate方法中实现

    private MyDbHelper myDbHelper = null;
    if(myDbHelper == null)
    {
        myDbHelper = new MyDbHelper(MainActivity.this, "users");
    }

#### 添加数据

    ContentValues contentValues = new ContentValues();
    contentValues.put("id", 1);
    contentValues.put("name", "UserA");
    contentValues.put("age", 18);

    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    long id = sqLiteDatabase.insert("users", null, contentValues);  //返回插入行的id
    sqLiteDatabase.close();

#### 查询数据
    
    SQLiteDatabase sqLiteDatabase = myDbHelper.getReadableDatabase();
    Cursor cursor = sqLiteDatabase.rawQuery("select * from users", null);

    while(cursor.moveToNext()){
        int id = cursor.getInt(cursor.getColumnIndex("id"));
        String uname=cursor.getString(cursor.getColumnIndex("name"));
        int age = cursor.getInt(cursor.getColumnIndex("age"));

        Log.v("db data", id +" "+ uname + " " + age);

        User user = new User();
        user.setUid(id2);
        user.setUname(name);
        user.setAge(age);
        users.add(user);
    }

#### 更新数据
    
    有两种方法,一种是拼接SQL语句

    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    sqLiteDatabase.execSQL("update users set name=?,age=? where id=?", new Object[]{"用户名1", 99, 1});
    sqLiteDatabase.close();

    另一种是使用update方法传入ContentValues参数

    SQLiteDatabase sqLiteDatabase = eDbHelper.getWritableDatabase();
    ContentValues contentValues = new ContentValues();
    contentValues.put("name", schedule.name);
    contentValues.put("comment", schedule.comment);
    contentValues.put("money", schedule.money);
    contentValues.put("type", schedule.type);
    contentValues.put("feq", schedule.feq);
    contentValues.put("feq_value", schedule.feqValue);
    contentValues.put("alarm_time", schedule.alarmTime);
    contentValues.put("income_spend", schedule.incomeSpend);
    sqLiteDatabase.update("schedule", contentValues, "id=?", new String[]{String.valueOf(schedule.id)});
    sqLiteDatabase.close();

#### 删除数据
    
    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    sqLiteDatabase.execSQL("delete from users where id = 1");   //直写SQL语句实现
    //sqLiteDatabase.delete("schedule", "id=?", new String[]{String.valueOf(id)});  //delete方法实现
    sqLiteDatabase.close();
    