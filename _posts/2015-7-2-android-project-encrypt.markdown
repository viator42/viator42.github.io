---
layout: post
title:  "Android代码的反编译混淆实践"
date:   2015-07-02
categories: Android
---

由于项目要发布,所以花了半天时间对代码的反编译和混淆进行下研究.

apk包直接解压缩后可以看到除了图片资源和布局xml文件之外所有的类都封装到class.hex文件中了.可以使用dex2jar工具可以把dex文件解包成为jar包.
使用方法是运行d2j-dex2jar.bat(Linux下用.sh) 文件路径 .
生成的.jar文件再次解压缩就能看到.class文件.使用Java Decompiler可以看到反编译后的源代码.

如果没有混淆处理的话反编译的代码跟源码基本上一模一样.

Android Studio使用ProGuard对代码进行混淆处理.

1. 从android sdk 目录复制文件.

/sdk/tools/proguard/proguard-android.txt 复制到工程的/app目录下.

2. 修改build.gradle配置文件

    buildTypes {
        release {
            minifyEnabled true
            debuggable true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

minifyEnabled由false改为true

3. 如果项目中有第三方的类库.必须将这些排除.否则会出现找不到类名的错误.因为这些类库可能已经加密.

修改proguard-android.txt,按以下格式添加排除的类库.

    -dontwarn org.apache.commons.**
    -dontwarn com.amap.api.**

    -keep class org.apache.commons.** { *; }
    -keep class com.amap.api.** { *; }

这样生成的apk反编译后所有的类名变量名都用a,b,c,d,e代替.