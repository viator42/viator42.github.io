---
layout: post
title:  "ViewStub用法笔记"
date:   2018-1-24
categories: Android
---

### 定义    

ViewStub，惰性装载控件。ViewStub是一个无形的、零大小的视图，可以在程序运行的过程中，通过懒加载的模式inflate进布局资源中。当一个ViewStub的inflate()方法被调用或者被设为显示时，这个ViewStub使用设定的View才会被加载，并替换当前ViewStub的位置。因此，ViewStub存在于视图层次，直到setVisibility(int)或inflate()方法被调用，否则是不加载控件的，所以消耗的资源小。通常也叫它为“懒惰的include”。

ViewStub的好处是，在某些场景中，并不一定需要把所有的内容都展示出来，可以隐藏一些View视图，待用户需要展示的时候再加载到当前的Layout中，这个时候就可以用到ViewStub这个控件了，这样可以减少资源的消耗，使最初的加载速度变快。

### 使用

__需要占位的View__

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <EditText
            android:id="@+id/username"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:ems="10"
            android:hint="username"
            android:inputType="textPersonName" />

        <EditText
            android:id="@+id/password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:ems="10"
            android:hint="password"
            android:inputType="textPassword" />

        <Button
            android:id="@+id/lgon"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="login" />
    </LinearLayout>

__ViewStub__

    <ViewStub
        android:id="@+id/stub"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/show_stub"
        android:layout="@layout/item_to_stub" />

* layout属性指定需要占位的View

__Activity.xml__

    使用inflate()方法来加载layout

    private ViewStub viewStub;
    viewStub = (ViewStub) findViewById(R.id.stub);

    View toStub  = viewStub.inflate();
    usernameEditText = (EditText) toStub.findViewById(R.id.username);
    passwordEditText = (EditText) findViewById(R.id.password);

* 在使用ViewStub的过程中，有一点需要特别注意。对于一个ViewStun而言，当setVisibility(int)或inflate()方法被调用之后，这个ViewStub在布局中将被使用指定的View替换，所以inflate过一遍的ViewStub，如果被隐藏之后再次想要显示，将不能使用inflate()方法，但是可以再次使用setVisibility(int)方法设置为可见，这就是这两个方法的区别。