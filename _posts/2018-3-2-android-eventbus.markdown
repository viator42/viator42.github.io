---
layout: post
title:  "Android EventBus学习笔记"
date:   2018-3-2
categories: android
---

## 概念

**EventBus**是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。比如请求网络，等网络返回时通过Handler或Broadcast通知UI，两个Fragment之间需要通过Listener通信，这些需求都可以通过**EventBus**实现。

作为一个消息总线，有三个主要的元素：

* Event：事件
* Subscriber：事件订阅者，接收特定的事件
* Publisher:事件发布者，用于通知Subscriber有事件发生

## 原理

主要基于观察者模式，事件是被观察者，订阅者类是观察者。当事件出现或者发送变更的时候，会通过EventBus通知观察者，观察者定义的方法会自动调用    
EventBus根据事件类的不同调用通知不同的观察者，每个事件都必须自定义一个事件类    

## 导入库

AndroidManifest.xml

    dependencies {
        implementation 'org.greenrobot:eventbus:3.1.1'
    }

## 使用方法

1.定义需要post的对象类，普通的POJO对象即可

    public class EventMsg {
        public long id;
        public String msg;
    }

2.准备 Subscriber （订阅者）

    //需要先注册订阅者
    protected void onCreate(Bundle savedInstanceState) {
        。。。
        EventBus.getDefault().register(this);
    }

    //Context销毁时需要同步注销订阅者
    protected void onDestroy() {
        super.onDestroy();
        EventBus.getDefault().unregister(this);
    }

    //事件接收方法，方法名称可以随意设置，但要添加@Subscribe注解
    //根据参数不同判断
    @Subscribe(threadMode = ThreadMode.MAIN)
    public void onMessageEvent(EventMsg eventMsg) {
        resultTextView.setText(eventMsg.msg);
    }

3.可以在任何地方发送post请求, 参数就是定义的Event对象

    EventMsg eventMsg = new EventMsg();
    eventMsg.id = 1;
    eventMsg.msg = "This is event msg";

    EventBus.getDefault().post(eventMsg);

## threadMode分类

threadMode设置subscriber方法在哪个线程内执行

1. ThreadMode: POSTING    
默认选项,跟post请求在同一个线程

2. ThreadMode: MAIN
在主线程(UI线程)中运行

3. ThreadMode: MAIN_ORDERED
同样是在主线程中运行.跟MAIN的区别是当有多个post的时候,MAIN_ORDERED会按添加的先后顺序执行

4. ThreadMode: BACKGROUND
Subscriber会在后台线程中运行,如果post不在主线程执行,那么Subscribers和post在同一个线程中.如果post在主线程中执行,Subscribers会开启一个单独的线程.

5. ThreadMode: ASYNC
Subscriber总是在不同的线程中运行,不管post是不是在主线程中.

## 自定义EventBus对象

EventBus.getDefault()获取默认的EventBus对象

还可以自定义

    EventBus eventBus = EventBus.builder()
        .logNoSubscriberMessages(true)
        .sendNoSubscriberEvent(true)
        .build();

## Sticky Events粘性事件

在Android开发中，Sticky事件只指事件消费者在事件发布之后才注册的也能接收到该事件的特殊类型。Android中就有这样的实例，也就是Sticky Broadcast，即粘性广播。正常情况下如果发送者发送了某个广播，而接收者在这个广播发送后才注册自己的Receiver，这时接收者便 无法接收到刚才的广播，为此Android引入了StickyBroadcast，在广播发送结束后会保存刚刚发送的广播（Intent），这样当接收者注册完Receiver 后就可以接收到刚才已经发布的广播。这就使得我们可以预先处理一些事件，让有消费者时再把这些事件投递给消费者。

分发粘性事件

    EventBus.getDefault().postSticky(new MessageEvent("Hello everyone!"));

事件接收方法，添加sticky=true属性

    @Subscribe(sticky = true, threadMode = ThreadMode.MAIN)
    public void onEvent(MessageEvent event) {
        。。。
    }

## Subscriber 索引

Subscriber索引是EventBus3的新特性。是为了加速首次subscriber注册过程的可选优化。其在编译的时候为注册类构建了一个索引，而不是在运行时，这样的结果是其让EventBus 3.0的性能提升了一倍

### 生成索引

在AndroidManife.xml中添加：
	
    android {
        defaultConfig {
            javaCompileOptions {
                annotationProcessorOptions {
                    arguments = [ eventBusIndex : 'com.example.myapp.MyEventBusIndex' ]
                }
            }
        }
    }
    
    dependencies {
        implementation 'org.greenrobot:eventbus:3.1.1'
    annotationProcessor 'org.greenrobot:eventbus-annotation-processor:3.1.1'

### 使用索引

成功编译项目后, 会生成 eventBusIndex 指定的类. 然后用这个类来初始化 EventBus:

    EventBus eventBus = EventBus.builder().addIndex(new MyEventBusIndex()).build();

或者这样添加全局索引:

    EventBus.builder().addIndex(new MyEventBusIndex()).installDefaultEventBus();
    // Now the default instance uses the given index. Use it like this:
    EventBus eventBus = EventBus.getDefault();

## 优先级 

大多数 EventBus 的使用都既不会用到优先级, 也不会取消事件, 但是在一些特殊情况下还是可能用到的. 比如, 当 app 在前台的时候触发某些更新 UI 的代码, 否则做些其他的事情.

可以在注册 Subscriber 的时候提供优先级来改变事件传递的顺序:

    @Subscribe(priority = 1);
    public void onEvent(MessageEvent event) {
        ...
    }

在同一个 ThreadMode 下, 更高优先级的 Subscriber 会比低优先级的更先收到事件. 默认的优先级是 0.

    优先级只影响相同 ThreadMode 中的 Subscriber.

## 取消事件的传递

可以通过在 Subscriber 的事件处理线程调用 cancelEventDelivery(Object event) 来取消事件传递. 进一步的事件传递将被取消，接下来的 Subscriber 都不会接收到事件。

    @Subscribe
    public void onEvent(MessageEvent event){
        EventBus.getDefault().cancelEventDelivery(event);
    }


