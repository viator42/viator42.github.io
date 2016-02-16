---
layout: post
title:  "Android SQLite 数据库CRUD操作笔记"
date:   2016-02-16
categories: android
---

Android使用SQLiteOpenHelper对数据库操作进行了封装,使用的时候需自定义一个类并继承他.

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
    
需要重写onCreate方法,完成数据表初始化操作.

### 数据库操作

先定义一个SQLiteOpenHelper对象.

    private MyDbHelper myDbHelper = null;
    if(myDbHelper == null)
    {
        myDbHelper = new MyDbHelper(MainActivity.this, "users");

    }

添加数据

    ContentValues contentValues = new ContentValues();
    contentValues.put("id", 1);
    contentValues.put("name", "UserA");
    contentValues.put("age", 18);

    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    sqLiteDatabase.insert("users", null, contentValues);
    sqLiteDatabase.close();

更新数据

    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    sqLiteDatabase.execSQL("update users set name=?,age=? where id=?", new Object[]{"用户名1", 99, 1});
    sqLiteDatabase.close();
    
查询数据
    
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
    

删除数据
    
    SQLiteDatabase sqLiteDatabase = myDbHelper.getWritableDatabase();
    sqLiteDatabase.execSQL("delete from users where id = 1");
    sqLiteDatabase.close();
    