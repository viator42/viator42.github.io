---
layout: post
title:  "Android开发中iOS like样式对应方法"
date:   2018-5-28
categories: Android
---

## 圆角按钮

创建res/drawable/round_rect_btn.xml

    <?xml version="1.0" encoding="utf-8"?>
    <shape xmlns:android="http://schemas.android.com/apk/res/android"
        android:shape="rectangle"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <!-- 填充的颜色 -->
        <solid android:color="#b4d465" />
        <!-- 设置按钮的四个角为弧形 -->
        <!-- android:radius 弧形的半径 -->
        <corners android:radius="16dp" />

        <!-- padding：Button里面的文字与Button边界的间隔 -->
        <padding
            android:left="10dp"
            android:top="10dp"
            android:right="10dp"
            android:bottom="10dp"/>
    </shape>

Button中设置background属性

    <Button
    android:id="@+id/btn"
    android:background="@drawable/round_rect_btn" />

## ToolBar title居中设置

在ToolBar中定义一个TextView并设置居中,然后设置其layout_gravity为center,最后把ToolBar自带title设为空值

AndroidMainfest.xml

    <activity
        android:name=".activity.HelpActivity"
        android:label="@string/title_activity_help"
        android:launchMode="singleTop"
        android:screenOrientation="portrait"
        android:theme="@style/AppTheme.NoActionBar"
        />

actitity_mian.xml

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        app:popupTheme="@style/AppTheme.PopupOverlay"
        app:title="">

        <TextView
            android:id="@+id/toolbar_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:textColor="@color/text_white"
            android:textSize="20sp" />

    </android.support.v7.widget.Toolbar>

MianActivity.java

    Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
    toolbar.setTitle("");
    TextView toolbarTitleTextView = (TextView) findViewById(R.id.toolbar_title);
    toolbarTitleTextView.setText("我是标题");
    toolbar.setNavigationIcon(R.drawable.ic_arrow_left_white_24dp);
    toolbar.setNavigationOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            onBackPressed();
        }
    });
    setSupportActionBar(toolbar);
    
## BottomBar样式对应

底部Tab在Support Library中已经有对应方案,使用BottomNavigationView    
需要解决的问题是去掉当item大于3的时候，使用shift mode模式    

__解决方法__

创建Helper类

    public class BottomNavigationViewHelper {
        public static void disableShiftMode(BottomNavigationView view) {
            BottomNavigationMenuView menuView = (BottomNavigationMenuView) view.getChildAt(0);
            try {
                Field shiftingMode = menuView.getClass().getDeclaredField("mShiftingMode");
                shiftingMode.setAccessible(true);
                shiftingMode.setBoolean(menuView, false);
                shiftingMode.setAccessible(false);
                for (int i = 0; i < menuView.getChildCount(); i++) {
                    BottomNavigationItemView item = (BottomNavigationItemView) menuView.getChildAt(i);
                    //noinspection RestrictedApi
                    item.setShiftingMode(false);
                    // set once again checked value, so view will be updated
                    //noinspection RestrictedApi
                    item.setChecked(item.getItemData().isChecked());
                }
            } catch (NoSuchFieldException e) {
                Log.e("BNVHelper", "Unable to get shift mode field", e);
            } catch (IllegalAccessException e) {
                Log.e("BNVHelper", "Unable to change value of shift mode", e);
            }
        }
    }

使用

    BottomNavigationView navigation = (BottomNavigationView) findViewById(R.id.navigation);
    BottomNavigationViewHelper.disableShiftMode(navigation);

