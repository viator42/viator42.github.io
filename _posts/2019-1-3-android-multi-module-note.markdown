---
layout: post
title:  "Android组件化架构笔记"
date:   "2019-1-3"
categories: Android
---

## 项目添加新的模块

点击new module -> Android Library, 模块名称base

修改settings.gradle

    include ':app', ':base'

app模块中引用base模块，修改build.gradle

    dependencies {
        。。。
        implementation project(':base')
    }

--------

### Application Module，Android Library，Java Library的区别

android application module以作为一个可以运行的应用，Android Library，Java Library相当于被调用的库。区别是是否带有Android SDK。   
一个项目中可以有多个android application module，每一个都可以作为单独的程序运行。 
app module 和 library module 以及 java module 主要区别在于生成内容的不同，app module 生成 apk 程序文件。library module 生成 aar 文件，java module 生成 jar 文件。 aar 和 jar 文件都可以作为 app 的依赖库，主要区别在于 aar 除了能携带编译好的程序以外，还能携带资源，是对jar文件的一个提升。    

--------

## 组件之间的通信方式

### LocalBoardcast 本地广播

App关闭的时候发送一个本地广播通知所有Activity关闭

### EventBus

事件总线。在Activity，Fragment，Service之间传递数据使用，用于代替Handler looper回调

EventBus是基于观察者模式构建的，事件是观察者。

#### EventBus的概述

__三要素__

* Event     事件。它可以是任意类型。
* Subscriber 事件订阅者。在EventBus3.0之前我们必须定义以onEvent开头的那几个方法，分别是onEvent、onEventMainThread、onEventBackgroundThread和onEventAsync，而在3.0之后事件处理的方法名可以随意取，不过需要加上注解@subscribe()，并且指定线程模型，默认是POSTING。
* Publisher 事件的发布者。我们可以在任意线程里发布事件，一般情况下，使用EventBus.getDefault()就可以得到一个EventBus对象，然后再调用post(Object)方法即可。

__四种线程模型__

* POSTING   (默认) 表示事件处理函数的线程跟发布事件的线程在同一个线程。
* MAIN      表示事件处理函数的线程在主线程(UI)线程，因此在这里不能进行耗时操作。
* BACKGROUND 表示事件处理函数的线程在后台线程，因此不能进行UI操作。如果发布事件的线程是主线程(UI线程)，那么事件处理函数将会开启一个后台线程，如果果发布事件的线程是在后台线程，那么事件处理函数就使用该线程。
* ASYNC     表示无论事件发布的线程是哪一个，事件处理函数始终会新建一个子线程运行，同样不能进行UI操作。

#### 使用实例

0.build.gradle导入第三方库

    dependencies {
        。。。
        implementation 'org.greenrobot:eventbus:3.1.1'
    }

1.定义事件消息类，就是一个普通的POJO类

    public class EventMsg {
        public long id;
        public String msg;
    }

2.注册订阅者

一般在对象创建的时候注册订阅者，对象销毁的时候注销订阅

    public class EventBusTesterActivity extends AppCompatActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            。。。
            EventBus.getDefault().register(this);
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            EventBus.getDefault().unregister(this);
        }
    }

2.发送事件消息

    EventMsg eventMsg = new EventMsg();
    eventMsg.id = 1;
    eventMsg.msg = "This is event msg";

    EventBus.getDefault().post(eventMsg);

3.接收消息

    @Subscribe(threadMode = ThreadMode.MAIN)
    public void onMessageEvent(EventMsg eventMsg) {
        resultTextView.setText(eventMsg.msg);
    }

__完整实例__

    public class EventBusTesterActivity extends AppCompatActivity {
        Button basicTestBtn;
        TextView resultTextView;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_event_bus_tester);

            resultTextView = findViewById(R.id.result);

            basicTestBtn = findViewById(R.id.basic_test);
            basicTestBtn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    EventMsg eventMsg = new EventMsg();
                    eventMsg.id = 1;
                    eventMsg.msg = "This is event msg";

                    EventBus.getDefault().post(eventMsg);
                }
            });

            EventBus.getDefault().register(this);
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            EventBus.getDefault().unregister(this);
        }

        @Subscribe(threadMode = ThreadMode.MAIN)
        public void onMessageEvent(EventMsg eventMsg) {
            resultTextView.setText(eventMsg.msg);
        }

    }

EventBus的缺点是每一个事件都要定义单独的消息类

参考 http://viator42.github.io/android/2018/03/02/android-eventbus/

--------

## Activity导航

跳转使用intent，intent分为显式intent和隐式intent

### 显式intent

    Intent intent = new Intent();
    intent.setClass(this,Other.class); //此句表示显式意图，因为明确设置激活对象为Other类
    startActivity(intent);

### 隐式Intent

首先是在AndroidManifest.xml中为某个Activity设置意图过滤器：

__action__

使用action定义目标activity

    <activity
        android:name=".SecondActivity">
        <intent-filter>
            <action android:name="com.viator42,vividandroidexamples.action.SECOND" />
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
    </activity>

跳转

    Intent intent = new Intent();
    intent.setAction("com.viator42,vividandroidexamples.action.SECOND");
    startActivity(intent);

--------

## ARouter

--------

## Activity分发技术

一个Activity中可能容纳多个业务，这些业务分布在独立的模块中。比如商品的评论列表。IM模块



