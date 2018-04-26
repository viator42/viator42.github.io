---
layout: post
title:  "Android ProGuard相关"
date:   2018-04-25
categories: Android
---

## 代码混淆过程

* 压缩
移除代码中无用的类,字段,方法,特性

* 优化
对字节码进行优化,移除无用的指令

* 混淆
使用a,b,c...这种简短的名称对类,方法,变量进行重命名

* 预检
对处理后的代码进行预检

## 编写ProGuard.cfg文件

-dontusemixedcaseclassnames    
指定混淆的类名称不使用大小写混合

-skipnonpubliclibraryclasses    
跳过非公开的类

-keepattributes *Annotation*    
保护代码中的Annotation不被混淆

-keepattributes Exceptions,InnerClasses,Signature,Deprecated,SourceFile,LineNumberTable,*Annotation*,EnclosingMethod

## 保留不被混淆的东西

保护某些包下的类不被混淆

    -keep public * extends com.viator42.sampleapp

实体类保留get,set方法不被混淆

    -keep public class com.brand.ushopping.model {
        public void set* (***);
        public *** get* ();
        public *** is* ();
    }

--------


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