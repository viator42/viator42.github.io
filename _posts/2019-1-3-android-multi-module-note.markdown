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

--------

## Activity导航

### 隐式跳转

### ARouter

--------

## Activity分发技术

一个Activity中可能容纳多个业务，这些业务分布在独立的模块中。比如商品的评论列表。IM模块



