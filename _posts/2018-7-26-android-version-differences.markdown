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

# Android 7.0 (API 24) Nougat

* 通知栏快捷回复
* 加入原生分屏多任务功能，多任务快速切换
* 低耗电模式,屏幕关闭后限制网络访问
* 删除了个隐式广播 在WLAN和移动数据切换时CONNECTIVITY_ACTION、相机拍摄照片ACTION_NEW_PICTURE 和录像ACTION_NEW_VIDEO
* 建议使用 SurfaceView 代替 TextureView
* ConnectivityManager检查是否启用了流量节省选项
* android.service.quicksettings.Tile 快速设置图块,可以自定义设置项
* android.provider.BlockedNumberContract 号码屏蔽API
* Android ICU4J API

# Android 7.1 (API 25)

* 圆形应用图标
* App Shortcuts 长按应用图标进入快捷菜单

# Android 8.0 (API 26) Oreo

* 通知渠道

* 自动填充框架

* 画中画功能PictureInPicture API

* 统一的布局外边距和内边距

    Android 8.0 让您可以更轻松地指定 View 元素的对边使用相同外边距和内边距的情形。具体来说，您现在可以在布局 XML 文件中使用以下属性：

    layout_marginVertical，同时定义 layout_marginTop 和 layout_marginBottom。     
    layout_marginHorizontal，同时定义 layout_marginLeft 和 layout_marginRight。    
    paddingVertical，同时定义 paddingTop 和 paddingBottom。    
    paddingHorizontal，同时定义 paddingLeft 和 paddingRight。    

* findViewById函数的全部实例均返回 <T extends View> T，而不是 View, Activity.findViewById()不再需要转换类型

* Notification Dots
* 自适应图标（Adaptive icons）
* 后台进程限制
* 运行时权限策略变化

# Android 8.1 (API 27)

# Android 9.0 (API 28) Pie

* 显示屏缺口(刘海屏)支持

* 引入了 ImageDecoder 类，可提供现代化的图像解码方法。 使用该类取代 BitmapFactory 和 BitmapFactory.Options API。

* Android 9 引入了 AnimatedImageDrawable 类，用于绘制和显示 GIF 和 WebP 动画图像。 

* Android 9 限制后台应用访问用户输入和传感器数据的能力。 如果您的应用在运行 Android 9 设备的后台运行，系统将对您的应用采取以下限制：

    您的应用不能访问麦克风或摄像头。    
    使用连续报告模式的传感器（例如加速度计和陀螺仪）不会接收事件。    
    使用变化或一次性报告模式的传感器不会接收事件。    

* Android 9 引入 CALL_LOG 权限组并将 READ_CALL_LOG、WRITE_CALL_LOG 和 PROCESS_OUTGOING_CALLS 权限移入该组。 在之前的 Android 版本中，这些权限位于 PHONE 权限组。

* 通话权限修改

引入CALL_LOG权限组,并将 READ_CALL_LOG、WRITE_CALL_LOG 和 PROCESS_OUTGOING_CALLS 权限移入该组。 在之前的 Android 版本中，这些权限位于 PHONE 权限组。

要从手机状态中读取电话号码，请根据您的用例更新应用以请求必要的权限：

    要通过 PHONE_STATE Intent 操作读取电话号码，同时需要 READ_CALL_LOG 权限和 READ_PHONE_STATE 权限。    
    要从 onCallStateChanged() 中读取电话号码，只需要 READ_CALL_LOG 权限。 不需要 READ_PHONE_STATE 权限。    

* 测试类库修改

    基于JUnit和Mockito

