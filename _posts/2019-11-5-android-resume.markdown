---
layout: post
title:  "Android面试相关总结"
date:   2019-11-5
categories: android
---

# Java基础

[Java基础相关笔记](/java/2018/02/24/java-notes/)

--------

# Android基础

[Android基础相关笔记](/android/2015/04/04/android-note/)

--------

# 四大组件的原理

[四大组件的原理](/android/2017/05/06/android-activity-service-broadcastservice-contentprovider/)

--------

# 题目

### 什么是Activity

四大组件之一,一般的,一个用户交互界面对应一个activity    
setContentView() ,// 要显示的布局     
activity 是Context的子类,同时实现了window.callback和keyevent.callback, 可以处理与窗体用户交互的事件.     
我开发常用的的有FragmentActivitiyListActivity  ,PreferenceActivity ,TabAcitivty等    

### 请描述一下Activity 生命周期

Activity从创建到销毁有多种状态，从一种状态到另一种状态时会激发相应的回调方法，这些回调方法包括：onCreate onStart onResume onPause onStop onDestroy    
其实这些方法都是两两对应的，onCreate创建与onDestroy销毁；    
onStart可见与onStop不可见；onResume可编辑（即焦点）与onPause；    
如果界面有共同的特点或者功能的时候,还会自己定义一个BaseActivity.     

### Service的生命周期

### 如何保存Activity的状态或者(Activiy 重启怎么保存数据？

Activity的状态通常情况下系统会自动保存的，只有当我们需要保存额外的数据时才需要使用到这样的功能。
一般来说, 调用onPause()和onStop()方法后的activity实例仍然存在于内存中, activity的所有信息和状态数据不会消失, 当activity重新回到前台之后, 所有的改变都会得到保留。
但是当系统内存不足时, 调用onPause()和onStop()方法后的activity可能会被系统摧毁, 此时内存中就不会存有该activity的实例对象了。如果之后这个activity重新回到前台, 之前所作的改变就会消失。为了避免此种情况的发生, 我们可以覆写onSaveInstanceState()方法。onSaveInstanceState()方法接受一个Bundle类型的参数, 开发者可以将状态数据存储到这个Bundle对象中, 这样即使activity被系统摧毁, 当用户重新启动这个activity而调用它的onCreate()方法时, 上述的Bundle对象会作为实参传递给onCreate()方法, 开发者可以从Bundle对象中取出保存的数据, 然后利用这些数据将activity恢复到被摧毁之前的状态。
需要注意的是, onSaveInstanceState()方法并不是一定会被调用的, 因为有些场景是不需要保存状态数据的. 比如用户按下BACK键退出activity时, 用户显然想要关闭这个activity, 此时是没有必要保存数据以供下次恢复的, 也就是onSaveInstanceState()方法不会被调用. 如果调用onSaveInstanceState()方法, 调用将发生在onPause()或onStop()方法之前。

### 如何退出Activity？如何安全退出已调用多个Activity的Application？

1. Application中定义Activity列表,应用关闭的时候遍历这个列表关闭所有的Activity
2. 在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。
3. 在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。

### Activity的启动模式

singleTop 跟standard 模式比较类似。唯一的区别就是，当跳转的对象是位于栈顶的activity（应该可以理解为用户眼前所 看到的activity）时，程序将不会生成一个新的activity实例，而是直接跳到现存于栈顶的那个activity实例。拿上面的例子来说，当Act1 为 singleTop 模式时，执行跳转后栈里面依旧只有一个实例，如果现在按返回键程序将直接退出。
	singleTask模式和singleInstance模式都是只创建一个实例的。在这种模式下，无论跳转的对象是不是位于栈顶的activity，程序都不会生成一个新的实例（当然前提是栈里面已经有这个实例）。这种模式相当有用，在以后的多activity开发中，常会因为跳转的关系导致同个页面生成多个实例，这个在用户体验上始终有点不好，而如果你将对应的activity声明为singleTask 模式，这种问题将不复存在。在主页的Activity很常用



