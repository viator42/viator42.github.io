---
layout: post
title:  "Gradle for Android笔记"
date:   "2019-1-1"
categories: android
---

## Android应用的构建过程

首先所有的资源文件会被编译，并且在一个R文件中被引用。然后Java文件编译，通过dex工具转换成dalvik字节码。最后这些文件会被打包成一个APK文件，这个应用最终安装在设备中前，APK文件会被一个debug或者release的key文件签名。

--------

## Gradle相关

Android的构建文件中必须的元素

    buildscript {
        
        repositories {
            jcenter()   //整个构建过程中的依赖仓库
        }
        dependencies {
        }
    }

build.gradle Settings.gradle在项目的根目录，每个模块都有一个build.gradle

setttings.gradle定义了哪些模块会参与构建

    include ':app', ':base'

根目录的build.gradle

    buildscript {
        //定义依赖仓库
        repositories {
            google()
            jcenter()
        }
        
        //构建过程中的依赖包
        dependencies {
            classpath 'com.android.tools.build:gradle:3.2.1'
            classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }

    }

    allprojects {
        repositories {
            google()
            jcenter()
        }
    }

每个模块的build.gradle

    apply plugin: 'com.android.application' //导入Android应用插件

    //包含了全部的Android特有配置
    android {
        compileSdkVersion 27
        defaultConfig {
            applicationId "com.viator42.ugo"    //应用的id，跟package name不同。同样的package name可以有不同的id
            minSdkVersion 19
            targetSdkVersion 27
            versionCode 1
            versionName "1.0"
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
            vectorDrawables.useSupportLibrary = true
        }
        //用来打包不同构建类型的应用
        buildTypes {
            debug {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
            release {
                minifyEnabled true
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }

        flavorDimensions "default"
        productFlavors {
            productFlavors.all {
                flavor -> flavor.manifestPlaceholders =[channel: name]
            }
        }
    }

    dependencies {
        implementation fileTree(include: ['*.jar'], dir: 'libs')
        。。。
    }

###定义依赖

一个依赖由 group：name：version构成

    implementation 'com.android.support:appcompat-v7:27.1.1'

添加本地依赖

    implementation files('libs/AMap_Location_V3.3.0_20170118.jar')

aar文件依赖

### 创建Variant

buildType构建类型用来定义如何构建一个应用，默认的有debug和release

    buildTypes {
        debug {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            debuggable = true
        }
        staging.initWith(buildTypes.debug)  //可以从现有的构建类型继承属性
        staging {
            applicationIdSuffix "-staging"  //applicatinId当前类型的app id添加后缀，这样测试版和正式版能装在同一台手机上
            versionNameSuffix "-staging"    //为当前类型的versionName添加后缀

        }
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            debuggable = false
        }
    }

productFlavors用于创建不同的渠道包

    productFlavors {
        qihu360 {
            manifestPlaceholders = [
                    channel_id: "1000",
                    app_name  : "UGou_360定制版",
            ]

        }
        wandoujia {
            manifestPlaceholders = [
                    channel_id: "1001",
                    app_name  : "UGou_豌豆荚定制版",
            ]
        }
//        productFlavors.all {
//            flavor -> flavor.manifestPlaceholders =[channel: name]
//        }
    }

    dependencies {
        qihu360Implementation project(':base')  //为渠道包引入特定的module
    }

## Gradle参数

修改根目录下的gradle.properties文件，以下是可以提供提高构建效率的选项
    
* org.gradle.jvmargs=-Xms512m -Xmx2048m     //设置Java虚拟机的内存分配 Xms：初始内存 Xmx最大内存
* org.gradle.daemon=true    //第一次构建的时候开启一个守护进程，在以后的构建过程中都会复用这个进程
* org.dradle.configureondemand=true     //只编译有变化的模块，当有多个模块的时候构建时间会大为加快
* org.gradle.parallel=true      //开启多模块并行构建

--------

## Gradle使用Groovy语言定义构建任务

### Groovy语言

__定义变量__

行尾没有括号

    def name =  'Viator42'  //单引号是单纯的字符串
    def msg = "Your name is ${name}"    //双引号可以包含表达式

__类和成员变量__

    class GroovyClass {
        String param1

        String func1() {
            return 'aaa'
        }
    }

__集合__

List

    //  定义
    List list = [1, 2, 3]
    //  循环遍历
    list.each() {element ->
        print element
    }

Map

    //  定义
    Map map = {param1: 1, param2: 2}
    //  获取map中的项目
    map.get('param1')
    map['param2']

### 在Gradle中的使用

    build.gradle中定义一个任务

    android {
        task hello {
            println 'Task hello'
        }

        //普通的任务在配置阶段运行
        task task During Config {
            println 'Task hello'
        }

        // 加了 << 标识符的会在构建阶段运行
        task taskDuringBuild << {
            println 'task During Build'
        }

        //设置Task执行的先后顺序
        task task1 {
            println 'Task 1'
        }

        task task2 {
            println 'Task 2'
        }

        task2.mustRunAfter task1

    }

通过操控构建过程来重命名APK文件，添加版本号信息

    android.applicationVariants.all { variant ->
        variant.outputs.all { output ->
            def file = output.outputFile
            println "apk file name is: ${output.outputFile.name}"
            outputFileName = "${variant.name}-${variant.versionName}.apk"
        }
    }



