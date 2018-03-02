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

## 使用方法

1. 注册EventBus

    EventBus.getDefault().register(MainActivity.this);
    EventBus.getDefault().unregister(MainActivity.this);

2. 定义Event对象用来传递数据.带有getter和setter的POJO对象即可

    public class EventMessage {
        private ArrayList<String> items;

        public EventMessage(ArrayList<String> items) {
            this.items = items;
        }

        public ArrayList<String> getItems() {
            return items;
        }

        public void setItems(ArrayList<String> items) {
            this.items = items;
        }
    }

3. 发送post请求, 参数就是定义的Event对象

    EventBus.getDefault().post(new EventMessage(data));

4. subscriber方法,当event被发送的时候由subscribers接收处理

    @Subscribe(threadMode = ThreadMode.MAIN)
    public void handleSomethingElse(EventMessage event) {
        
    }

添加@Subscribe注解,方法参数为Event对象类

### threadMode分类

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

