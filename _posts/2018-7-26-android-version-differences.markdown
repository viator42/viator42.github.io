---
layout: post
title:  "Android 各个版本的区别"
date:   2018-7-26
categories: android
---

# Android 4.4 (API 19) Kitkat

* Halo主题UI
* Activity.isLowRamDevice() 匹配低内存设备
* 打印框架
* 存储访问框架,可以通过同一个应用完成打开文件的操作
* 短信提供程序,访问短信的API,Telephoney,允许应用读取修改发送短信
* 全屏沉浸模式,隐藏状态栏导航栏和三大按键
* 应用转场框架 TransactionManager
* 基于Chrominu的webview
* Procstats 内存分析工具
* Animator API允许pause()和resume()暂停和继续动画

# Android 5.0 (API 21) lolipop 

* Material Design UI
* 系统由以往的Dalvik模式改为采用ART（Android Runtime）模式，实现ahead-of-time (AOT)静态编译与just-in-time (JIT)动态编译交互进行
* 新加入锁屏通知
* 新加入屏幕顶端的抬头通知
* V7中引入CardView和RecycleView等新控件
* 支持64位系统
* JobScheduler 允许自定义一些在指定条件下(设备充电,接入WLAN)运行的作业,降低电池,流量消耗
* 固定屏幕API 

# Android 5.0 (API 22)

* 网络访问弃用ApacheHttpClient库,使用URLConnection

# Android 6.X (API 23) Marshmallow

* 新加入指纹库FingerPrintManager
* 新增运行时权限概念,调用功能时动态请求权限
* 新增瞌睡模式和待机模式
* 从openSSL库转向使用boringSSL库
* 移除对Apache HTTP client的支持，建议使用HttpURLConnection。
* Doze电量管理

# Android 7.X

* 通知栏快捷回复
* 加入原生分屏多任务功能，多任务快速切换

# Android 8.X

* 画中画功能    
* Notification Dots
* 自动填充（Auto-Fill）
* 自适应图标（Adaptive icons）
* 后台进程限制
* 运行时权限策略变化



