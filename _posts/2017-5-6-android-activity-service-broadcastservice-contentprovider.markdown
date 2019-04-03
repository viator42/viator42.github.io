---
layout: post
title:  "Android 四大组件相关"
date:   2017-05-06
categories: android
---

# Activity

### Activity的生命周期

![Activity Lifescycle](http://android.okhelp.cz/wp-content/uploads/lifecycle-activity-android.png "Activity Lifescycle")

__调用顺序__

activity第一次创建时调用onCreate(), 经过onStart() 、onResume()变成foreground process（前景模式）。 弹出对话框时activity变成visible process（可见模式）,调用onPause().对话框交互结束调用onResume()返回前景模式.跳转到其他activity的时候调用onStop().跳转回来的时候经过
onRestart(), onStart() 、onResume().Activity被结束的时候调用onDestroy().

__方法解释__

* onCreate() 方法实现一些初始化的工作,比如加载setContentView加载界面布局资源    
* onStart() Activity正在被启动,但还没出现在前台,无法和用户交互    
* onRestart() activity正在被重新启动,从桌面或者其他activity回来的时候调用    
* onResume() activity已经可见,而且出现在前台并开始活动

__Activity全屏__
    
    requestWindowFeature(Window.FEATURE_NO_TITLE);  //隐藏应用的ActionBar
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
            WindowManager.LayoutParams.FLAG_FULLSCREEN);    //应用全屏,隐藏android的标题栏

__activity启动模式__

* standard (标准模式) 默认的启动模式,每次启动一个新的activity都创建一个新的实例到栈中,不管这个实例是否已经存在
* singleTop (栈顶复用模式) 创建activity的时候如果栈顶已经是该活动,就直接使用不会重复创建.这个activity的onCreate,onStart不会被系统调用,因为它并没有发生改变
* singleTask (栈内复用模式) 如果栈中已经有此activity则把这个活动之上的所有活动出栈.
* singleInstance (单实例模式) 这个模式下的Activity单独在一个task栈中。这个栈只有一个Activity。

设置方法

    <activity
    android:launchMode="singleTop"/>

__activity跳转(带参数)__

	Intent intent = new Intent(activity.this, MainActivity.class);
    Bundle bundle = new Bundle();
    Bundle bundle = new Bundle();
    bundle.putString("key", "value");
	startActivity(intent);
	finish();

    Bundle bundle = this.getIntent().getExtras();
    String value = bundle.getString("key", "");

__Intent的组成__

Intent由6部分信息组成：Component Name、Action、Data、Category、Extras、Flags。根据信息的作用用于，又可分为三类:   

* Component Name、Action、Data、Category为一类，这4中信息决定了Android会启动哪个组件，其中Component Name用于在显式Intent中使用，Action、Data、Category、Extras、Flags用于在隐式Intent中使用。

* Extras为一类，里面包含了具体的用于组件实际处理的数据信息。
* Flags为一类，其是Intent的元数据，决定了Android对其操作的一些行为

Component Name是在显式Intent中指定目标Activity    
Action是表示了要执行操作的字符串，比如查看或选择，其对应着Intent Filter中的action标签<action />    
Data指的是Uri对象和数据的MIME类型，其对应着Intent Filter中的data标签<data />一个完整的Uri由scheme、host、port、path组成，格式是<scheme>://<host>:<port>/<path>，例如content://com.example.project:200/folder/subfolder/etc。    
Category包含了关于组件如何处理Intent的一些其他信息，虽然可以在Intent中加入任意数量的category，但是大多数的Intent其实不需要category。
Extras就是额外的数据信息，Intent中有一个Bundle对象存储着各种键值对，接收该Intent的组件可以从中读取出所需要的信息以便完成相应的工作。

参考链接: http://lib.csdn.net/article/android/62966

__startActivityForResult带参数跳转回传__

在Android中startActivityForResult主要作用就是:      
A-Activity需要在B-Activtiy中执行一些数据操作，而B-Activity又要将，执行操作数据的结果返回给A-Activtiy

    //A-Activity跳转
    Intent intent = new Intent(AddressesActivity.this, AddAddressActivity.class);
    startActivityForResult(intent, StaticValues.ADDRESS_ACTION_UPDATE);

    //返回A-Activity之后的回调方法
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode) {
            case StaticValues.ADDRESS_ACTION_UPDATE:
                Bundle bundle = data.getExtras();
                break;
        }
    }

    //被调用的B-Activtiy返回值
    Intent intent = new Intent();
    Bundle bundle = new Bundle();
    intent.putExtras(bundle);
    //setResult的参数对应onActivityResult的resultCode和data
    AddAddressActivity.this.setResult(StaticValues.RESULT_CODE_OK, intent);
    AddAddressActivity.this.finish();

Actitity之间跳转,分为显式跳转和隐式跳转

__显式跳转__

    Intent intent=new Intent(this,OtherActivity.class);  //方法1

    Intent intent2=new Intent();

    intent2.setClass(this, OtherActivity.class);//方法2
    intent2.setClassName(this, "com.zy.MutiActivity.OtherActivity");  //方法3 此方式可用于打开其它的应用
    intent2.setComponent(new ComponentName(this, OtherActivity.class));  //方法4
    startActivity(intent2);

__隐式跳转（只要action、category、data和要跳转到的Activity在AndroidManifest.xml中设置的匹配就OK__

隐式跳转不指定启动特定的Activity,需要判断action和category找到匹配的Actitity启动     
AndroidManifest.xml文件中为每一个Activity中设置intent-filter来指定

这个是指定启动App启动时首先启动的Actitity

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>

指定目标Activity的filter, 包括name和category,name只有一个category可以设置多个

    <activity android:name=".SecondActivity" >
        <intent-filter>
            <action android:name="com.example.activitytest.ACTION_START" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="com.example.activitytest.MY_CATEGORY"/>
            <data android:scheme="ispring" android:host="blog.csdn.net" />
        </intent-filter>
    </activity>

创建Intent的时候设置name和category,只有name和所有的category都匹配的时候才会跳转到新的Activity.否则就会爆出ActivityNotFoundException异常

    Intent intent = new Intent("com.example.activitytest.ACTION_START");
    intent.addCategory("com.example.activitytest.MY_CATEGORY");
    startActivity(intent);

Android可以根据Intent所携带的信息去查找要启动的组件，Intent还携带了一些数据信息以便要启动的组件根据Intent中的这些数据做相应的处理。

__Intent filter的匹配规则__

1. action的匹配规则    
一个过滤规则中有多个action,Intent中的action和规则中的任一一个匹配成功即可.没有的话匹配失败

2. category的匹配规则        
入宫Intent中含有category,那么所有的category必须和过滤规则中的其中一个category相同.Intent中没有category也能匹配成功.系统在调用startActivity()的时候默认会添加"android.intent.category.DEFAULT"

3. data的匹配原则    
filter中定义了data,intent中也必须定义data.data包括scheme,host,port,path,pathPrefix,pathPattern参数.

### OnSavedInstance

不算生命周期方法,作用是当发生异常(内存不足,强行退出,屏幕旋转)由系统销毁一个Activity的时候会调用onSaveInstanceState()方法,用来保存当前实例的状态.比如存储数据到sharedFeference中

__调用时机__

onSaveInstanceState()在Activity快要销毁的时候提前调用,比如按home键退出,屏幕旋转,back键返回都会调用    
onRestoreInstanceState()在onStart() 和 onPostCreate(Bundle)之间调用。用来恢复现场    

--------

## Activity的启动过程

__涉及类的定义__

* ActivityManagerServices，简称AMS，服务端对象，负责系统中所有Activity的生命周期

* ActivityThread，App的真正入口。当开启App之后，会调用main()开始运行，开启消息循环队列，这就是传说中的UI线程或者叫主线程。与ActivityManagerServices配合，一起完成Activity的管理工作

* ApplicationThread，用来实现ActivityManagerService与ActivityThread之间的交互。在ActivityManagerService需要管理相关Application中的Activity的生命周期时，通过ApplicationThread的代理对象与ActivityThread通讯。

* ApplicationThreadProxy，是ApplicationThread在服务器端的代理，负责和客户端的ApplicationThread通讯。AMS就是通过该代理与ActivityThread进行通信的。

* Instrumentation，每一个应用程序只有一个Instrumentation对象，每个Activity内都有一个对该对象的引用。Instrumentation可以理解为应用进程的管家，
ActivityThread要创建或暂停某个Activity时，都需要通过Instrumentation来进行具体的操作。

* ActivityStack，Activity在AMS的栈管理，用来记录已经启动的Activity的先后关系，状态信息等。通过ActivityStack决定是否需要启动新的进程。

* ActivityRecord，ActivityStack的管理对象，每个Activity在AMS对应一个ActivityRecord，来记录Activity的状态以及其他的管理信息。其实就是服务器端的Activity对象的映像。

* TaskRecord，AMS抽象出来的一个“任务”的概念，是记录ActivityRecord的栈，一个“Task”包含若干个ActivityRecord。AMS用TaskRecord确保Activity启动和退出的顺序。如果你清楚Activity的4种launchMode，那么对这个概念应该不陌生。

* zygote Android系统里面，zygote是一个进程的名字。

__简要启动流程__

用户在Launcher程序里点击应用图标时，会通知ActivityManagerService启动应用的入口Activity，ActivityManagerService发现这个应用还未启动，则会通知Zygote进程孵化出应用进程，然后在这个dalvik应用进程里执行ActivityThread的main方法。应用进程接下来通知ActivityManagerService应用进程已启动，ActivityManagerService保存应用进程的一个代理对象，这样ActivityManagerService可以通过这个代理对象控制应用进程，然后ActivityManagerService通知应用进程创建入口Activity的实例，并执行它的生命周期方法。

__Activity启动时的概要交互流程__

用户从Launcher程序点击应用图标可启动应用的入口Activity，Activity启动时需要多个进程之间的交互，Android系统中有一个zygote进程专用于孵化Android框架层和应用层程序的进程。还有一个system_server进程，该进程里运行了很多binder service，例如ActivityManagerService，PackageManagerService，WindowManagerService，这些binder service分别运行在不同的线程中，其中ActivityManagerService负责管理Activity栈，应用进程，task。

Activity启动时的概要交互流程如下图如下所示

![Activity启动时的概要交互流程](http://www.cloudchou.com/wp-content/uploads/2015/05/activity_start_flow.png)

用户在Launcher程序里点击应用图标时，会通知ActivityManagerService启动应用的入口Activity，ActivityManagerService发现这个应用还未启动，则会通知Zygote进程孵化出应用进程，然后在这个dalvik应用进程里执行ActivityThread的main方法。应用进程接下来通知ActivityManagerService应用进程已启动，ActivityManagerService保存应用进程的一个代理对象，这样ActivityManagerService可以通过这个代理对象控制应用进程，然后ActivityManagerService通知应用进程创建入口Activity的实例，并执行它的生命周期方法。

参考: http://www.cloudchou.com/android/post-788.html

__调用过程__

 当我们调用startActivity()启动一个Activity的时候，首先他会调到startActivityForResult()方法：

    @Override
    public void startActivity(Intent intent, @Nullable Bundle options) {
        if (options != null) {
            startActivityForResult(intent, -1, options);
        } else {
            // Note we want to go through this call for compatibility with
            // applications that may have overridden the method.
            startActivityForResult(intent, -1);
        }
    }

接下来由Instrumentation类调用execStartActivity方法,Instrumentation类用来辅助Activity完成启动Activity的过程

    public void startActivityForResult(@RequiresPermission Intent intent, int requestCode,
            @Nullable Bundle options) {
        if (mParent == null) {
            options = transferSpringboardActivityOptions(options);
            Instrumentation.ActivityResult ar =
                mInstrumentation.execStartActivity(
                    this, mMainThread.getApplicationThread(), mToken, this,
                    intent, requestCode, options);
            if (ar != null) {
                mMainThread.sendActivityResult(
                    mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                    ar.getResultData());
            }
            if (requestCode >= 0) {
                // If this start is requesting a result, we can avoid making
                // the activity visible until the result is received.  Setting
                // this code during onCreate(Bundle savedInstanceState) or onResume() will keep the
                // activity hidden during this time, to avoid flickering.
                // This can only be done when a result is requested because
                // that guarantees we will get information back when the
                // activity is finished, no matter what happens to it.
                mStartedActivity = true;
            }

            cancelInputsAndStartExitTransition(options);
            // TODO Consider clearing/flushing other event sources and events for child windows.
        } else {
            if (options != null) {
                mParent.startActivityFromChild(this, intent, requestCode, options);
            } else {
                // Note we want to go through this method for compatibility with
                // existing applications that may have overridden it.
                mParent.startActivityFromChild(this, intent, requestCode);
            }
        }
    }

--------

# Service

Service分为普通Service和IntentService    
普通Service的调用方法是onCreate(),然后onStartCommand(),服务就会保持运行状态,直到执行stopService()销毁        
IntentService是将用户的请求执行在一个子线程中,用户只需重写onHandleIntent方法进行操作,任务执行完毕之后IntentService会自动销毁    

__Service和Activity在同一个线程吗__ 

如果是activity中启动的service那么在同一个线程中.activity退出service随之结束.如果需要service保持运行就需要新开一个线程启动service或者service运行在其他的进程中.    

### 定义Service类

类继承Service (android.app.Service)    
     onCreate: 创建的时候调用的方法     
     onStartCommand: service启动时调用的方法    

    public class TestService extends Service {
        private Timer timer = null;
        public class TestBinder extends Binder
        {
            public void sendMsg(String msg)
            {
                Log.v("ServiceTester", "msg content: " + msg);
            }
            public String getMsg()
            {
                return "an msg from TestService";
            }
        }
        private TestBinder binder = new TestBinder();

        @Override
        public void onCreate() {
            super.onCreate();
            Log.v("ServiceTester", "service created");

        }

        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            return binder;
        }

        @Override
        public boolean onUnbind(Intent intent) {
            Log.v("ServiceTester", "service unBind");
            return super.onUnbind(intent);

        }

        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            Log.v("ServiceTester", "service startCommand");

            if(timer == null)
            {
                timer = new Timer();
            }
            timer.schedule(new TimerTask() {
                @Override
                public void run() {
                    Log.v("ServiceTester", "service Ticking");
                }
            }, 1000, 1000);

            return super.onStartCommand(intent, flags, startId);
        }

        @Override
        public void onDestroy() {
            super.onDestroy();
            Log.v("ServiceTester", "service Destroy");

        }
    }

AndroidManifest.xml 中注册service

    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.viator42.erikanote">
        <application>
        '''

    <service
        android:name=".TestService"
        android:enabled="true"
        android:exported="true">
    </service>

启动service

    Intent intent = new Intent(MainActivity.this, TestService.class);
    startService(intent);

如果应用退出，service即被销毁

如果service与Activity进行通信，那么就需要在service类中定义Binder对象。
Binder类中定义数据交换的方法。

    public class TestBinder extends Binder
    {
        public void sendMsg(String msg)
        {
            Log.v("ServiceTester", "msg content: " + msg);
        }
        public String getMsg()
        {
            return "an msg from TestService";
        }
    }
    private TestBinder binder = new TestBinder();

onBind()方法中返回该binder对象

    public IBinder onBind(Intent intent) {
        return binder;
    }

activity中进行与service数据交换    
创建ServiceConnection对象,之后通过binder对象与service进行通讯。    

    private TestService.TestBinder testBinder;
    private ServiceConnection serviceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            Log.v("ServiceTester", "Service Connected");
            testBinder = (TestService.TestBinder) service;
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            Log.v("ServiceTester", "Service Disconnected");
        }
    };

    Intent intent = new Intent(MainActivity.this, TesterService.class);
    bindService(intent, serviceConnection, Service.BIND_AUTO_CREATE);

    ...
    Log.v("ServiceTester", testBinder.getMsg());
    testBinder.sendMsg("A msg to TestService");

__Service的生命周期__

* 调用startService：    
onCreate() -> onStartCommand() -> service执行中 -> onDestroy()

* 调用bindService：    
onCreate() -> onBind() -> service执行中 -> onUnbind() -> onDestroy()

__Service的两种启动方法，有什么区别__

 1.在Context中通过public boolean bindService(Intent service,ServiceConnection conn,int flags) 方法来进行Service与Context的关联并启动，并且Service的生命周期依附于Context

### IntentService

IntentService无需重写onStartCommand，onBind方法，只需重写onHandleIntent即可    
IntentService会单独开启一个线程，完成操作之后自动退出，无需手动停止。    

__IntentService使用__

    public class TesterIntentService extends IntentService {
        public TesterIntentService() {
            super("TesterIntentService");
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            Log.v("ServiceTester", "IntentService Start");

            Log.v("ServiceTester", "IntentService Running");
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Log.v("ServiceTester", "IntentService Done");

        }

        @Override
        public void onDestroy() {
            super.onDestroy();
            Log.v("ServiceTester", "IntentService onDestroy");
        }
    }

Activity调用

    Intent intent = new Intent(MainActivity.this, TesterIntentService.class);
    startService(intent);

--------

# BroadcastReceiver

广播机制是一个发布-订阅模式,发布者负责发送广播,接收者监听广播.发布方不管接收方是否收到广播    
广播分为发送的Broadcast,接收的BroadcastReceiver和传递信息的Intent

广播分为普通广播,有序广播,本地广播,sticky广播

### 普通广播示例

AndroidManifest.xml定义

    <receiver android:name=".TesterBroadcastReceiver" >
        <intent-filter>
            <action android:name="intent.action.ACTION_TESTER"></action>
        </intent-filter>
    </receiver>

创建广播接收器BroadcastReceiver类

    public class TesterBroadcastReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Bundle bundle = intent.getExtras();
            Log.v("TesterBroadcastReceiver", bundle.getString("msg"));
        }
    }

注册广播

    testerBroadcastReceiver = new TesterBroadcastReceiver();
    registerReceiver(testerBroadcastReceiver, new IntentFilter("intent.action.ACTION_TESTER"));

发送广播

    Intent intent = new Intent("intent.action.ACTION_TESTER");
    Bundle bundle = new Bundle();
    bundle.putString("msg", "This is an Recv msg");
    intent.putExtras(bundle);

    sendBroadcast(new Intent(intent));

__有序广播__

通过sendOrderedBroadcast()发送,

AndroidManifest定义中加上priority, 数字越大优先级越高

    <receiver android:name=".TesterBroadcastReceiver" >
            <intent-filter android:priority="100">
                <action android:name="intent.action.ACTION_TESTER"></action>
            </intent-filter>
        </receiver>

发送广播使用sendOrderedBroadcast, 其中第二个参数是设置权限，即接收器必须具有相应的权限才能正常接收到广播,权限可以是系统权限也可以是自定义权限。

    sendOrderedBroadcast(new Intent(intent), null);

__本地广播__

其他的广播是全局的,发送后所有的应用都能接收到,本地广播是只限进程内能收到    
通常用法是关闭应用的时候发一条本地广播,每个Activity收到后执行finish()

__Sticky__

--------

## ContentProvider

### 作用    
为应用提供对外的CURD接口.系统或者其他应用可以通过此接口进行数据的查询添加删除操作.

### 实现

#### 定义Provider类    

    public class TesterProvider extends ContentProvider {
        public TesterProvider() {
        }

        @Override
        public int delete(Uri uri, String selection, String[] selectionArgs) {
            // Implement this to handle requests to delete one or more rows.
            Log.v("Provider", "delete "+uri + selection);
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public String getType(Uri uri) {
            // TODO: Implement this to handle requests for the MIME type of the data
            // at the given URI.
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public Uri insert(Uri uri, ContentValues values) {
            // TODO: Implement this to handle requests to insert a new row.
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public boolean onCreate() {
            // TODO: Implement this to initialize your content provider on startup.
            return false;
        }

        @Override
        public Cursor query(Uri uri, String[] projection, String selection,
                            String[] selectionArgs, String sortOrder) {
            // TODO: Implement this to handle query requests from clients.
            Log.v("Provider", "query "+uri);

            return null;
        }

        @Override
        public int update(Uri uri, ContentValues values, String selection,
                        String[] selectionArgs) {
            // TODO: Implement this to handle requests to update one or more rows.
            throw new UnsupportedOperationException("Not yet implemented");
        }
    }

#### 在AndroidManifest.xml文件中注册Provider.

    </application>
        <provider
        android:name=".TesterProvider"
        android:authorities="com.viator42.provider.tester"
        android:enabled="true"
        android:exported="true"></provider>
    </application>


#### 使用ContentResolver调用Provider    

    ContentResolver contentResolver = getContentResolver();
    Uri uri = Uri.parse("content://com.viator42.provider.tester");
    contentResolver.query(uri, null, null, null, null);

###注意    
* 调用的url应该加上content://前缀
* ContentProvider无法传入Content对象,应用不一定处在运行状态

