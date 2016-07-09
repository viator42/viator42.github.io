---
layout: post
title:  "Android 动画效果"
date:   2016-06-29
categories: android
---

### 动画分类    
* TranslateAnimation 位置移动
* RotateAnimation 旋转
* ScaleAnimation 尺寸变化
* AlphaAnimation 透明度变化

### 动画定义    
在res/anim下创建动画定义的xml文件,格式如下    

    <? xml version="1.0" encoding= "utf-8"?>
        <set xmlns: android="http://schemas.android.com/apk/res/android" >
    </set>

### 定义

#### TranslateAnimation

    <translate
    android :fromXDelta="10"     //初始位置X轴
    android :fromYDelta="10"     //初始位置Y轴
    android :toXDelta="100"     //结束位置X轴
    android :toYDelta="100"     //结束位置Y轴
    android :duration="200"     //动画时长
    android :fillAfter="true"     //动画结束时是否返回起始位置
    />

#### RotateAnimation

    <rotate
    android :fromDegrees="0"     //初始角度
    android :toDegrees="+270"     //结束角度 +表示顺时针 -逆时针
    android :pivotX="50%"          //旋转中心点X轴
    android :pivotY="50%"          //旋转中心点Y轴
    android :duration="200"
    android :fillAfter="true"
    />

#### ScaleAnimation

    <scale
    android :fromXScale="1.0"     //初始比例X轴
    android :fromYScale="1.0"     //初始比例Y轴
    android :toXScale="2.0"          //结束比例X轴
    android :toYScale="2.0"          //结束比例Y轴
    android :pivotX="50%"          //缩放中心点X轴
    android :pivotY="50%"          //缩放中心点Y轴
    android :duration="200"
    android :fillAfter="true"
    />

#### AlphaAnimation

    <alpha
    android :fromAlpha="0.1"     //初始透明度
    android :toAlpha="0.1"          //结束透明度
    android :duration="200"        
    android :fillAfter="true"
    />

### 使用

    //创建动画
    Animation animation = animation = AnimationUtils. loadAnimation(AnimationActivity. this, R.anim. anim_tester_1 );
    //view上使用动画
    view_item.startAnimation( animation) ;

