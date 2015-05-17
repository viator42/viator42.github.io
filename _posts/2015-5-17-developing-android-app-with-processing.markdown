---
layout: post
title:  "用Processing语言开发安卓应用"
date:   2015-5-17
categories: others
---

此教程是在Mac下完成的,其他平台略有不同.

1. 首先下载Processing IDE[processing.org/download](https://processing.org/download/).选择2.X版本下载.3.0版无法安装android扩展.下载完成后解压缩安装.

2. 在用户目录下创建.bash_profile文件,指定android sdk的位置
文件内容

    export ANDROID_HOME=/Users/viator42/Library/Android/sdk
     export PATH=/Users/viator42/Library/Android/sdk/platform-tools:/Users/viator42/Library/Android/sdk/tools:$PATH


3. 打开Processing IDE, 右侧选择语言的地方点击Add Mode...选择Android Mode点击Install.如果没有这个选项说明系统未找到android sdk.下载此扩展需要翻墙.


4. 安装完成后重启Processing,切换到Android Mode.这时需要手动指定android sdk的位置.


这时就可以运行一些demo来测试一下了.如果连接着开发者模式的手机会直接在手机上运行.同时在项目目录下生成转换的Android项目,可以直接导入到ADT或者AndroidStudio中.详细的可以参考作者的[github](https://github.com/processing/processing-android/wiki).

个人感觉Processing语言适合做个人项目或者功能简单的应用,短短几行就能实现一些很酷炫的效果.对于Android开发者来说作为Java语言的补充是个不错的选择.
