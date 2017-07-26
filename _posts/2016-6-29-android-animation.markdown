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
        <set xmlns:android="http://schemas.android.com/apk/res/android" >
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
    Animation animation = AnimationUtils.loadAnimation(AnimationActivity.this, R.anim.anim_tester_1);
    //view上使用动画
    view_item.startAnimation(animation) ;

### Activity 跳转动画

在startActivity后面调用overridePendingTransition,两个参数代表新Activty进入和旧Activty退出的动画,两个动画注意时间必须相等

    Intent intent = new Intent(MainActivity.this, DestActivity.class);
    startActivity(intent);
    overridePendingTransition(R.anim.wrap_enter_in, R.anim.wrap_enter_out);

Activty退出的时候也可以使用动画,在onBackPressed方法中使用

    public void onBackPressed() {
        super.onBackPressed();
        overridePendingTransition(R.anim.wrap_exit_in,R.anim.wrap_exit_out);
    }

#### 动画示例

新Activity向左滑动进入,向右滑动退出

进入动画

    wrap_enter_in.xml
    <translate
        android:duration="250"
        android:fromXDelta="0%p"
        android:toXDelta="-100%p"
        android:fillAfter="true"/>

    wrap_enter_out.xml
    <translate
        android:duration="250"
        android:fromXDelta="100%p"
        android:toXDelta="0%p"
        android:fillAfter="true"/>

退出动画

    wrap_exit_in.xml
    <translate
        android:duration="250"
        android:fromXDelta="-100%p"
        android:toXDelta="0%p"
        android:fillAfter="true"/>

    wrap_exit_out.xml
    <translate
        android:duration="250"
        android:fromXDelta="0%p"
        android:toXDelta="100%p"
        android:fillAfter="true"/>

### 多重动画

第一个动画设置停止事件播放第二个动画

    Animation animation = AnimationUtils.loadAnimation(this, R.anim.scale);
            iv.startAnimation(animation);
            final Animation animation2 = AnimationUtils.loadAnimation(this, R.anim.rotate);
            animation.setAnimationListener(new Animation.AnimationListener() {
                @Override
                public void onAnimationStart(Animation animation) {

                }

                @Override
                public void onAnimationEnd(Animation animation) {

                    iv.startAnimation(animation2);
                }

                @Override
                public void onAnimationRepeat(Animation animation) {

                }
            });

### 逐帧动画

使用方法

1.动画所用的图片导入res/drawable中

2.res/drawable创建动画定义文件    

    <animation-list xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/m_1" android:duration="100" />
        <item android:drawable="@drawable/m_2" android:duration="100" />
        <item android:drawable="@drawable/m_3" android:duration="100" />
        <item android:drawable="@drawable/m_4" android:duration="100" />
        <item android:drawable="@drawable/m_5" android:duration="100" />
    </animation-list>

3.Activity中使用

    frameAnimImageView = (ImageView) findViewById(R.id.frame_anim);
        frameAnimImageView.setBackgroundResource(R.drawable.anim_frame);
        frameAnimImageView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                AnimationDrawable animationDrawable = (AnimationDrawable) frameAnimImageView.getBackground();
                animationDrawable.start();

            }
        });