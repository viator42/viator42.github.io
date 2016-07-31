---
layout: post
title:  "Android ADB命令工具笔记"
date:   2016-07-20
categories: android
---

## adb常用命令

* 显示连接的设备    
adb devices

* 显示帮助    
adb help

* 进入调试设备的shell模式,退出使用exit    
adb shell

* 给设备安装应用    
adb install -r 应用程序路径.apk

* 从设备中获取文件    
adb pull [remote path] [local path]   例如: adb pull /sdcard/aaa.txt c:\

* 文件发送到设备    
adb push [local path] [remote path] 例如: adb push c:\ES.apk /sdcard/Apk/

