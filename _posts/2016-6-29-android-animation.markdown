---
layout: post
title:  "Android 动画效果"
date:   2016-06-29
categories: android
---

Android动画分为View动画,帧动画和视图动画    

---------

## 视图动画
View动画只支持平移,缩放,旋转,透明度的变换效果,通过状态的渐变来产生动画效果.        
View动画有两种定义方法,一种是xml定义,另一种是通过代码生成    

### xml定义    
在res/anim下创建动画定义的xml文件,格式如下    

    <? xml version="1.0" encoding= "utf-8"?>
        <set xmlns:android="http://schemas.android.com/apk/res/android" >
    </set>

#### 位置移动(TranslateAnimation)

    <translate
    android :fromXDelta="10"     //初始位置X轴
    android :fromYDelta="10"     //初始位置Y轴
    android :toXDelta="100"     //结束位置X轴
    android :toYDelta="100"     //结束位置Y轴
    android :duration="200"     //动画时长
    android :fillAfter="true"     //动画结束时是否返回起始位置
    />

#### 旋转(RotateAnimation)

    <rotate
    android :fromDegrees="0"     //初始角度
    android :toDegrees="+270"     //结束角度 +表示顺时针 -逆时针
    android :pivotX="50%"          //旋转中心点X轴
    android :pivotY="50%"          //旋转中心点Y轴
    android :duration="200"
    android :fillAfter="true"
    />

#### 尺寸变化(ScaleAnimation)

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

#### 透明度变化(AlphaAnimation)

    <alpha
    android :fromAlpha="0.1"     //初始透明度
    android :toAlpha="0.1"          //结束透明度
    android :duration="200"        
    android :fillAfter="true"
    />

#### 使用

    //创建动画
    Animation animation = AnimationUtils.loadAnimation(AnimationActivity.this, R.anim.anim_tester_1);
    //view上使用动画
    view_item.startAnimation(animation) ;

另一种是在代码中定义动画

1.位置移动(TranslateAnimation)

    ScaleAnimation scaleAnimation = new ScaleAnimation(1.0f, 2.0f, 1.0f, 2.0f);
    scaleAnimation.setDuration(200);
    v.startAnimation(scaleAnimation);

2.旋转(RotateAnimation)

    TranslateAnimation translateAnimation = new TranslateAnimation(10, 100, 10, 100);
    translateAnimation.setDuration(200);
    v.startAnimation(translateAnimation);

3.尺寸变化(ScaleAnimation)

    RotateAnimation rotateAnimation = new RotateAnimation(0, 90);
    rotateAnimation.setDuration(200);
    v.startAnimation(rotateAnimation);

4.透明度变化(AlphaAnimation)

    AlphaAnimation alphaAnimation = new AlphaAnimation(1f, 0.5f);
    alphaAnimation.setDuration(200);
    v.startAnimation(alphaAnimation);

__View动画可以在ViewGroup中控制子元素的出场效果,或者Activity之间的切换效果__

#### Activity跳转的动画效果

在startActivity后面调用overridePendingTransition,两个参数代表新Activty进入和旧Activty退出的动画,两个动画注意时间必须相等

    Intent intent = new Intent(MainActivity.this, DestActivity.class);
    startActivity(intent);
    overridePendingTransition(R.anim.wrap_enter_in, R.anim.wrap_enter_out);

Activty退出的时候也可以使用动画,在onBackPressed方法中使用

    public void onBackPressed() {
        super.onBackPressed();
        overridePendingTransition(R.anim.wrap_exit_in,R.anim.wrap_exit_out);
    }

__示例__

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

--------

## 属性动画

Android 3.0之后出现了属性动画.    
视图动画(补间动画)只能作用于View对象,而且只有移动,旋转,尺寸变化,透明度四种效果.只改变View的显示外观不能修改属性
属性动画是通过对目标对象不断的修改属性来实现动画效果

__有关类__

ValueAnimator    
属性动画的运行机制是通过不断地对值进行操作来实现的，通过不断控制值的变化，再不断手动赋给对象的属性，从而实现动画效果。而初始值和结束值之间的动画过渡就是由ValueAnimator这个类来负责计算的。相当于一个计数器

    ValueAnimator valueAnimator = ValueAnimator.ofInt(0,100);
    valueAnimator.setDuration(1000);
    valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
        @Override
        public void onAnimationUpdate(ValueAnimator animation) {
            Log.v("AnimateValue", String.valueOf(animation.getAnimatedValue()));
        }
    });
    valueAnimator.start();

ValueAnimator类    
属性动画机制中 最核心的一个类    
实现动画的原理：通过不断控制 值 的变化，再不断 手动 赋给对象的属性，从而实现动画效果。

ValueAnimatior类的方法

ofInt()       
ofFloat()
ofObject()    

ifInt(0, 100, 0) 参数为任意多个整形数,赋给对象的值在这些范围内变化,从开始值过渡到结束值,ofFloat同理

ofObject() 将初始值以对象的形式过渡到结束值

使用object类型作为动画数值范围需要自定义TypeEvaluator(估值器)

    TypeEvaluator<Point> typeEvaluator = new TypeEvaluator<Point>() {
        @Override
        public Point evaluate(float fraction, Point startValue, Point endValue) {
            long newX = (long) (fraction * (endValue.x - startValue.x));
            long newY = (long) (fraction * (endValue.y - startValue.y));

            return new Point(newX, newY);
        }
    };

    Point p1 = new Point(0, 0);
    Point p2 = new Point(30, 50);
    ValueAnimator valueAnimator1 = ValueAnimator.ofObject(typeEvaluator, p1, p2);
    valueAnimator1.setDuration(1000);
    valueAnimator1.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
        @Override
        public void onAnimationUpdate(ValueAnimator animation) {
            Point point = (Point) animation.getAnimatedValue();
            Log.v("ValueAnimator", point.toString());

        }
    });
    valueAnimator1.start();

--------

ObjectAnimator    
对任意的对象进行动画操作的类

    ObjectAnimator animator = ObjectAnimator.ofFloat(destTextView, "alpha", 1f, 0f, 1f);
    animator.setDuration(5000);
    animator.start();

参数: 执行动画的view, 改变的属性, 之后的数字是属性值的变化列表,属性值会按次序改变

ObjectAnimator与 ValueAnimator类的区别：    

* ValueAnimator 类是先改变值，然后 手动赋值 给对象的属性从而实现动画；是 间接 对对象属性进行操作；
* ObjectAnimator 类是先改变值，然后 自动赋值 给对象的属性从而实现动画；是 直接 对对象属性进行操作；

### 组合动画AnimationSet类

* AnimatorSet.play(Animator anim) ：播放当前动画 
* AnimatorSet.after(long delay) ：将现有动画延迟x毫秒后执行 
* AnimatorSet.with(Animator anim) ：将现有动画和传入的动画同时执行 
* AnimatorSet.after(Animator anim) ：将现有动画插入到传入的动画之后执行 
* AnimatorSet.before(Animator anim) ： 将现有动画插入到传入的动画之前执行

组合动画使用

AnimatorSet animSet = new AnimatorSet();    //创建Animationset  
animSet.play(anim1).with(anim2).before(anim3);  //组合播放动画的策略
animSet.setDuration(5000);  //播放动画
animSet.start();

或者说在xml文件中定义动画集合

__set_animation.xml__

    <set android:ordering="together" > 
        // 下面的动画同时进行 
        <objectAnimator 
            android:duration="2000" 
            android:propertyName="translationX" 
            android:valueFrom="0"
            android:valueTo="300"
            android:valueType="floatType" > 
        </objectAnimator>
        <objectAnimator 
            android:duration="3000" 
            android:propertyName="rotation"
            android:valueFrom="0" 
            android:valueTo="360" 
            android:valueType="floatType" > 
            </objectAnimator> 
    </set>

使用动画集合

    AnimatorSet animator = (AnimatorSet) AnimatorInflater.loadAnimator(this, R.animator.set_animation);
    animator.setTarget(targetView);
    animator.start();

---------

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

--------

## 逐帧动画

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

## LayoutAnimation

为ViewGroup指定的动画,当子元素展示的时候使用这个动画.常用在ListView上面.每个item出现的时候伴随着动画.

__使用__

在属性里直接定义

    <ListView
        ...
        android:layoutAnimation:@anim/layout_anim01
        >

