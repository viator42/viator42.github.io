---
layout: post
title:  "Gradle for Android笔记"
date:   "2019-1-1"
categories: android
---

## Android应用的构建过程

编译构建分为4个步骤

代码编译 -> 代码合成 -> 资源打包 -> 签名和对齐

1. 代码编译
Java编译器对工程的代码文件进行编译，包括App源代码，编译生成的R文件和AIDL接口生成的Java接口文件

2. 代码合成
通过dex工具将class文件和第三方库文件生成虚拟机可执行的dex文件。如果使用了MultiDex，会生成多个dex文件

3. 资源打包
apkbuilder工具将dex文件，apt编译后的资源文件，第三方库打包生成apk文件

4.签名对齐
使用预定的key对apk包进行签名。对齐可以使得在运行时Android与应用程序间的交互更加有效率

首先所有的资源文件会被编译，并且在一个R文件中被引用。然后Java文件编译，通过dex工具转换成dalvik字节码。最后这些文件会被打包成一个APK文件，这个应用最终安装在设备中前，APK文件会被一个debug或者release的key文件签名。

--------

## Gradle文件

在项目的根目录有build.gradle，Settings.gradle，每个模块都有一个build.gradle

### setttings.gradle

这个文件定义了哪些模块会参与构建,创建模块后需要在这里添加到项目

    include ':app', ':base'

### 根目录的build.gradle

    buildscript {
        //定义依赖仓库
        repositories {
            google()
            jcenter()   //整个构建过程中的依赖仓库
        }
        
        //构建过程中的依赖包
        dependencies {
            classpath 'com.android.tools.build:gradle:3.2.1'
            classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }

    }

    //声明那些被用于所有模块的属性，可以在allprojects中创建任务，这些任务最终会被运用到所有模块
    allprojects {
        repositories {
            google()
            jcenter()
        }
    }

### 每个模块的build.gradle

    apply plugin: 'com.android.application' //导入Android应用插件

    //包含了全部的Android特有配置
    android {
        compileSdkVersion 27    //用来编译应用的Android API版本
        buildToolsVersion '27.0.2'  //构建工具和编译器使用的版本号
        defaultConfig {
            applicationId "com.viator42.ugo"    //applicationId覆盖了AndroidManifest.xml中的package name属性，跟package name不同。同样的package name可以有不同的id，这样设计可以为一个应用制作不同的版本,而且可以安装到同一个手机上
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

--------

### 定义依赖

一个依赖由 group：name：version构成

    implementation 'com.android.support:appcompat-v7:27.1.1'

添加本地依赖

    implementation files('libs/AMap_Location_V3.3.0_20170118.jar')

aar文件依赖

--------

### 创建Variant

buildType构建类型用来定义如何构建一个应用，debug标识是否包含，application是什么，是否进行代码压缩
默认的有debug和release

    android {
        。。。
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
    }

### productFlavors

productFlavors用于创建不同的版本，比如构建免费版和付费版，内部试用版版和外部版，同一类别不同客户使用的版本，唯一不同的是颜色图标和后台url，对应不同市场发布的渠道包    

    android {
        。。。
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

--------

## Gradle 3.0以上的变化

compile指令改成了api，两者使用没有区别

增加了implementation指令作为默认的依赖方式，代替了compile。跟compile的区别在于不能跨模块依赖。模块中的依赖包不会被外部访问到，这样可以隐藏内部接口的实现而且不会产生依赖冲突。

--------



