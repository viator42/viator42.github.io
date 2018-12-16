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

--------

## 启用ProGuard

build.gradle文件修改

    android {
        ···
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
    }

每次构建时 ProGuard 都会输出下列文件：

* dump.txt      说明 APK 中所有类文件的内部结构。    
* mapping.txt   提供原始与混淆过的类、方法和字段名称之间的转换。
* seeds.txt     列出未进行混淆的类和成员。
* usage.txt     列出从 APK 移除的代码。

这些文件保存在 <module-name>/build/outputs/mapping/release/ 中。

--------

## 编写proguard-rules.pro文件

\# 代表行注释符    
\- 表示一条规则的开始    

## 常用选项

* -optimizationpasses 5   代码混淆压缩比，在0和7之间，默认为5，一般不需要改
 
* -dontusemixedcaseclassnames     混淆时不使用大小写混合，混淆后的类名为小写
 
* -dontskipnonpubliclibraryclasses    指定不去忽略非公共的库的类
 
* -dontskipnonpubliclibraryclassmembers   指定不去忽略非公共的库的类的成员
 
* -dontpreverify  不做预校验，preverify是proguard的4个步骤之一，Android不需要preverify，去掉这一步可加快混淆速度
 
* -verbose
* -printmapping proguardMapping.txt
 
有了verbose这句话，混淆后就会生成映射文件
包含有类名->混淆后类名的映射关系
然后使用printmapping指定映射文件的名称
 
* -keepattributes *Annotation*    保护代码中的Annotation不被混淆，这在JSON实体映射时非常重要，比如fastJson
 
* -keepattributes Signature   避免混淆泛型，这在JSON实体映射时非常重要，比如fastJson
 
* -keepattributes SourceFile,LineNumberTable  抛出异常时保留代码行号，在异常分析中可以方便定位

* -dontskipnonpubliclibraryclasses

用于告诉ProGuard，不要跳过对非公开类的处理。默认情况下是跳过的，因为程序中不会引用它们，有些情况下人们编写的代码与类库中的类在同一个包下，并且对包中内容加以引用，此时需要加入此条声明。

* -dontusemixedcaseclassnames

这个是给Microsoft Windows用户的，因为ProGuard假定使用的操作系统是能区分两个只是大小写不同的文件名，但是Microsoft Windows不是这样的操作系统，所以必须为ProGuard指定-dontusemixedcaseclassnames选项

--------

## 保留不被混淆的东西

#### -keep 
保护某些包下的类不被混淆    

    -keep public com.viator42.sampleapp
    -keep public * extends com.viator42.sampleapp

实体类保留get,set方法不被混淆

    -keep public class com.brand.ushopping.model {
        public void set* (***);
        public *** get* ();
        public *** is* ();
    }

## 其他keep选项

* -keep 	不混淆类和类成员（包括方法和参数）
* -keepclassmembers 	不混淆类中的成员
* -keepclasseswithmembers 	不混淆类中的成员及包含它的类
* -keepnames 	
* -keepclassmembernames
* -keepclasseswithmembernames

有无name的keep*区别    
keep是阻止类和类的变量（实际上就是类中的方法和变量）被压缩和混淆，而keepnames那么则是在压缩后，防止类和类的成员被混淆    

## 通配符

    <init> 	    匹配所有构造函数
    <fields> 	匹配所有字段
    <methods> 	匹配所有方法，不包括构造函数
    ? 	        匹配任意单个字符
    % 	        匹配任意原始数据类型，例如 boolean、int，但是不包括 void
    * 	        匹配任意长度字符，但是不包括包名分隔符（ . )，例如 android.support.* 不匹配 android.support.annotation.Keep
    ** 	        匹配任意长度字符，包括包名分隔符（ . )，例如 android.support.** 匹配 support 包下的所有类
    *** 	    匹配任意类型，包括原始数据类型、数组
    … 	        匹配任意数量的任意参数类型

## 其他选项

* -dontnote 不打印潜在的错误或疏漏的注释

* -dontwarn 不针对未处理的引用或其他重要问题发出警告

    -dontwarn sun.misc.Unsafe

* -keepattributes   保护特定类型不被混淆

    -keepattributes Exceptions, InnerClasses, Signature, Deprecated, SourceFile ,LineNumberTable, *Annotation*, EnclosingMethod

* -ignorewarning  // 忽略产生的警告继续往下运行

* -dontskipnonpubliclibraryclassmembers // 不跳过非公开库的类成员

--------

##Android项目配置

* 如果使用了Gson之类的工具要使JavaBean类即实体类不被混淆。    
* 如果使用了自定义控件那么要保证它们不参与混淆。    
* 如果使用了枚举要保证枚举不被混淆。    
* 对第三方库中的类不进行混淆    

Activity，Application，Service，BroadcastReceiver，ContentProvider这些写入AndroidManifest.xml中的类需要排除    
以下是官方示例    

    -keep public class * extends android.app.Activity
    -keep public class * extends android.app.Application
    -keep public class * extends android.app.Service
    -keep public class * extends android.content.BroadcastReceiver
    -keep public class * extends android.content.ContentProvider

    -keep public class * extends android.view.View {
        public <init>(android.content.Context);
        public <init>(android.content.Context, android.util.AttributeSet);
        public <init>(android.content.Context, android.util.AttributeSet, int);
        public void set*(...);
    }

    -keepclasseswithmembers class * {
        public <init>(android.content.Context, android.util.AttributeSet);
    }

    -keepclasseswithmembers class * {
        public <init>(android.content.Context, android.util.AttributeSet, int);
    }

    -keepclassmembers class * extends android.content.Context {
        public void *(android.view.View);
        public void *(android.view.MenuItem);
    }

    -keepclassmembers class * implements android.os.Parcelable {
        static ** CREATOR;
    }

    -keepclassmembers class **.R$* {
        public static <fields>;
    }

    -keepclassmembers class * {
        @android.webkit.JavascriptInterface <methods>;
    }

速查表 https://www.guardsquare.com/en/products/proguard/manual/refcard

------------------------------------------------------------------------

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