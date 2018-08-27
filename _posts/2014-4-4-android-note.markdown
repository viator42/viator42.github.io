---
layout: post
title:  "Android学习笔记"
date:   2015-04-04
categories: android
---

## 资源存储 /raw /asset 的区别

相同点是两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。

不同点

* res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。    
* res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹    

读取文件资源

读取res/raw下的文件资源，通过以下方式获取输入流来进行写操作

    InputStream is = getResources().openRawResource(R.id.filename);  

读取assets下的文件资源，通过以下方式获取输入流来进行写操作

    AssetManager am = null;  
    am = getAssets();  
    InputStream is = am.open("filename");

## 屏幕显示dp、sp、px的区别

dp (dip)设备独立像素,不依赖于实际的分辨率,根据屏幕密度自动进行变换(推荐使用这种单位)    
sp (scaled pixels)（放大像素）主要用于字体显示（best for textsize）,跟dp不同的是可以根据用户的字体大小首选项进行缩放    
px 实际像素数,在不同分辨率设备上显示效果不同,不推荐使用    

## 图片显示fitScale的定义

### Fresco

* center 	居中，无缩放。
* centerCrop 	保持宽高比缩小或放大，使得两边都大于或等于显示边界，且宽或高契合显示边界。居中显示。
* focusCrop 	同centerCrop, 但居中点不是中点，而是指定的某个点。
* centerInside 	缩放图片使两边都在显示边界内，居中显示。和 fitCenter 不同，不会对图片进行放大。如果图尺寸大于显示边界，则保持长宽比缩小图片。
* fitCenter 	保持宽高比，缩小或者放大，使得图片完全显示在显示边界内，且宽或高契合显示边界。居中显示。
* fitStart 	同上。但不居中，和显示边界左上对齐。
* fitEnd 	同fitCenter， 但不居中，和显示边界右下对齐。
* fitXY 	不保存宽高比，填充满显示边界。
* none 	如要使用tile mode显示, 需要设置为none

### 获取界面组件

	<Button
	android:id="@+id/btn1" />

	Button btn1 = findViewById(R.id.btn1);

----------

## Activity 相关

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

## IPC机制

IPC为跨进程通信

跨进程通信使用的时机: 1.破解单个应用所使用的最大内存数的限制,把耗内存的操作放到单独的进程中运行. 2.ContentProvider实际也是使用的进程间通信

__多进程模式__

开启多进程模式

    <activity
        ...
        android:process:":remote">
    >

添加了process属性之后这个activity就相当于运行在了一个单独的进程中,跟单进程相比

* 静态成员,单例模式失效
* 线程同步机制失效
* Application多次创建
* SharedReference可靠性下降

__进程间通信,使用AIDL__

实现一个运行在另一个进程中的Service

AndroidManifest.xml

    <service
        android:name=".BookManagerService"
        android:enabled="true"
        android:process=":remote"
        android:exported="true"></service>

被传输bean类需要实现Parcelable接口

    public class Book implements Parcelable {
        public int id;
        public String name;

        public Book(int id, String name) {
            this.id = id;
            this.name = name;
        }

        protected Book(Parcel in) {
            id = in.readInt();
            name = in.readString();
        }

        public static final Creator<Book> CREATOR = new Creator<Book>() {
            @Override
            public Book createFromParcel(Parcel in) {
                return new Book(in);
            }

            @Override
            public Book[] newArray(int size) {
                return new Book[size];
            }
        };

        @Override
        public int describeContents() {
            return 0;
        }

        @Override
        public void writeToParcel(Parcel dest, int flags) {
            dest.writeInt(id);
            dest.writeString(name);
        }
    }

创建一个AIDL文件,定义接口和接口方法

    interface IBookManagerInterface {
        List getBookList();
        void addBook(in int id, in String name);
    }

调用侧Activity

    public class MainActivity extends AppCompatActivity {

        private IBookManagerInterface iBookManagerInterface;

        private ServiceConnection serviceConnection = new ServiceConnection() {
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                iBookManagerInterface = IBookManagerInterface.Stub.asInterface(service);
            }

            @Override
            public void onServiceDisconnected(ComponentName name) {

            }
        };

        protected void onCreate(Bundle savedInstanceState) {
            ...
            // 创建服务
            Intent intent = new Intent(this, BookManagerService.class);
            //绑定服务和ServiceConnection
            bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE);
        }

        iBookManagerInterface.addBook(5, "New Book 005");
        List<Book> bookList = iBookManagerInterface.getBookList();
    }

被调用侧Service

    public class BookManagerService extends Service {
        public BookManagerService() {
        }

        private CopyOnWriteArrayList<Book> bookList;

        @Override
        public void onCreate() {
            super.onCreate();

            bookList = new CopyOnWriteArrayList<Book>();
            bookList.add(new Book(1, "Book 001"));
            bookList.add(new Book(2, "Book 002"));
            bookList.add(new Book(3, "Book 003"));
            bookList.add(new Book(4, "Book 004"));

        }

        //创建Binder
        private Binder mBinder = new IBookManagerInterface.Stub() {
            //实现AIDL中的接口方法
            @Override
            public List getBookList() throws RemoteException {
                return bookList;
            }

            @Override
            public void addBook(int id, String name) throws RemoteException {
                bookList.add(new Book(id, name));
            }
        };

        //绑定Binder
        @Override
        public IBinder onBind(Intent intent) {
            return mBinder;
        }
    }

--------

### Android 手势相关

首先定义GestureDetector

    GestureDetector mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {
        @Override
        //当手指按下的时候触发下面的方法
        public boolean onDown(MotionEvent e) {
            Toast.makeText(MainActivity.this, "Press Down", Toast.LENGTH_SHORT).show();
            return true;
        }

        @Override
        //当用户手指在屏幕上按下,而且还未移动和松开的时候触发这个方法
        public void onShowPress(MotionEvent e) {

        }

        @Override
        //当手指在屏幕上轻轻点击的时候触发下面的方法
        public boolean onSingleTapUp(MotionEvent e) {
            return false;
        }

        @Override
        //当手指在屏幕上滚动的时候触发这个方法
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            return false;
        }

        @Override
        //当用户手指在屏幕上长按的时候触发下面的方法
        public void onLongPress(MotionEvent e) {
            Toast.makeText(MainActivity.this, "Long pressed", Toast.LENGTH_SHORT).show();
        }

        @Override
        //当用户的手指在触摸屏上拖过的时候触发下面的方法,velocityX代表横向上的速度,velocityY代表纵向上的速度
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            return false;
        }
    });

组件应用touch事件, 将touch事件交给gesture处理

    button = (Button) findViewById(R.id.test_btn);
    button.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            mGestureDetector.onTouchEvent(event);
            return true;
        }
    });

--------

## View绘制的过程

各种组件都是ViewGroup的子类,ViewGroup是View的子类,view结构如下

![view结构](http://zhaowen.io/post/Android_Dive_Deep_In_View/decor_view.jpg)

Android的UI界面是一个树形结构,View的嵌套.子View在父View中，这些View都经过一个相同的流程最终显示到屏幕上    
每一个View的绘制都有Measure,Layout,Draw三个步骤的过程

measure -> onMeasure() -> layout -> onLayout() -> draw -> onDraw()

* Measure()    
测量视图的大小

* Layout()    
计算视图的位置

* Draw()    
视图绘制到屏幕上

meaure会经历performMeaure -> meaure -> onMeaure 的调用过程,其他两个也是同样    
视图绘制过程中会回调onMeasure(), onLayout(), onDraw()方法,自定义View的时候需要重写这三个方法    

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        super.onLayout(changed, l, t, r, b);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
    }

点击屏幕的事件顺序 DOWN->UP
滑动屏幕的事件顺序 DOWN->MOVE...->UP

widthMeasureSpec和heightMeasureSpec是测量的view的尺寸    
其中高两位是模式    
模式分为一下三种    

* EXACTLY 当View的layout_width和layout_height设置的是固定值的时候
* AT_MOST 控件的layout_width和layout_height设置成wrap_content的时候,控件的大小随着子控件的大小变化.
* UNSPECIFIED 不指定测量的大小

__重写onMeasure__

    //widthMeasureSpec和heightMeasureSpec是当前的测量宽高
    onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        //getMode用来获取测量模式
        int wMode = MeasureSpec.getMode(widthMeasureSpec);
        int hMode = MeasureSpec.getMode(widthMeasureSpec);

        //getSize用来从Spec中获取尺寸
        int width = MeasureSpec.getSize(widthMeasureSpec);
        int height = MeasureSpec.getSize(heightMeasureSpec);

        getPaddingLeft/Right/Top/Bottom获取Padding尺寸,需要在view中手动减去padding否则设置的padding值无效

        //mode和size重新组合成Spec
        int resultWidthMeasureSpec = MeasureSpec.makeMeasureSpec(width, wMode);
        int resultHeightMeasureSpec = MeasureSpec.makeMeasureSpec(height, hMode);

        //返回设置的尺寸
        super.onMeasure(resultWidthMeasureSpec, resultHeightMeasureSpec);
    }

__重写onLayout__

onLayout方法确定ViewGroup所有的子元素的位置

__自定义View需要注意的地方__

* View支持wrap_content    
* View支持padding,就是设置padding值的时候onMeasure函数返回的尺寸减去padding值    
* 使用post方法来传递信息,尽量不使用Handler   
* onDetatchedFromVindow方法加载动画和线程,onDetachedFromWindow方法停止动画和线程,防止一直占用系统资源导致OOM   
* View有滑动时处理滑动冲突

---------

## View的事件分发机制

每一个view都有三个方法来处理点击事件

* dispatchTouchEvent 用于事件的分发,如果传递到当前view,一定会调用这个   
* onInterruptTouchEvent 用于判断是否拦截某个事件,被dispatchTouchEvent调用
* onTouchEvent 拦截之后处理点击事件,被onInterruptTouchEvent调用

点击事件是递归传递的按顺序为 Activity -> Window -> View,从顶级ViewGroup一级一级向下直到目标view进行处理.    
每个view收到点击事件后会寻找遍历自己的子view分发事件.如果子view能处理则由子view处理,没有的话就由当前view进行处理.    
判断的依据是 1.能处理click事件 2.点击的坐标在view的范围内 3.view设置的允许响应事件enable=true.   

如果View设置了touchListener,onTouch的返回值(boolean)决定onTouchEvent是否会被回调.如果onTouch返回了false,则会调用上级的onTouchEvent

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        return super.dispatchTouchEvent(ev);
    }

MotionEvent 是手指触控屏幕产生的一系列事件, 主要有以下几种

* ACTION_DOWN    
* ACTION_UP    
* ACTION_MOVE

--------

### 处理滑动冲突的问题

* 内外滑动方向一致的情况
* 内外滑动方向不一致的情况

__解决方法__

1. 重写父容器的onInterruptTouchEvent方法, 判断是否拦截事件.    
2. 父容器不拦截任何的方法,

解决滑动冲突的一个示例

    public class ViewPagerX extends ViewPager {
        private int lastX;
        private int lastY;

        public ViewPagerX(Context context, AttributeSet attrs) {
            super(context, attrs);
        }

        @Override
        public boolean dispatchTouchEvent(MotionEvent ev) {
            int x = (int) ev.getX();
            int y = (int) ev.getY();

            switch (ev.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    super.requestDisallowInterceptTouchEvent(true);
                    break;

                case MotionEvent.ACTION_MOVE:
                    int deltaX = x - lastX;
                    int deltaY = y - lastY;

                    if (Math.abs(deltaX) < Math.abs(deltaY)) {
                        super.requestDisallowInterceptTouchEvent(false);
                    }
                    break;

                case MotionEvent.ACTION_UP:
                    super.requestDisallowInterceptTouchEvent(false);
                    break;
            }
            lastX = x;
            lastY = y;

            return super.dispatchTouchEvent(ev);
        }
    }

--------

## 自定义控件的实现过程

* 自定义属性的声明和获取    
    分析需要的自定义属性
    在res/values/attrs.xml定义声明
    在layout文件中进行使用
    在View的构造方法中进行获取

* 测量onMeasure

* 布局onLayout(ViewGroup)

* 绘制onDraw

* onTouchEvent

* onInterceptTouchEvent(ViewGroup)

* 状态的恢复与保存

---------

### 输出log日志

	import android.util.Log;
	Log.v(“log title”, “value");

2个参数,一个是tag,另一个是日志内容.

输出日志类型

* Log.v();  日志信息,对应级别verbose
* Log.i();  调试信息,对应级别info
* Log.w();  警告信息,对应级别warning
* Log.e();  错误信息,对应级别error

--------

### RelativeLayout属性

第一类:属性值为true或false
  
  Android:layout_centerHrizontal 水平居中    
  android:layout_centerVertical 垂直居中    
  android:layout_centerInparent 相对于父元素完全居中    
  android:layout_alignParentBottom 贴紧父元素的下边缘    
  android:layout_alignParentLeft 贴紧父元素的左边缘    
  android:layout_alignParentRight 贴紧父元素的右边缘    
  android:layout_alignParentTop 贴紧父元素的上边缘    
  android:layout_alignWithParentIfMissing 如果对应的兄弟元素找不到的话就以父元素做参照物    

第二类：属性值必须为id的引用名“@id/id-name”

  android:layout_below 在某元素的下方    
  android:layout_above 在某元素的的上方    
  android:layout_toLeftOf 在某元素的左边    
  android:layout_toRightOf 在某元素的右边    
  android:layout_alignTop 本元素的上边缘和某元素的的上边缘对齐    
  android:layout_alignLeft 本元素的左边缘和某元素的的左边缘对齐    
  android:layout_alignBottom 本元素的下边缘和某元素的的下边缘对齐    
  android:layout_alignRight 本元素的右边缘和某元素的的右边缘对齐    

第三类：属性值为具体的像素值，如30dip，40px

  android:layout_marginBottom 离某元素底边缘的距离    
  android:layout_marginLeft 离某元素左边缘的距离    
  android:layout_marginRight 离某元素右边缘的距离    
  android:layout_marginTop 离某元素上边缘的距离    

--------

## SharedReference

	//获取SharedPreferences实例     第一个参数是名称, 第二个是作用域
	SharedPreferences ref = getSharedPreferences("user", Context.MODE_PRIVATE);
	//获取数据,参数分别为key值和默认值(未取到值时的返回值)
	int id = ref.getInt("id", 0);
	ref.getString("username", "")

	//向SharedPreferences存储数据
	SharedPreferences ref = activity.getSharedPreferences("user", Context.MODE_PRIVATE);
	//获取editor对象
	SharedPreferences.Editor editor = ref.edit();
	//赋值
	editor.putInt("id", 1);
	editor.putString("tel", “1234567890");
	//完成后提交修改
	editor.commit();

--------

## Fragment (Android4 以上)

__Fragment生命周期__
![Fragment Lifescycle](http://indy-world.net/wp-content/uploads/2014/08/fragment_lifecycle.png "Fragment Lifescycle")

__Fragment的生命周期和Activity的生命周期关系__

onAttach()    
在Fragment和Activity关联时调用

onActivityCreated()        
在 Activity 的 onCreate() 方法已返回时调用

onDestroyView()    
Fragment从屏幕上移除的时候调用

onDetach()    
在取消Fragment与Activity的关联时调用

__实际演示__

 场景演示 : 切换到该Fragment

11-29 14:26:35.095: D/AppListFragment(7649): onAttach    
11-29 14:26:35.095: D/AppListFragment(7649): onCreate    
11-29 14:26:35.095: D/AppListFragment(7649): onCreateView    
11-29 14:26:35.100: D/AppListFragment(7649): onActivityCreated    
11-29 14:26:35.120: D/AppListFragment(7649): onStart    
11-29 14:26:35.120: D/AppListFragment(7649): onResume    

屏幕灭掉：

11-29 14:27:35.185: D/AppListFragment(7649): onPause    
11-29 14:27:35.205: D/AppListFragment(7649): onSaveInstanceState    
11-29 14:27:35.205: D/AppListFragment(7649): onStop    


屏幕解锁

11-29 14:33:13.240: D/AppListFragment(7649): onStart    
11-29 14:33:13.275: D/AppListFragment(7649): onResume    


切换到其他Fragment:
11-29 14:33:33.655: D/AppListFragment(7649): onPause    
11-29 14:33:33.655: D/AppListFragment(7649): onStop    
11-29 14:33:33.660: D/AppListFragment(7649): onDestroyView    


切换回本身的Fragment:

11-29 14:33:55.820: D/AppListFragment(7649): onCreateView    
11-29 14:33:55.825: D/AppListFragment(7649): onActivityCreated    
11-29 14:33:55.825: D/AppListFragment(7649): onStart    
11-29 14:33:55.825: D/AppListFragment(7649): onResume    

回到桌面

11-29 14:34:26.590: D/AppListFragment(7649): onPause    
11-29 14:34:26.880: D/AppListFragment(7649): onSaveInstanceState    
11-29 14:34:26.880: D/AppListFragment(7649): onStop    

回到应用

11-29 14:36:51.940: D/AppListFragment(7649): onStart    
11-29 14:36:51.940: D/AppListFragment(7649): onResume    


退出应用

11-29 14:37:03.020: D/AppListFragment(7649): onPause    
11-29 14:37:03.155: D/AppListFragment(7649): onStop    
11-29 14:37:03.155: D/AppListFragment(7649): onDestroyView    
11-29 14:37:03.165: D/AppListFragment(7649): onDestroy    
11-29 14:37:03.165: D/AppListFragment(7649): onDetach    

Fragment类定义
HelloFragment.java

public class HelloFragment extends Fragment

自定义fragment类继承Fragment

包含以下方法

    public class StatisticFragment extends Fragment {
        private OnFragmentInteractionListener mListener;
        private AppContext context;
        private Store store;

        private TextView productCountTextView;

        /**
         * Use this factory method to create a new instance of
         * this fragment using the provided parameters.
         *
         * @return A new instance of fragment StatisticFragment.
         */
        // TODO: Rename and change types and number of parameters
        public static StatisticFragment newInstance(String param1, String param2) {
            StatisticFragment fragment = new StatisticFragment();
            Bundle args = new Bundle();

            fragment.setArguments(args);
            return fragment;
        }

        public StatisticFragment() {
            // Required empty public constructor
        }

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            context = (AppContext) getActivity().getApplicationContext();
            store = context.getStore();

        }

        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                 Bundle savedInstanceState) {
            // Inflate the layout for this fragment
            View view = inflater.inflate(R.layout.fragment_statistic, container, false);

            productCountTextView = (TextView) view.findViewById(R.id.product_count);
            
            return view;
        }
    }

Activity.java 使用 Fragment

先定义FragmentManager

    private FragmentManager manager;
    private FragmentTransaction transaction;

设置Fragment

    manager = getFragmentManager();
    transaction = manager.beginTransaction();
    transaction.replace(R.id.content, fragment );
    transaction.commit();

### fragment传递参数

创建对象时使用bundle传递参数

    fragment1 = new Fragment1();
    Bundle bundle = new Bundle();
    bundle.putParcelable("data", model01);

    fragment1.setArguments(bundle);

    transaction = manager.beginTransaction();
    transaction.replace(R.id.content, fragment1);
    transaction.commit();

然后fragment获取参数

    public static Fragment1 newInstance(Model01 model01) {
        Fragment1 fragment = new Fragment1();
        Bundle args = new Bundle();
        args.putParcelable("data", model01);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            model01 = getArguments().getParcelable("data");
        }
    }

可以传递基本类型和parcelble对象

通过id获取fragment对象

    FragmentManager manager = getFragmentManager();
    Fragment1 fragment1 = (Fragment1) manager.findFragmentById(R.id.content);

获取到fragment实例之后就可以执行重新赋值的操作

--------

### ListView

简单列表,只显示一个textview,使用ArrayAdapter实现.
    
    private ArrayList<String> paymethodList;
    private ListView payMethodListView;

    payMethodListView = (ListView) view.findViewById(R.id.pay_method_list);

    paymethodList = new ArrayList<String>();
            paymethodList.add("支付宝");
            paymethodList.add("信用卡1");
            paymethodList.add("信用卡2");

    ArrayAdapter adapter = new ArrayAdapter(context, android.R.layout.simple_list_item_1, paymethodList);
            payMethodListView.setAdapter(adapter);
            payMethodListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                @Override
                public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                    payMethod = paymethodList.get(position);
                    setPayConfirmMode();
                }
            });

带单选框的listview

    listview=new ListView(this);

    //android.R.layout.simple_list_item_single_choice设置ListView项目条带单选按钮

    listview.setAdapter(new ArrayAdapter<String>(this,android.R.layout.simple_list_item_single_choice,getData()));

    listview.setChoiceMode(ListView.CHOICE_MODE_SINGLE);//如果不使用这个设置，选项中的radiobutton无法响应选中事件

    listview.setItemChecked(2, true);

    payMethodListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                @Override
                public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                    listview.setItemChecked(position, true);
                }
            });

动态添加数据

    adapter.add() 或者addAll()添加元素
    adapter.notifyDataSetChanged();      //通知listview数据改变


### ListView自定义布局

先创建一个layout resource file: layout.xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent" android:layout_height="match_parent">

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/name" />

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/price" />

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/address" />
	</LinearLayout>

创建一个Adaper类继承自BaseAdapter

	public class ContentAdaper extends BaseAdapter {
	    //填充数据的List
	    List<Map<String, Object>> list = new ArrayList<Map<String, Object>>();

	    //上下文
	    private Context context;

	    //用来导入布局
	    private LayoutInflater inflater = null;

	    //构造器
	    public ContentAdaper(List<Map<String, Object>> list, Context context) {
	        this.context = context;
	        this.list = list;
	        inflater = LayoutInflater.from(context);
	    }

	    @Override
	    public int getCount() {
	        // TODO Auto-generated method stub
	        return list.size();
	    }

	    @Override
	    public Object getItem(int arg0) {
	        // TODO Auto-generated method stub
	        return list.get(arg0);
	    }

	    @Override
	    public long getItemId(int position) {
	        // TODO Auto-generated method stub
	        return position;
	    }

	    //核心部分，返回Listview视图
	    @Override
	    public View getView(int position, View convertView, ViewGroup parent) {
	        // TODO Auto-generated method stub
	        ViewHolder holder = null;

	        if (convertView == null) {
	            holder = new ViewHolder();
	            convertView = inflater.inflate(R.layout.layout, null);
	            holder.name = (TextView) convertView.findViewById(R.id.name);
	            holder.price = (TextView) convertView.findViewById(R.id.price);
	            holder.address = (TextView) convertView.findViewById(R.id.address);
	            //为view设置标签
	            convertView.setTag(holder);
	        } else {
	            holder = (ViewHolder) convertView.getTag();
	        }

	        holder.name.setText(list.get(position).get("name").toString());
	        holder.price.setText(list.get(position).get("price").toString());
	        holder.address.setText(list.get(position).get("address").toString());

	        return convertView;
	    }

	    static class ViewHolder {
	        TextView name;
	        TextView price;
	        TextView address;
	    }
	}

最后在Activity中调用Adaper.

	List<Map<String,Object>> data = new ArrayList<Map<String,Object>>();
	Map item1 = new HashMap();
	item1.put("name", "Name1");
	item1.put("price", "450")
	item1.put("address", "address 101");
	data.add(item1);
	ContentAdaper adaper = new ContentAdaper(data, this);
	listView.setAdapter(adaper);

listView onClick事件设置

	position

	orderlistView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {

            }
        });

参数id用来返回某一行的标识id, 默认返回的是行号.需要重写Adapter的getItemId方法来获取id属性.

	@Override
    public long getItemId(int position) {
        // TODO Auto-generated method stub
        return (long)list.get(position).get("id");

    }

### ScrollTo方法操作

    mListView.scrollTo(x, y)

把显示区域移动到x, y坐标处

scrollTo和scrollBy的区别    

scrollTo是距离起始位置的偏移量,scrollBy是根据当前位置的移动

android scrollview 自动滚动到顶部或者底部

    //设置默认滚动到顶部
    scrollView.post(new Runnable() {
    
    @Override
    public void run() {
        // TODO Auto-generated method stub
        scrollView.fullScroll(ScrollView.FOCUS_UP);
    }
    });

    //设置默认滚动到底部
    scrollView.post(new Runnable() {
    
    @Override
    public void run() {
        // TODO Auto-generated method stub
        scrollView.fullScroll(ScrollView.FOCUS_DOWN);
    }
    });

    View中有EditText的时候页面加载焦点会自动放到第一个EditText上,取消方法是在跟节点添加以下属性

    android:focusable="true"
    android:focusableInTouchMode="true" 

--------

### 使用SimpleAddpter

    List listData = new ArrayList<Map<String,Object>>();

    Map line = new HashMap();
    line.put("id", 1);
    line.put("name", "beijing");
    listData.add(line);

    // 参数含义, context, 列表数据, 列表项布局, 显示的value名称, 名称对应的xml布局id
    SimpleAdapter adapter = new SimpleAdapter(this, listData, R.layout.spinner_item, new String[] {"name"}, new int[] {R.id.name});
    provinceSpinner.setAdapter(adapter);

SimpleAdapter构造函数的参数分别为

1. 上下文,一般为Activity类
2. 列表数据, 类型是List,每个元素是Map, 每个Map对象包含key,value键值对表示属性名和属性值.
3. 布局xml文件.
4. 字符串数组, 表示数据中key的属性名.
5. int数组, 数据中每个key属性名所对应的样式xml中的组件id.

最后两个参数数组项是一一对应的,把数据赋值给对应的控件显示

设置列表项选中时的事件

    provinceSpinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(RegisterActivity.this, ((Map)provinceSpinner.getItemAtPosition(i)).get("id").toString(), Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onNothingSelected(AdapterView<?> adapterView) {

            }
        });

--------

### Application 全局类配置

定义一个类可以继承Application, onCreate方法在应用启动时运行.

	public class AppContext extends Application
	{
	    private User user;
	    private Store store;

	    @Override
	    public void onCreate() {
	        super.onCreate();

	    }
	}

必须在manifest中指定类名.

	<application
	    android:name=".AppContext" >

在Activity中可以直接调用Application类的方法和变量
	
	AppContext context =  (AppContext)activity.getApplication();

### Handler和Message

从网络上下载图片的示例.

AndroidManifest.xml添加网络访问权限

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

导入Hanler和Message类.

    import android.os.Handler;
    import android.os.Message;

Activity类和OnCreate方法.

	private String imgUrl = "http://image_url";
    private final int IS_END = 1;
    private ProgressDialog dialog;

	protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        img = (ImageView)findViewById(R.id.img);
        loadImgBtn = (Button)findViewById(R.id.load_image);
        loadImgBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            	//新建一个网络访问的线程.
                new Thread(new MyThread()).start();
                dialog.show();

            }
        });

        dialog = new ProgressDialog(this);
        dialog.setTitle("提示");
        dialog.setMessage("正在下载,请稍后....");
        dialog.setCancelable(false);

    }

Activity类中新建一个Handler类并重写handleMessage方法.

	public Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg)
        {
        	//获取Message消息
            byte[] data = (byte[])msg.obj;
            Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
            img.setImageBitmap(bitmap);

            if(msg.what == IS_END)
            {
                dialog.dismiss();
            }

        }
    };


	public class MyThread implements Runnable
    {

        @Override
        public void run() {
            HttpClient httpClient = new DefaultHttpClient();
            HttpGet httpGet = new HttpGet(imgUrl);
            HttpResponse response = null;
            try{
                response = httpClient.execute(httpGet);
                if(response.getStatusLine().getStatusCode() == 200)
                {
                    byte[] data = EntityUtils.toByteArray(response.getEntity());
                    //Message.obtain()方法是从消息池中获取消息对象.
                    Message message = Message.obtain();
                    message.obj = data;
                    message.what = IS_END;
                    //发送Message消息
                    handler.sendMessage(message);

                }

            } catch (Exception e)
            {
                e.printStackTrace();
            }


        }
    }

发送Message消息方法

* 第一种方法

	Message message = Message.obtain();
	message.obj = data;
	message.what = IS_END;
	handler.sendMessage(message);

* 第二种方法, 新建message对象指定handler

    Message message = Message.obtain(handler);
    message.obj = data;
    message.what = IS_END;
    message.sendToTarget();

* 第三种方法, 新建message对象指定handler和what参数
	
	Message message = Message.obtain(handler, 1);
    message.obj = data;
    message.what = IS_END;
    message.sendToTarget();

### Looper对象

Activity中有一个默认的Looper对象,来处理子线程发送的消息.所以主线程接收子线程发送的消息就补需要定义looper
如果子线程需要获取主线程发送的消息就必须定义Lopper.

定义一个Handler对象

	private Handler handler;

主线程发送Message消息

	Message message = Message.obtain();
	message.obj = "Jack";
	handler.sendMessage(message);

子线程定义Loop消息队列并接收消息.
	public class MyThread implements Runnable
    {

        @Override
        public void run() {
            Looper.prepare();//循环消息队列

            handler = new Handler()
            {
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);
                    Log.v("--supai", "从UI主线程中获取消息-->" + msg.obj);

                }
            };

            Looper.loop();//直到消息队列循环结束

        }
    }

### 查看SHA1签名

(Mac OS)
    
    keytool -list -v -keystore ~/.android/debug.keystore

口令没有就直接enter

--------

### Spinner选择器

有dialog和dropdown两种显示方式.
需要使用适配器来完成填充数据.

获取数据, 设置显示内容name和值id.    

    ArrayList<Map<String, Object>> result= new ArrayList<Map<String, Object>>();
    Map province = new HashMap();
    province.put("id", jsonObject.getString("code"));
    province.put("name", jsonObject.getString("name"));
    result.add(province);


设置spinner    

    provinceSpinner = (Spinner)findViewById(R.id.province);
    
    SimpleAdapter adapter = new SimpleAdapter
        NewStoreActivity.this, result, R.layout.spinner_item, new String[] {"name"}, new int[] {R.id.alias});
    provinceSpinner.setAdapter(adapter);
    provinceSpinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
        @Override
        public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
            String code = ((Map) provinceSpinner.getItemAtPosition(i)).get("id").toString();
            new GetCityTask().execute(new Integer(code));

        }
        @Override
        public void onNothingSelected(AdapterView<?> adapterView) {

        }
    });

Spinner_item.xml布局文件

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent" android:layout_height="match_parent"
        android:orientation="horizontal">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:id="@+id/alias"
            android:gravity="center" />
    </LinearLayout>

### 自定义选项布局

/res/value下添加spinner_item.xml

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent" android:layout_height="match_parent"
        android:orientation="horizontal">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:id="@+id/name"
            android:gravity="center" />
    </LinearLayout>

--------

### AVD 模拟器添加sd卡

使用mksdcard程序,位置如下(MacOS系统)

    /Users/viator42/Library/Android/sdk/tools/mksdcard

命令格式 label:标签  size:大小 file:文件位置

    mksdcard -l <label> <size> <file>

--------

### 获取相册图片

方式1, 直接返回图片数据.

点击按钮触发打开相册的intent,此处putExtra("return-data", true)是直接返回图片数据,图片较小而且不需要剪裁的时候使用.否则使用临时文件存储.

    iconImageBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //打开相册
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("image/*");
            intent.putExtra("crop", "true");
            intent.putExtra("return-data", true);

            startActivityForResult(intent, 1000);
        }
    });

定义onActivityResult方法,获取Bitmap图片.

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == 1000 && resultCode == RESULT_OK) {
            if(data != null)
            {
                ContentResolver resolver = getContentResolver();
                Uri originalUri = data.getData();
                try {
                    MediaStore.Images.Media.getBitmap(resolver, originalUri);
    
                } catch (IOException e) {
                    e.printStackTrace();
                }
                if (logoBitmap != null)
                {
                    //获取Bitmap
                    iconImageBtn.setImageBitmap(logoBitmap);

                }

            }
            else
            {
                Toast.makeText(activity.getApplicationContext(), "图片读取失败", Toast.LENGTH_SHORT).show();
            }
        }

        super.onActivityResult(requestCode, resultCode, data);

    }

方式2, 使用临时文件.
如果图片需要剪裁则必须使用临时文件,否则会出异常.

定义文件

    private File sdcardTempFile = new File("/mnt/sdcard/", "tmp_pic_" + SystemClock.currentThreadTimeMillis() + ".jpg");

点击按钮触发打开相册的intent

    iconImageBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //打开相册
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("image/*");
            intent.putExtra("crop", "true");
            intent.putExtra("aspectX", 1);// 裁剪框比例
            intent.putExtra("aspectY", 1);
            intent.putExtra("outputX", StaticValues.ICON_WIDTH);// 输出图片大小
            intent.putExtra("outputY", StaticValues.ICON_HEIGHT);
            intent.putExtra("return-data", false);// 不返回图片数据(必须)
            intent.putExtra("output", Uri.fromFile(sdcardTempFile));

            startActivityForResult(intent, 1000);
            }
        });

定义onActivityResult方法,获取Bitmap图片.

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == 1000 && resultCode == RESULT_OK) {
            if(data != null)
            {
                logoBitmap = BitmapFactory.decodeFile(sdcardTempFile.getAbsolutePath());
                if (logoBitmap != null)
                {
                    iconImageBtn.setImageBitmap(logoBitmap);
                }s
            }
            else
            {
                Toast.makeText(activity.getApplicationContext(), "图片读取失败", Toast.LENGTH_SHORT).show();
            }
        }
        super.onActivityResult(requestCode, resultCode, data);
    }

### 图片尺寸压缩

    /**
     * 图片进行压缩处理,宽度固定,高度自动适配
     * @param bm
     *
     * @return
     */
    public static Bitmap zoomImg(Bitmap bm){
        // 获得图片的宽高
        int width = bm.getWidth();
        int height = bm.getHeight();

        if(width > 0 && height > 0)
        {
            if(width <= StaticValues.WIDTH_LIMIT)
            {
                //原图小于规定尺寸的不用缩放
                return bm;
            }
            else
            {
                int newWidth = StaticValues.WIDTH_LIMIT;
                int newHeight = (int)((newWidth * height) / width);
                // 计算缩放比例
                float scaleWidth = ((float) newWidth) / width;
                float scaleHeight = ((float) newHeight) / height;

                // 取得想要缩放的matrix参数
                Matrix matrix = new Matrix();
                matrix.postScale(scaleWidth, scaleHeight);
                // 得到新的图片
                Bitmap newbm = Bitmap.createBitmap(bm, 0, 0, width, height, matrix, true);
                return newbm;
            }
        }
        else
        {
            return bm;

        }
    }

### 图片裁剪

    Intent intent = new Intent("com.android.camera.action.CROP");
    intent.setDataAndType(imageUri, "image/*");
    intent.putExtra("crop", "true");
    intent.putExtra("aspectX", 1);
    intent.putExtra("aspectY", 1);
    intent.putExtra("outputX", StaticValues.ICON_WIDTH);
    intent.putExtra("outputY", StaticValues.ICON_HEIGHT);
    intent.putExtra("scale", true);
    intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri);
    intent.putExtra("return-data", false);
    intent.putExtra("outputFormat", Bitmap.CompressFormat.JPEG.toString());
    intent.putExtra("noFaceDetection", true); // no face detection
    startActivityForResult(intent, StaticValues.IMAGE_CROP);

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if(requestCode == StaticValues.IMAGE_CROP && resultCode == RESULT_OK)
        {
            //裁剪
            if(imageUri != null){
                logoBitmap = decodeUriAsBitmap(imageUri);//decode bitmap
                iconImageBtn.setImageBitmap(logoBitmap);
            }
            else
            {
                Toast.makeText(NewStoreActivity.this, "图片裁剪失败", Toast.LENGTH_SHORT).show();
            }
        }
    }
    
--------

### GPS获取当前位置

AndroidManifest.xml文件添加以下权限.
    
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="im.supai.supaimarketing" >
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    ...
    </manifest>

类必须继承LocationListener接口并重写相关函数.

    public class NewStoreActivity extends BaseAccivity implements LocationListener {
        
        //位置相关
        @Override
        public void onLocationChanged(Location location) {
            latitude = location.getLatitude();
            longitude = location.getLongitude();
        }

        @Override
        public void onStatusChanged(String s, int i, Bundle bundle) {
        }

        @Override
        public void onProviderEnabled(String s) {
        }

        @Override
        public void onProviderDisabled(String s) {
        }
    }

在Activity类中需要获取位置的地方新建一个LocationManager并发送请求即可.

    LocationManager locationManager = (LocationManager)getSystemService(Context.LOCATION_SERVICE);
    locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, (android.location.LocationListener) this);

--------

### ImageView/ImageButton 设置缩放

设置自适应调整大小和最大宽度.

    itemBtn.setAdjustViewBounds(true);
    itemBtn.setMaxWidth(100);

--------

### 根据权重设置控件宽度

    把view的宽度设置为0dp同时设置weight属性.
    
    android:layout_width="0dp"
    android:layout_weight="1"

--------

### AutoCompleteTextView的使用方法

AutoCompleteTextView可以为输入框提供补全提示功能

在layout中定义widget

    <AutoCompleteTextView
    android:id="@+id/search_bar" />

activity中实现

    private AutoCompleteTextView searchEditText;
    searchEditText = (AutoCompleteTextView) findViewById(R.id.search_bar);

    String[] ary = new String[]{"aaa", "bbb"};  //定义候选词列表,可以使用字符串数组或者ArrayList

    //定义adaper
    ArrayAdapter<String> searchEditAdapter=new ArrayAdapter<String>(this, 
        android.R.layout.simple_dropdown_item_1line, context.searchList);
    searchEditText.setAdapter(searchEditAdapter);

--------

## ASyncTask相关

一般声明在Activity类中作为内内部类.标注三个参数的类型
第一个参数表示要执行的任务通常是网络的路径。第二个参数表示进度的刻度，第三个参数表示任务执行的结果。

重写方法完成操作.

*  onPreExecute 表示任务执行之前的操作.    
*  doInBackground方法实现耗时的任务。    
*  onPostExecute 主要是更新UI的操作.    

        public class ListAllTask extends AsyncTask<String, Void, String>
        {
            @Override
            protected void onPreExecute() {
                super.onPreExecute();
            }

            @Override
            protected String doInBackground(String... params) {
                return null;
            }

            @Override
            protected void onPostExecute(String s) {
                super.onPostExecute(s);
            }
        }

运行AsyncTask
new ListAllTask().execute("aaa");

Android多线程编程主要使用的方法    
创建Thread,Handler Looper机制通信和使用异步框架ASyncTask    

Android 原生的 AsyncTask.java 是对线程池的一个封装，使用其自定义的 Executor 来调度线程的执行方式（并发还是串行），并使用 Handler 来完成子线程和主线程数据的共享。

ASyncTask需要继承父类并定义三个泛型类型

    private class MyTask extends AsyncTask<Void, Void, Void> { ... }

* Params  传入的参数,这里是可变长参数
* Progress    处理过程中的进度信息
* Result  返回的结果信息

ASyncTask需要重写三个方法    
* onPreExecute() 该方法将在执行实际的后台操作前被UI thread调用。可以在该方法中做一些准备工作，如在界面上显示一个进度条。
* doInBackground(Params...), 将在onPreExecute 方法执行后马上执行，该方法运行在后台线程中。这里将主要负责执行那些很耗时的后台计算工作。可以调用 publishProgress方法来更新实时的任务进度。此方法中不能进行修改UI操作
* onProgressUpdate(Progress...) UI thread将调用这个方法从而在界面上展示任务的进展情况，例如通过一个进度条进行展示。
* onPostExecute(Result), 在doInBackground 执行完成后，onPostExecute 方法将被UI thread调用，后台的计算结果将通过该方法传递到UI thread.

ASyncTask必须在UI线程中创建,execute方法必须在UI thread中调用,不要手动的调用onPreExecute(), onPostExecute(Result)，doInBackground(Params...), onProgressUpdate(Progress...)这几个方法

ASyncTask的缺点: 后台线程只有一个,多个任务线性执行

参考    
https://segmentfault.com/a/1190000002872278    
http://www.infoq.com/cn/articles/android-asynctask    

## ASyncTask源码整理

主要使用的技术是ThreadPoll线程池和Handler Looper实现

ASyncTask构造函数中创建FutureTask, WorkerRunnable<Params, Result>对象     
WorkerRunnable继承了Runnable接口,call方法调用doInBackground()方法,结果返回Result对象,最后调用postResult方法返回结果
FutureTask实现了done方法在任务完成时调用postResultIfNotInvoked() -> postResult()返回结果

execute()方法执行    

调用execute() -> executeOnExecutor()

executeOnExecutor()    

    onPreExecute()
    mWorker.mParams = params;   //Callable对象参数赋值
    exec.execute(mFuture);  //线程添加到线程池

WorkerRunnable的对象中

    mWorker = new WorkerRunnable<Params, Result>() {
            public Result call() throws Exception {
                mTaskInvoked.set(true);
                Result result = null;
                try {
                    Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
                    //noinspection unchecked
                    result = doInBackground(mParams);
                    Binder.flushPendingCommands();
                } catch (Throwable tr) {
                    mCancelled.set(true);
                    throw tr;
                } finally {
                    postResult(result);
                }
                return result;
            }
        };

postResult方法会把使用Handler Messageer把结果发送给主线程

    private Result postResult(Result result) {
        @SuppressWarnings("unchecked")
        Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
                new AsyncTaskResult<Result>(this, result));
        message.sendToTarget();
        return result;
    }

主线程Handler接收返回的结果

    private static class InternalHandler extends Handler {
        public InternalHandler() {
            super(Looper.getMainLooper());
        }

        @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
        @Override
        public void handleMessage(Message msg) {
            AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
            switch (msg.what) {
                case MESSAGE_POST_RESULT:
                    // There is only one result
                    result.mTask.finish(result.mData[0]);
                    break;
                case MESSAGE_POST_PROGRESS:
                    result.mTask.onProgressUpdate(result.mData);
                    break;
            }
        }
    }

finish() -> onPostExecute()

    private void finish(Result result) {
        if (isCancelled()) {
            onCancelled(result);
        } else {
            onPostExecute(result);
        }
        mStatus = Status.FINISHED;
    }

--------

### Android ToolBar使用

ToolBar是Android 5.0（API Level 21）之后用来取代ActionBar的

1.AndroidMenifest.xml 中修改theme为NoActionBar

    android:theme="@style/AppTheme.NoActionBar"

2.activity.xml中添加ToolBar

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/AppTheme.PopupOverlay" >

            <!--自定义控件-->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:text="自定义控件" />

        </android.support.v7.widget.Toolbar>
    </android.support.design.widget.AppBarLayout>

Toolbar中可以添加自定义控件

3.activity.java

    public class MainActivity extends AppCompatActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            //定义和初始化ToolBar
            Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            toolbar.setNavigationIcon(R.mipmap.ic_launcher);//设置导航栏图标
            toolbar.setLogo(R.mipmap.ic_launcher);//设置app logo
            toolbar.setTitle("Title");//设置主标题
            toolbar.setSubtitle("Subtitle");//设置子标题

        }
    }

--------

## Menu相关

Android4.0以上的系统Menu默认在ToolBar的右上

Menu的定义方法

1. 在res/menu下创建一个menu的样式文件.

    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android">
        <item
            android:id="@+id/item_1"
            android:title="ITEM_1"
            />
        <item
            android:id="@+id/item_2"
            android:title="ITEM_2"
            />
        <item
            android:id="@+id/item_3"
            android:title="ITEM_3"
            />
    </menu>

2. Activity中加载这个menu

    public boolean onCreateOptionsMenu(Menu menu) {
        super.onCreateOptionsMenu(menu);
        getMenuInflater().inflate(R.menu.mainpage_menu, menu);
        return true;
    }

    //Menu item的点击事件定义
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId())
        {
            case R.id.item_1:
                Toast.makeText(MainActivity.this, "Menu item 1", Toast.LENGTH_SHORT).show();
                break;

            case R.id.item_2:
                Toast.makeText(MainActivity.this, "Menu item 2", Toast.LENGTH_SHORT).show();
                break;

            case R.id.item_3:
                Toast.makeText(MainActivity.this, "Menu item 3", Toast.LENGTH_SHORT).show();
                break;

        }
        return super.onOptionsItemSelected(item);
    }

    //动态调整菜单,比如说控制菜单项是否显示
    public boolean onPrepareOptionsMenu(Menu menu) {
        MenuItem itemAddressSetDefault = menu.findItem(R.id.action_address_set_default);
        itemAddressSetDefault.setVisible(false);

        return super.onPrepareOptionsMenu(menu);
    }

--------

### 定时器Timer和TimerTask

在开发中我们有时会有这样的需求，即在固定的每隔一段时间执行某一个任务。比如UI上的控件需要随着时间改变，我们可以使用Java为我们提供的计时器的工具类，即Timer和TimerTask。   

Timer是一个普通的类，其中有几个重要的方法；而TimerTask则是一个抽象类，其中有一个抽象方法run()，类似线程中的run()方法，我们使用Timer创建一个他的对象，然后使用这对象的schedule方法来完成这种间隔的操作。    

schedule方法有三个参数    

* 第一个参数就是TimerTask类型的对象，我们实现TimerTask的run()方法就是要周期执行的一个任务；    
* 第二个参数有两种类型，第一种是long类型，表示多长时间后开始执行，另一种是Date类型，表示从那个时间后开始执行；    
* 第三个参数就是执行的周期，为long类型。    

schedule方法还有一种两个参数的执行重载，第一个参数仍然是TimerTask，第二个表示为long的形式表示多长时间后执行一次，为Date就表示某个时间后执行一次。     

Timer就是一个线程，使用schedule方法完成对TimerTask的调度，多个TimerTask可以共用一个Timer，也就是说Timer对象调用一次schedule方法就是创建了一个线程，并且调用一次schedule后TimerTask是无限制的循环下去的，使用Timer的cancel()停止操作。当然同一个Timer执行一次cancel()方法后，所有Timer线程都被终止。    

#### 用法：

    //true 说明这个timer以daemon方式运行（优先级低，程序结束timer也自动结束）   
    java.util.Timer timer = new java.util.Timer(true);  
      
    TimerTask task = new TimerTask() {  
       public void run() {  
       //每次需要执行的代码放到这里面。     
       }     
    };  
      
    //以下是几种调度task的方法：  
      
    //time为Date类型：在指定时间执行一次。  
    timer.schedule(task, time);  
      
    //firstTime为Date类型,period为long，表示从firstTime时刻开始，每隔period毫秒执行一次。  
    timer.schedule(task, firstTime, period);     
      
    //delay 为long类型：从现在起过delay毫秒执行一次。  
    timer.schedule(task, delay);  
      
    //delay为long,period为long：从现在起过delay毫秒以后，每隔period毫秒执行一次。  
    timer.schedule(task, delay, period);  

#### 示例:

    import android.app.Activity;  
    import android.os.Bundle;  
    import android.os.Handler;  
    import android.os.Message;  
      
    import java.util.Timer;  
    import java.util.TimerTask;  
      
    public class TimerTaskActivity extends Activity {  
      
        private Timer mTimer;  
      
        @Override  
        protected void onCreate(Bundle savedInstanceState) {  
            super.onCreate(savedInstanceState);  
            // init timer  
            mTimer = new Timer();  
            // start timer task  
            setTimerTask();  
        }  
      
        @Override  
        protected void onDestroy() {  
            super.onDestroy();  
            // cancel timer  
            mTimer.cancel();  
        }  
      
        private void setTimerTask() {  
            mTimer.schedule(new TimerTask() {  
                @Override  
                public void run() {  
                    Message message = new Message();  
                    message.what = 1;  
                    doActionHandler.sendMessage(message);  
                }  
            }, 1000, 1000/* 表示1000毫秒之後，每隔1000毫秒執行一次 */);  
        }  
      
        /** 
         * do some action 
         */  
        private Handler doActionHandler = new Handler() {  
            @Override  
            public void handleMessage(Message msg) {  
                super.handleMessage(msg);  
                int msgId = msg.what;  
                switch (msgId) {  
                    case 1:  
                        // do some action  
                        break;  
                    default:  
                        break;  
                }  
            }  
        };  
    }  

__Android平台从其它线程访问主线程主要有以下几种方式：__

* Activity.runOnUiThread(Runnable)    
* View.post(Runnable)    
* View.postDelayed(Runnable, long)    
* Handler    

__View.post__

View.post方法的主要作用是保证指定的任务顺序执行,post方法的执行时间是在view的layout过程结束之后.
post方法会创建一个Runnable,但还是在主线程/UI线程中执行的.    
所以可以直接调用UI的元素    
主要用途是获取view的宽高尺寸,如果直接获取的话值为0,因为measure过程还没有完成

    msgTextView.setText(String.valueOf(costomWidget.getMeasuredWidth()) + "  " + String.valueOf(costomWidget.getMeasuredHeight()));


### Activity的创建过程

## ASyncTask的源码

## HandlerLooper的实现机制

## ImageView

android:scaleType是控制图片如何resized/moved来匹对ImageView的size。

ImageView.ScaleType / android:scaleType值的意义区别：

* CENTER  按图片的原来size居中显示，当图片长/宽超过View的长/宽，则截取图片的居中部分显示    
* CENTER_CROP 按比例扩大图片的size居中显示，使得图片长(宽)等于或大于View的长(宽)    
* CENTER_INSIDE   将图片的内容完整居中显示，通过按比例缩小或原来的size使得图片长/宽等于或小于View的长/宽
* FIT_CENTER  把图片按比例扩大/缩小到View的宽度，居中显示
* FIT_END 把图片按比例扩大/缩小到View的宽度，显示在View的下部分位置
* FIT_START   把图片按比例扩大/缩小到View的宽度，显示在View的上部分位置
* FIT_XY  把图片不按比例扩大/缩小到View的大小显示
* MATRIX  用矩阵来绘制，动态缩小放大图片来显示。

#### Bitmap的四种属性,也就是指图片压缩质量参数

* public static final Bitmap.Config __ALPHA_8__
* public static final Bitmap.Config __ARGB_4444__
* public static final Bitmap.Config __ARGB_8888__
* public static final Bitmap.Config __RGB_565__

我们知道ARGB指的是一种色彩模式，里面A代表Alpha，R表示red，G表示green，B表示blue，其实所有的可见色都是右红绿蓝组成的，所以红绿蓝又称为三原色，每个原色都存储着所表示颜色的信息值

说白了就ALPHA_8就是Alpha由8位组成
ARGB_4444就是由4个4位组成即16位，
ARGB_8888就是由4个8位组成即32位，
RGB_565就是R为5位，G为6位，B为5位共16位

由此可见：
ALPHA_8 代表8位Alpha位图
ARGB_4444 代表16位ARGB位图
ARGB_8888 代表32位ARGB位图
RGB_565 代表8位RGB位图

位图位数越高代表其可以存储的颜色信息越多，当然图像也就越逼真。

设置位图的压缩模式

    BitmapFactory.Options options = new BitmapFactory.Options();
            options2.inPreferredConfig = Bitmap.Config.RGB_565;
    bm = BitmapFactory.decodeFile("filepath/file.jpg", options);

## Service相关

__定义Service类__

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


## ViewPager

继承自ViewGroup, 专门用以实现左右滑动切换View的效果

### 使用方法

使用方法和ListView类似,先定义一个Adapter类继承PagerAdapter

    public class MainpageBannerViewPagerAdapter extends PagerAdapter {
        private Context context;
        private ArrayList<SimpleDraweeView> imageViews;

        public MainpageBannerViewPagerAdapter(Context context, ArrayList<SimpleDraweeView> imageViews) {
            this.context = context;
            this.imageViews = imageViews;
        }

        @Override
        public int getCount() {
            return imageViews.size();
        }

        @Override
        public boolean isViewFromObject(View view, Object object) {
            return view == object;
        }

        @Override
        public ImageView instantiateItem(ViewGroup container, int position) {
            SimpleDraweeView imageView = imageViews.get(position);
            container.addView(imageView);
            return imageView;
        }

    }

__重载方法__

* getCount()    获取ViewPager实际绘制的列表项的数量    
* instantiateItem(ViewGroup container, int position)    获取当前列表项，也就是正在显示的当前页，当前ViewPager所在的位置    
* destroyItem(ViewGroup container,int position,Object object) 当前项离开屏幕时回调本方法，在本方法中需要将当前想从ViewPager中移除    
* isViewFromObject(View view,Object obj)    判断view和obj是否为同一个view    

最后ViewPager关联Adapter即可

    viewPager.setAdapter(goodsViewPagerAdapter);

## SSL加密握手过程

1. 客户端将它所支持的算法列表和一个用作产生密钥的随机数发送给服务器
2. 服务器从算法列表中选择一种加密算法，并将它和一份包含服务器公用密钥的证书发送给客户端；该证书还包含了用于认证目的的服务器标识，服务器同时还提供了一个用作产生密钥的随机数
3. 客户端验证服务器证书,如果验证失败,则停止建立SSL.验证成功进行下一步骤
4. 客户端为本次会话生成预备主密码,并用服务器公钥加密后发送给服务器.
5. 如果服务器要求验证客户端身份,则检查客户端的CA证书是否可信,如果不在可信列表中则结束会话.检查通过后服务器用私钥解密收到的预备主密码(pre master key),并生成本次会话的主密码(master key).客户端与服务端使用pre master key独立计算出master key.
6. 客户端通知服务端以后发送的消息均使用master key加密.通知服务端完成这次SSL握手
7. 服务端通知客户端完成SSL握手

## Android 单元测试

工程采用MVP架构时对Presenter的接口进行测试

单元测试使用Junit,AS在创建项目时已经支持,不需要手动导入

__待测试的类__

    public class Calculator {
        public int add(int one, int another) {
            // 为了简单起见，暂不考虑溢出等情况。
            return one + another;
        }

        public int multiply(int one, int another) {
            // 为了简单起见，暂不考虑溢出等情况。
            return one * another;
        }
    }

__测试类__

    public class CalculatorTest {
        Calculator mCalculator;

        @Before
        public void setup() {
            mCalculator = new Calculator();
        }

        @Test
        public void testAdd() throws Exception {
            int sum = mCalculator.add(1, 2);
            assertEquals(3, sum);  //为了简洁，往往会static import Assert里面的所有方法。
        }

        @Test
        public void testMultiply() throws Exception {
            int product = mCalculator.multiply(2, 4);
            assertEquals(8, product);
        }

    }

__方法注解__

@Test 是测试方法,运行单元测试时依次执行   
@Before 是每个测试方法调用之前运行的方法,一般用来进行变量初始化
@After 每个测试方法结束后调用

__验证结果的方法__

assertEquals(expected, actual)    
验证expected的值跟actual是一样的，如果是一样的话，测试通过，不然的话，测试失败。如果传入的是object，那么这里的对比用的是equals()

assertEquals(expected, actual, tolerance)    
这里传入的expected和actual是float或double类型的，大家知道计算机表示浮点型数据都有一定的偏差，所以哪怕理论上他们是相等的，但是用计算机表示出来则可能不是，所以这里运行传入一个偏差值。如果两个数的差异在这个偏差值之内，则测试通过，否者测试失败。

assertTrue(boolean condition)    
验证contidion的值是true

assertFalse(boolean condition)    
验证contidion的值是false

assertNull(Object obj)    
验证obj的值是null

assertNotNull(Object obj)    
验证obj的值不是null

assertSame(expected, actual)    
验证expected和actual是同一个对象，即指向同一个对象

assertNotSame(expected, actual)    
验证expected和actual不是同一个对象，即指向不同的对象

fail()    
让测试方法失败

## Android 内存机制和优化方法

Dalvik内存堆(Heap)栈(Stack)

堆(Heap:   内存数据区,存储对象实例数据,方法内部变量(复杂类型),动态属性,由new创建的对象和数组,
栈(Stack:  内存指令区,基本类型的变量和对象的引用变量,方法内部变量(基本数据类型),静态属性,类方法,对象地址,常量

### 对象实例数据

实际上是保存对象实例的属性，属性的类型和对象本身的类型标记等，但是不保存实例的方法。实例的方法是属于数据指令，是保存在Stack里面，也就是上面表格里面的类方法。

对象实例在Heap中分配好以后，会在stack中保存一个4字节的Heap内存地址，用来查找对象的实例。因为在Stack里面会用到Heap的实例，特别是调用实例的时候需要传入一个this指针。

### 方法内部变量

类方法的内部变量分为两种情况：简单类型保存在Stack中；对象类型在Stack中保存地址，在Heap 中保存值。

### 非静态方法和静态方法

非静态方法有一个隐含的传入参数，这个参数是dalvik虚拟机传进去的，这个隐含参数就是对象实例在Stack中的地址指针。因此非静态方法（在Stack中的指令代码）总是可以找到自己的专用数据（在Heap 中的对象属性值）。

　　当然非静态方法也必须获得该隐含参数，因此非静态方法在调用前，必须先new一个对象实例，获得Stack中的地址指针，否则dalvik虚拟机将无法将隐含参数传给非静态方法。

　　静态方法没有隐含参数，因此也不需要new对象，只要class文件被ClassLoader load进入JVM的Stack，该静态方法即可被调用。所以我们可以直接使用类名调用类的方法。当然此时静态方法是存取不到Heap 中的对象属性的。

### 静态属性和动态属性

静态属性是保存在Stack中的，而不同于动态属性保存在Heap 中。正因为都是在Stack中，而Stack中指令和数据都是定长的，因此很容易算出偏移量，所以类方法(静态和非静态)都可以访问到类的静态属性。也正因为静态属性被保存在Stack中，所以具有了全局属性。

### 小结

　　Java的堆是一个运行时数据区,类的(对象从中分配空间。这些对象通过new、newarray、anewarray和multianewarray等指令建立，它们不需要程序代码来显式的释放。

　　堆是由垃圾回收来负责的，堆的优势是可以动态地分配内存大小，生存期也不必事先告诉编译器，因为它是在运行时动态分配内存的，Java的垃圾收集器会自动收走这些不再使用的数据。但缺点是，由于要在运行时动态分配内存，存取速度较慢。

　　栈的优势是，存取速度比堆要快，仅次于寄存器，栈数据可以共享。但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。栈中主要存放一些基本类型的变量（,int, short, long, byte, float, double, boolean, char）和对象句柄。

　　对比上面的解析可以看出，其实Java处理Heap和Stack的大致原理跟C++是一样的。只是多了一个内存回收机制，让程序员不用主动调用delete释放内存。就像在C++里面，一般使用new申请的内存才会放到堆里面，而一般的临时变量都是放到栈里面去。

### Android的GC如何回收内存

Android的每个应用程序都会使用一个专有的Dalvik虚拟机实例来运行，它是由Zygote服务进程孵化出来的，也就是说每个应用程序都是在属于自己的进程中运行的。

Android为不同类型的进程分配了不同的内存使用上限，如果程序在运行过程中出现了内存泄漏的而造成应用进程使用的内存超过了这个上限，则会被系统视为内存泄漏，从而被kill掉，这使得仅仅自己的进程被kill掉，而不会影响其他进程（如果是system_process等系统进程出问题的话，则会引起系统重启）。

Android的GC是自动运行的,不再被引用的对象就会被GC回收.因此对于我们已经不需要使用的对象，我们可以把它设置为null，这样当GC运行的时候，就好遍历到你这个对象已经没有引用，会自动把该对象占用的内存回收。

### 查看内存使用情况

adb命令

    adb shell dumpsys meminfo [package name]

代码中查看

    ActivityManager activityManager = (ActivityManager) getSystemService(ACTIVITY_SERVICE);

    获取堆内存大小(单位M)
    activityManager.getMemoryClass()
    activityManager.getLargeMemoryClass()

如果在AndroidManifest.xml中的<application>标签下添加属性android:largeHeap=“true” ,将启用大堆内存模式

## viewGroup和子控件

自己定义的GridView,ListView点击时会没有反应,是因为其中的Item项中有Button，CheckBox等子控件占据了焦点事件    
解决方法是在Item布局的根布局上定义android:descendantFocusability属性来控制    
属性值为

* beforeDescendants：viewgroup会优先其子类控件而获取到焦点   
* afterDescendants：viewgroup只有当其子类控件不需要获取焦点时才获取焦点   
* blocksDescendants：viewgroup会覆盖子类控件而直接获得焦点   

--------

## Recycler View添加滚动到底部自动加载更多

RecyclerView没有提供滚动到底部加载更多的功能,需要自己实现    

自定义一个OnScrollListener

    public abstract class EndlessRecyclerOnScrollListener extends RecyclerView.OnScrollListener {
        public static String TAG = EndlessRecyclerOnScrollListener.class.getSimpleName();

        private int previousTotal = 0; // The total number of items in the dataset after the last load
        private boolean loading = true; // True if we are still waiting for the last set of data to load.
        private int visibleThreshold = 5; // The minimum amount of items to have below your current scroll position before loading more.
        int firstVisibleItem, visibleItemCount, totalItemCount;

        private int current_page = 1;

        private GridLayoutManager layoutManager;

        public EndlessRecyclerOnScrollListener(GridLayoutManager layoutManager) {
            this.layoutManager = layoutManager;
        }

        @Override
        public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
            super.onScrolled(recyclerView, dx, dy);

            visibleItemCount = recyclerView.getChildCount();
            totalItemCount = layoutManager.getItemCount();
            firstVisibleItem = layoutManager.findFirstVisibleItemPosition();

            if (loading) {
                if (totalItemCount > previousTotal) {
                    loading = false;
                    previousTotal = totalItemCount;
                }
            }
            if (!loading && (totalItemCount - visibleItemCount)
                    <= (firstVisibleItem + visibleThreshold)) {
                // End has been reached

                // Do something
                current_page++;

                onLoadMore(current_page);

                loading = true;
            }
        }

        public abstract void onLoadMore(int current_page);
    }

GridView和RecyclerView只有LayoutManager不同

    public abstract class EndlessGridRecyclerOnScrollListener extends
            RecyclerView.OnScrollListener {

        private int previousTotal = 0;
        private boolean loading = true;
        int firstVisibleItem, visibleItemCount, totalItemCount;

        private int currentPage = 1;

        private GridLayoutManager gridLayoutManager;

        public EndlessGridRecyclerOnScrollListener(
                GridLayoutManager gridLayoutManager) {
            this.gridLayoutManager = gridLayoutManager;
        }

        @Override
        public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
            super.onScrolled(recyclerView, dx, dy);

            CommonUtils.log(String.valueOf(dx) + " " + String.valueOf(dy) );

            visibleItemCount = recyclerView.getChildCount();
            totalItemCount = gridLayoutManager.getItemCount();
            firstVisibleItem = gridLayoutManager.findFirstVisibleItemPosition();


            if (loading) {
                if (totalItemCount > previousTotal) {
                    loading = false;
                    previousTotal = totalItemCount;
                }
            }
            if (!loading && (totalItemCount - visibleItemCount) <= firstVisibleItem) {
                currentPage++;
                onLoadMore(currentPage);
                loading = true;
            }
        }

        public abstract void onLoadMore(int currentPage);
    }

在Activity中使用

    listView.addOnScrollListener(new EndlessGridRecyclerOnScrollListener(layoutManager) {
        @Override
        public void onLoadMore(int currentPage) {
            loadPage();
        }
    });

如果RecyclerView包裹在NestedScrollView中,添加ScrollListener必须添加到NestedScrollView上,不然会出现RecyclerView不响应事件,无限循环加载的问题.

    <android.support.v4.widget.NestedScrollView xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior=    "@string/appbar_scrolling_view_behavior"
        tools:context=".module.branddetail.BrandDetailActivity"
        tools:showIn="@layout/activity_brand_detail"
        android:id="@+id/scroll_view">

        <android.support.v7.widget.RecyclerView
            android:id="@+id/brand_goods_list"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_alignParentBottom="true"
            android:layout_alignParentLeft="true"
            android:layout_alignParentStart="true"
            android:nestedScrollingEnabled="false"
            />

    </android.support.v4.widget.NestedScrollView>

继承NestedScrollView.OnScrollChangeListener

    public abstract class EndlessParentScrollListener implements NestedScrollView.OnScrollChangeListener {
        // The current offset index of data you have loaded
        private int currentPage = 0;
        // The total number of items in the dataset after the last load
        private int previousTotalItemCount = 0;
        // True if we are still waiting for the last set of data to load.
        private boolean loading = true;
        // Sets the starting page index
        private int startingPageIndex = 0;
        // The minimum amount of pixels to have below your current scroll position
        // before loading more.
        private int visibleThresholdDistance = 300;

        GridLayoutManager mLayoutManager;

        public EndlessParentScrollListener(GridLayoutManager layoutManager) {
            this.mLayoutManager = layoutManager;
        }

        @Override
        public void onScrollChange(NestedScrollView scrollView, int x, int y, int oldx, int oldy) {
            // We take the last son in the scrollview
            View view = scrollView.getChildAt(scrollView.getChildCount() - 1);
            int distanceToEnd = (view.getBottom() - (scrollView.getHeight() + scrollView.getScrollY()));

            int totalItemCount = mLayoutManager.getItemCount();
            // If the total item count is zero and the previous isn't, assume the
            // list is invalidated and should be reset back to initial state
            if (totalItemCount < previousTotalItemCount) {
                this.currentPage = this.startingPageIndex;
                this.previousTotalItemCount = totalItemCount;
                if (totalItemCount == 0) {
                    this.loading = true;
                }
            }

            // If it’s still loading, we check to see if the dataset count has
            // changed, if so we conclude it has finished loading and update the current page
            // number and total item count.
            if (loading && (totalItemCount > previousTotalItemCount)) {
                loading = false;
                previousTotalItemCount = totalItemCount;
            }

            // If it isn’t currently loading, we check to see if we have breached
            // the visibleThreshold and need to reload more data.
            // If we do need to reload some more data, we execute onLoadMore to fetch the data.
            // threshold should reflect how many total columns there are too
            if (!loading && distanceToEnd <= visibleThresholdDistance) {
                currentPage++;
                onLoadMore(currentPage, totalItemCount);
                loading = true;
            }
        }

        // Defines the process for actually loading more data based on page
        public abstract void onLoadMore(int page, int totalItemsCount);
    }

Activity中使用

    nestedScrollView.setOnScrollChangeListener(new EndlessParentScrollListener(gridLayoutManager) {
        @Override
        public void onLoadMore(int page, int totalItemsCount) {
            load();
        }
    });

--------

## View注入框架ButterKnife

对一个成员变量使用@BindView注解，并传入一个View ID， ButterKnife 就能够帮你找到对应的View，并自动的进行转换       
ButterKnife通过编译时对代码处理来执行View的查找,不占用性能

### 使用方法

导入库,添加依赖

    dependencies {
    implementation 'com.jakewharton:butterknife:8.8.1'
    annotationProcessor 'com.jakewharton:butterknife-compiler:8.8.1'
    }

Activity中使用

    //声明view的时候添加@InjectView注解
    @BindView(R.id.ok_btn) //控件对应的ID
    Button mBtn;

    @BindView(R.id.title_text)
    TextView mTitleTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity);

        //ButterKnife初始化
        ButterKnife.inject(this);

        //这样之后就可以直接使用变量了
        mTitleTextView.setText("test");
    }

Fragment的和adapter里也可以用，不过调用时要多加一个root view参数。    
Fragegment使用时记得同时继承onDestroyView，并在其中将ButterKnife.reset    

    public class FancyFragment extends Fragment {
    @BindView(R.id.button1) Button button1;
    @BindView(R.id.button2) Button button2;

    @Override View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fancy_fragment, container, false);
        //添加root view参数
        ButterKnife.inject(this, view);
        return view;
    }
    }

    @Override void onDestroyView() {
        super.onDestroyView();
        ButterKnife.reset(this);
    }

还能绑定String,Drawable,Color,Dimen资源

    @BindString(R.string.title) String title;
    @BindDrawable(R.drawable.graphic) Drawable graphic;
    @BindColor(R.color.red) int red; // int or ColorStateList field
    @BindDimen(R.dimen.spacer) Float spacer;

一次绑定多个资源的操作,使用@BindViews

    @BindViews({ R.id.first_name, R.id.middle_name, R.id.last_name })
    List<EditText> nameViews;

View绑定点击事件到方法

    @OnClick(R.id.submit)
    public void submit(View view) {
        // TODO submit data to server...
    }

---------

## Dagger2 笔记

Dagger是Android使用的依赖注入框架

控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（Dependency Injection，简称DI），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中.

通俗的来讲呢,就是一个类中需要依赖其他对象时,不需要你亲自为那些需要依赖的对象赋值,为那些对象赋值的操作交给了IOC框架.

### 简单使用

需要注入依赖的地方,@Inject标记在构造方法上,通过Dagger创建类实例的时候就通过调用这个构造方法

    public class Cloth {
        public String color;

        @Inject
        public Cloth() {
        }

        @Override
        public String toString() {
            return "布料的颜色是" + color;
        }
    }

也可以把@Inject标记在类成员上,如果只标记类成员不标记构造方法的话类将不会实例化

@Module,依赖的提供者,标记的类提供了被注入类的实例化方式,里面的方法用@Provides修饰,每一个provide方法用于实例化一个类

    @Module
    public class ClothModule {
        @Provides
        static Cloth provideCloth() {
            Cloth cloth = new Cloth();
            cloth.color = "red";
            return cloth;
        }
    }

@Component 依赖的注入者,是@Inject和@Module的桥梁,从@Module中获取依赖并注入@Inject

    @Component(modules = ClothModule.class)
    public interface ClothComponent {
        void inject(MainActivity mainActivity);
    }

需要注入的

    DaggerClothComponent.builder().clothModule(new ClothModule()).build().inject(this);

    ClothComponent clothComponent = DaggerClothComponent.create();
            clothComponent.inject(this);
