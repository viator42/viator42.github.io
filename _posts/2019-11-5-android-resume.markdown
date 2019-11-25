---
layout: post
title:  "Android面试相关总结"
date:   2019-11-5
categories: android
---

## [Java基础](/java/2018/02/24/java-notes/)

## [Android基础](/android/2015/04/04/android-note/)

## [四大组件的原理](/android/2017/05/06/android-activity-service-broadcastservice-contentprovider/)

## [View绘制的过程，事件分发机制，自定义View](/android/2017/05/06/android-activity-service-broadcastservice-contentprovider/)

## [Android动画相关](/android/2016/06/29/android-animation/)

## [Android多线程，多进程，Handler Looper 机制](/android/2017/08/09/android-multi-thread/)

## [MVP和MVVM模式,与MVC模式有什么不同](/android/2018/03/05/android-mvp/)

## [Databinding和MVVM模式](/android/2018/12/25/android-mvvm-databinding/)

## [Gradle](/android/2019/01/01/gradle-for-android/)    

## [ProGuard](/android/2018/04/25/android-proguard/)

## [Rertofit RxJava相关](/java/android/2018/02/11/android-retrofit-rxjaja/)

# 进程间通信AIDL

AIDL (Android Interface Definition Language)是一种IDL 语言，用于生成可以在Android设备上两个进程之间进行进程间通信(IPC)的代码。
如果把Service定义到另一个进程中,使用Servicez自带的Binder就无法进行通信,必须使用AIDL.

使用方法
1.创建AIDL文件,定义接口名称

	interface IBookManagerInterface {
		List getBookList();
		void addBook(in int id, in String name);
	}

2.传输的Model类,必须继承Parcelable接口

new IBookManagerInterface.Stub()创建Binder然后重写接口文件的所有方法

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


3.Service被调用端

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

		private Binder mBinder = new IBookManagerInterface.Stub() {
			@Override
			public List getBookList() throws RemoteException {
				return bookList;
			}

			@Override
			public void addBook(int id, String name) throws RemoteException {
				bookList.add(new Book(id, name));
			}
		};

		@Override
		public IBinder onBind(Intent intent) {
			return mBinder;
		}
	}


4. Activity调用端,调用接口方法的时候必须try-catch包裹

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

	try {
		iBookManagerInterface.addBook(5, "New Book 005");
	} catch (RemoteException e) {
		e.printStackTrace();
	}

	try {
		List<Book> bookList = iBookManagerInterface.getBookList();
		for(Book book: bookList) {
			Log.v("aidltester", book.name);
		}

	} catch (RemoteException e) {
		e.printStackTrace();
	}

# 题目

    线程中 sleep() 和 wait() 有何区别，各有什么含义？
    abstract和 interface 的区别?
    array,arrayList, List 三者有何区别？
    hashtable和 hashmap 的区别,并简述 Hashmap 的实现原理。
    StringBuilder和 String，subString方法的细微差别。
    请写出四种以上你知道的设计模式，并介绍下实现原理。
    安卓子线程是否能更新UI，如果能请说明具体细节。
    JavaGC机制的原理和内存泄露。
    请在100个电话号码找出135的电话号码，注意不能用正则（类似怎么最好的遍历 LogCat日志）。（此类算法一般比较类似，记得京东笔试比较10个数字，拿出最大的数字，也就是冒泡排序。唯品会是让你写一算法，依次从10个数字中拿出3个，不够依此类推）
    Handler机制，请写出一种更新UI的方法和代码
    请解释安卓为啥要加签名机制。
    你觉得安卓开发最关键的技术在哪里？

    
    http协议的理解和用法。
    安卓解决线程并发问题。
    你知道的数据结构有哪些，说下具体实现机制。
    十六进制数据怎么和十进制和二进制之间转换？
    谈下对 Java OOP 中多态的理解。
    Activty和 Fragmengt 之间怎么通信，Fragmengt和 Fragmengt 怎么通信？
    怎么让自己的进程不被第三方应用杀掉，系统杀掉之后怎么能启动起来。
    说下平时开发中比较注意的一些问题。答 ：可以熟说下svn和git的细节，和代码规范问题，和一些安全信息的问题等。
    自定义view效率高于xml定义吗？说明理由。
    广播注册一般有几种，各有什么优缺点？
    服务启动一般有几种，服务和Activty之间怎么通信，服务和服务之间怎么通信A？
    布局优化主要哪些？具体优化？
    数据库的知识，包括本地数据库优化点。


    安卓事件分发机制，请详细说下整个流程。
    安卓 View绘制机制和加载 过程，请详细说下整个流程。
    Activty的加载过程，请详细介绍下。（不是生命周期切记）
    安卓采用自动垃圾回收机制，请说下安卓内存管理的原理。
    说下 安卓虚拟机 和 java虚拟机 的原理和不同点。
    多线程中的安全队列一般通过什么实现？线程池原理？（java）
    安卓权限管理，为何在清单中注册权限，安卓APP就可以使用，反之不可以。（操作系统）
    socket短线重连怎么实现，心跳机制又是怎样实现，四次握手步骤有哪些？（网络通讯原理）
    HTTP中 TCP和UDP 有啥区别，说下HTTP请求的 IP报文结构。（计算机网络）
    你知道的安全加密有哪些？ （如果你说了一个加密，面试官就会接着跟进提问，所以之前你必须要会，不会的话背也要背下来）（安全加密）
    你知道的数据存储结构？堆栈和链表内部机制。（数据结构）
    说下 Linux进程和线程 的区别。进程调度优先级，和cpu调度进程关系。（操作系统）
    请你详细说下你知道的一种设计模式，并解释下java的高内聚和低耦合。
    Spring的反射和代理，在安卓中应用场景。（插件和ROM数据框架）
    JNI调用过程中 混淆问题。
    看过安卓源码吗，请说出一个你看过的API或者组建内部原理。
    Android 5.0、 6.0 以及7.0预测新特性。
    hybrid混合开发，响应式编程等。
    为啥离职呢 对待加班看法？
    你擅长什么，做了哪些东西？

    说下项目中遇到的棘手问题，包括技术，交际和沟通。
    说下你进几年的规划。
    给你一个项目，你怎么看待他的市场和技术的关系？
    你一般喜欢从什么渠道获取技术信息，和提高自己的能力？
    你以往的项目中，以你现在的眼光去评价项目的利弊。
    对加班怎么看？（不要太浮夸，现实一点哦）
    说下 OPP 和 AOP 的思想。
    你知道的一些开源框架和原理。
    不同语言是否可以互相调用？
    安卓适配和性能调优问题。
    对于非立项（KPI）项目，怎么推进？
    你还要什么了解和要问的吗？

### 多线程多点下载，断点续传

android中断点续传的思路

一、 断点续传的实现步骤：

第一步： 我们要获得下载资源的的长度，用http请求中HttpURLConnection的getContentLength()方法
第二步：在本地创建一个文件，设计其长度。File file = new File()
第三步：从数据库中获得上次下载的进度，当暂停下载时，存储下载的状态，用到数据库的知识
第四步：从上次下载的位置下载数据，同时保存进度到数据库：RandomAccessFile的seek方法与HttpURLConnection的setRequestProperty方法
第五步：将下载进度回传到Activity，可以通过Intent将数据广播到Activity中
第六步：下载完成后删除下载信息，在数据库中删除相应的信息


### 布局优化

* 使用RelativeLayout减少视图树层级
* include布局和merge标签把重复的布局抽取出来
* ViewStub组件实现按需加载
* 减少过度绘制

### 绘制优化

* 自定义View中onDraw() 方法中不要做耗时的任务，不要创建新的局部对象。

### 内存优化

Activity的实例引用到static变量，会导致Activity无法正常销毁，导致内存泄漏    
属性动画需要在onDestory()方法中停止不然会造成Activity销毁之后动画依然在播放，导致内存泄漏    

### ANR对策

ANR全名Application Not Responding, 也就是"应用无响应".    
在如下两种情况下会弹出ANR对话框:    

* 5s内无法响应用户输入事件(例如键盘输入, 触摸屏幕等).
* BroadcastReceiver在10s内无法结束.

如何避免ANR

	不要在主线程(UI线程)里面做繁重的操作.

ANR的处理

针对三种不同的情况, 一般的处理情况如下

* 主线程阻塞的
开辟单独的子线程来处理耗时阻塞事务.

* CPU满负荷, I/O阻塞的
I/O阻塞一般来说就是文件读写或数据库操作执行在主线程了, 也可以通过开辟子线程的方式异步执行.

* 内存不够用的
增大VM内存, 使用largeHeap属性, 排查内存泄露(这个在内存优化那篇细说吧)等.

### ListView优化

使用ViewHolder，避免在getView中做耗时操作

1. convertView重用，利用好 convertView 来重用 View，切忌每次 getView() 都新建。ListView 的核心原理就是重用 View，如果重用 view 不改变宽高，重用View可以减少重新分配缓存造成的内存频繁分配/回收;
2. ViewHolder优化，使用ViewHolder的原因是findViewById方法耗时较大，如果控件个数过多，会严重影响性能，而使用ViewHolder主要是为了可以省去这个时间。通过setTag，getTag直接获取View。
3. 减少Item View的布局层级，这是所有layout都必须遵循的，布局层级过深会直接导致View的测量与绘制浪费大量的时间。
4. adapter中的getView方法尽量少使用逻辑
5. 图片加载采用三级缓存，避免每次都要重新加载。
6. 尝试开启硬件加速来使ListView的滑动更加流畅。
7. 使用 RecycleView 代替。

### 线程优化

尽量采用线程池，避免每次创建新的Thread

### 内存优化

* 避免创建更多的对象
* 尽量使用final static来修饰
* 使用Android特有的数据结构，比如SparseArray和Pair等
* 使用软引用和弱引用
* 采用内存缓存和磁盘缓存
* 尽量采用静态内部类
* 加载Bitmap
* 使用ProGuard进行代码压缩
* 对最终的apk使用zipalign
* 使用多进程

### binder机制

作用 用于在线程之间传递信息,子线程向主线程传递结果,进度信息.

* Message：封装的消息体.

     其中包含了消息ID，消息处理对象以及处理的数据等，由MessageQueue统一列队，终由Handler处理。
     参数:
          what 自定义消息id
          arg1, arg2 消息参数
          obj 消息内容,可以为任意类型
          
* Handler：处理者，负责Message的发送及处理。在接收消息的地方定义.重写handleMessage(Message msg)方法来处理消息.

* MessageQueue：消息队列，用来存放Handler发送过来的消息，并按照FIFO规则执行。当然，存放Message并非实际意义的保存，而是将Message以链表的方式串联起来的，等待Looper的抽取。内部封装的类,使用时不可见.
* Looper：消息泵，不断地从MessageQueue中抽取Message执行。因此，一个MessageQueue需要一个Looper。UI线程自带Looper不需要定义.
子线程在定义Hander的时候需要先初始化Looper,初始化Hander的代码写在Looper.prepare();和Looper.loop();之间.

主线程和子线程通过Handler交互的实例

	public class MainActivity extends AppCompatActivity {
		private Button startThreadBtn;
		private Button pushMsgToSubBtn;
		private TextView msgTextView;
		private Handler toSubThreadHandler;
		private Handler toMainThreadHandler;
		private final static int MSG_TO_MAIN = 1;
		private final static int MSG_TO_SUB = 2;

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			msgTextView = (TextView) findViewById(R.id.msg);
			startThreadBtn = (Button) findViewById(R.id.start_thread);
			startThreadBtn.setOnClickListener(new View.OnClickListener() {
				@Override
				public void onClick(View v) {
					SubThread subThread = new SubThread();
					subThread.start();

					pushMsgToSubBtn.setEnabled(true);
				}
			});

			pushMsgToSubBtn = (Button) findViewById(R.id.push_msg_to_sub);
			pushMsgToSubBtn.setOnClickListener(new View.OnClickListener() {
				@Override
				public void onClick(View v) {
					Message message1 = Message.obtain();
					String msg1 = "This is an message to sub thread";
					message1.obj = msg1;
					message1.what = MSG_TO_SUB;
					toMainThreadHandler.sendMessage(message1);
				}
			});
			pushMsgToSubBtn.setEnabled(false);

			toSubThreadHandler = new Handler() {
				@Override
				public void handleMessage(Message msg) {
					super.handleMessage(msg);

					switch (msg.what)
					{
						case MSG_TO_MAIN:
							msgTextView.setText((String) msg.obj);
							break;
					}
				}
			};
		}

		private class SubThread extends Thread
		{
			@Override
			public void run() {
				super.run();

				Message toMainThreadMsg = new Message();
				toMainThreadMsg.what = 1;
				String msg = "A Messgae to Main Thread";
				toMainThreadMsg.obj = msg;

				toSubThreadHandler.sendMessage(toMainThreadMsg);

				Looper.prepare();
				toMainThreadHandler = new Handler(){
					@Override
					public void handleMessage(Message msg) {
						super.handleMessage(msg);
						switch (msg.what)
						{
							case MSG_TO_SUB:
								Toast.makeText(MainActivity.this, (String) msg.obj, Toast.LENGTH_SHORT).show();
								break;
						}
					}
				};
				Looper.loop();
			}
		}
	}

### handler原理， Message,loop,messageQueue关系，handler内存泄露问题。

Handler主要用途是在子线程中访问UI使用，子线程中进行网络访问，文件操作等耗时操作需要反映到UI的时候使用handler与主线程进行交互。        
Handler原理是借助Loop消息队列来满足线程间的通信，Handler在创建的时候会使用当前线程的Looper来构建消息系统，主线程中自带ActivityLooper不需要自己创建，Handler创建在子线程中的时候就需要手动创建Looper；Looper初始化的时候会创建一个消息队列MessageQueue；MessageQueue是一个队列，遵循先进先出的原则，作用是存放所有handler发送过来的消息，这些消息会一直存放消息队列中，等待被处理，每一个线程只有一直MessageQueue队列。    
Looper：每个线程通过Handler发送的消息都保存在，MessageQueue中，Looper通过调用loop（）的方法，就会进入到一个无限循环当中，然后每当发现MessageQueue中存在一条消息，就会将它取出，并传递到Handler的handleMessage（）方法中。每个线程中只会有一个Looper对象。    
信息传递的单位是Message，message就是一个数据模型，它的作用用于线程之间传递信息，常用的四个字段target，what，obj，arg;    

handler:它主要用于发送和接收消息，有三个主要方法

* sendMessage（）；    
* dispatchMessage（）；    
* handleMessage（）；    

发送方通过sendMessage把消息压入消息队列，Looper发现有新消息到来时调用handleMessage处理消息。

ThreadLocal是一个线程内部的数据存储类，通过他可以在指定的线程中存取数据，每个线程都有自己的ThreadLocal可以保证数据隔离。线程的Looper就是存储在ThreadLocal中，这样每个线程的Handler都可以独立运行不会相互干扰。如果不用ThreadLocal系统就必须定义一个全局表供Handler查找指定的Looper

### service生命周期，可以执行耗时操作吗？

不可以。    
Service和activity是运行在当前app所在的main thread（UI主线程）中的，而耗时操作（如：网络请求、拷贝数据、大文件）会阻塞主线程，给用户不好的体验。    

如果需要在服务中进行耗时操作，可以选择IntentService，  IntentService是Service的子类，用来处理异步请求。     
IntentService在onCreate()方法中通过HandlerThread单独开启一个线程来处理Intent请求对象所对应的任务，这样可以避免事务处理阻塞主线程。    
onHandleIntent()函数针对Intent的不同进行不同的事务处理就可以，执行完一个Intent请求对象所对应的工作之后，如果没有新的Intent请求达到，则自动停止Service； 否则ServiceHandler会取得下一个Intent请求    
传入该函数来处理其所对应的任务。    

### 自己设计一个图片加载框架

### http ResponseCode

* 200 - 请求成功
* 301 - 资源（网页等）被永久转移到其它URL
* 404 - 请求的资源（网页等）不存在
* 500 - 内部服务器错误

### 插件化，动态加载

### http协议了解多少，说说里面的协议头部有哪些字段？

### https了解多少？为什么百度全部都用了https包括首页

### ArrayList和Vector的主要区别是什么？

 ArrayList在Java1.2引入，用于替换Vector

Vector:    
    线程同步    
    当Vector中的元素超过它的初始大小时，Vector会将它的容量翻倍    

ArrayList:
    线程不同步，但性能很好    
    当ArrayList中的元素超过它的初始大小时，ArrayList只增加50%的大小    

### Java中try catch finally的执行顺序

先执行try中代码发生异常执行catch中代码，最后一定会执行finally中代码

### 实现SIngleton模式

最佳实践是使用静态内部类来创建，静态内部类中使用静态初始化块保证只创建一个类实例，不需要使用if判断而且是线程安全的。当第一次引用getInstance()方法的时候，访问静态内部类中的静态成员变量，此时该内部类需要调用static代码块(因为首次访问该类)。而后再次访问getInstance()方法会直接返回instace引用。

	public class SingleInstanceClass {
		private SingleInstanceClass() {}

		private static class SingleInstanceHolder {
			private static SingleInstanceClass singleInstanceClass;
			static {
				singleInstanceClass = new SingleInstanceClass();
			}
		}

		public static SingleInstanceClass getInstance() {
			return SingleInstanceHolder.singleInstanceClass;
		}

		public void doFunc() {
			System.out.println("SingleInstanceClass created!");
		}

	}

### 二叉树

前序遍历：先访问根节点，再访问左节点，最后右节点    
中旭遍历：先访问左节点，再访问根节点，最后右节点    
后序遍历：先访问左节点，再访问右节点，最后根节点    

### 重建二叉树
根据前序遍历和中序遍历的结果构建出二叉树

### java中==和equals和hashCode的区别

__==__    
基本数据类型的==比较的值相等    
引用类型(类、接口、数组)比较的是内存中的地址，同一个对象才会比较相等

__hashCode__    
hashCode也是Object类的一个方法。返回一个离散的int型整数。在集合类操作中使用，为了提高查询速度。（HashMap，HashSet等比较是否为同一个）    

__equals__
String,Integer,Date在这些类当中equals有其自身的实现，比较的是值是否相等    
对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（==）进行比较的，所以比较后的结果跟双等号（==）的结果相同。    

如果两个对象根据equals()方法比较是相等的，那么调用这两个对象中任意一个对象的hashCode方法都必须产生同样的整数结果。
如果两个对象根据equals()方法比较是不相等的，那么调用这两个对象中任意一个对象的hashCode方法，则不一定要产生相同的整数结果

### int与integer的区别    
int 基本类型    
integer 对象 int的封装类    

### String、StringBuffer、StringBuilder区别    
String:字符串常量 不适用于经常要改变值得情况，每次改变相当于生成一个新的对象    
和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。 
StringBuffer:字符串变量 （线程安全）    
StringBuilder:字符串变量（线程不安全） 确保单线程下可用，效率略高于StringBuffer    

### 什么是内部类？内部类的作用    
内部类可直接访问外部类的属性    
Java中内部类主要分为成员内部类、局部内部类(嵌套在方法和作用域内)、匿名内部类（没构造方法）、静态内部类（static修饰的类，不能使用任何外围类的非static成员变量和方法， 不依赖外围类）    

### 进程和线程的区别
进程是cpu资源分配的最小单位，线程是cpu调度的最小单位。    
进程之间不能共享资源，而线程共享所在进程的地址空间和其它资源。    
一个进程内可拥有多个线程，进程可开启进程，也可开启线程。    
一个线程只能属于一个进程，线程可直接使用同进程的资源,线程依赖于进程而存在。    

### final，finally，finalize的区别    
final:修饰类、成员变量和成员方法，类不可被继承，成员变量不可变，成员方法不可重写    
finally:与try...catch...共同使用，确保无论是否出现异常都能被调用到    
finalize:类的方法,垃圾回收之前会调用此方法,子类可以重写finalize()方法实现对资源的回收    

### Serializable 和Parcelable 的区别      
Serializable Java 序列化接口 在硬盘上读写 读写过程中有大量临时变量的生成，内部执行大量的i/o操作，效率很低。    
Parcelable Android 序列化接口 效率高 使用麻烦 在内存中读写（AS有相关插件 一键生成所需方法） ，对象不能保存到磁盘中    

### 静态属性和静态方法是否可以被继承？是否可以被重写？以及原因？   
可继承 不可重写 而是被隐藏   
如果子类里面定义了静态方法和属性，那么这时候父类的静态方法或属性称之为"隐藏"。如果你想要调用父类的静态方法和属性，直接通过父类名.方法或变量名完成。   

### 成员内部类、静态内部类、局部内部类和匿名内部类的理解，以及项目中的应用   
Java中内部类主要分为成员内部类、局部内部类(嵌套在方法和作用域内)、匿名内部类（没构造方法）、静态内部类（static修饰的类，不能使用任何外围类的非static成员变量和方法， 不依赖外围类）    
使用内部类最吸引人的原因是：每个内部类都能独立地继承一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响。    
因为Java不支持多继承，支持实现多个接口。但有时候会存在一些使用接口很难解决的问题，这个时候我们可以利用内部类提供的、可以继承多个具体的或者抽象的类的能力来解决这些程序设计问题。可以这样说，接口只是解决了部分问题，而内部类使得多重继承的解决方案变得更加完整。    

### 哪些情况下的对象会被垃圾回收机制处理掉？    
1.所有实例都没有活动线程访问。    
2.没有被其他任何实例访问的循环引用实例。    
3.Java 中有不同的引用类型。判断实例是否符合垃圾收集的条件都依赖于它的引用类型。    
要判断怎样的对象是没用的对象。这里有2种方法：   
1.采用标记计数的方法：    
给内存中的对象给打上标记，对象被引用一次，计数就加1，引用被释放了，计数就减一，当这个计数为0的时候，这个对象就可以被回收了。当然，这也就引发了一个问题：循环引用的对象是无法被识别出来并且被回收的。所以就有了第二种方法：    
2.采用根搜索算法：    
从一个根出发，搜索所有的可达对象，这样剩下的那些对象就是需要被回收的    

### Java反射的理解

JAVA反射机制是在运行状态中, 对于任意一个类, 都能够知道这个类的所有属性和方法; 对于任意一个对象, 都能够调用它的任意一个方法和属性。 从对象出发，通过反射（Class类）可以取得取得类的完整信息（类名 Class类型，所在包、具有的所有方法 Method[]类型、某个方法的完整信息（包括修饰符、返回值类型、异常、参数类型）、所有属性 Field[]、某个属性的完整信息、构造器 Constructors），调用类的属性或方法自己的总结： 在运行过程中获得类、对象、方法的所有信息。

### Java注解的理解

### String为什么要设计成不可变的？

1、字符串池的需求
字符串池是方法区（Method Area）中的一块特殊的存储区域。当一个字符串已经被创建并且该字符串在 池 中，该字符串的引用会立即返回给变量，而不是重新创建一个字符串再将引用返回给变量。如果字符串不是不可变的，那么改变一个引用（如: string2）的字符串将会导致另一个引用（如: string1）出现脏数据。
2、允许字符串缓存哈希码
在java中常常会用到字符串的哈希码，例如： HashMap 。String的不变性保证哈希码始终一，因此，他可以不用担心变化的出现。 这种方法意味着不必每次使用时都重新计算一次哈希码——这样，效率会高很多。
3、安全
String广泛的用于java 类中的参数，如：网络连接（Network connetion），打开文件（opening files ）等等。如果String不是不可变的，网络连接、文件将会被改变——这将会导致一系列的安全威胁。操作的方法本以为连接上了一台机器，但实际上却不是。由于反射中的参数都是字符串，同样，也会引起一系列的安全问题。

### List,Set,Map的区别

Set是最简单的一种集合。集合中的对象不按特定的方式排序，并且没有重复对象。 Set接口主要实现了两个实现类：HashSet： HashSet类按照哈希算法来存取集合中的对象，存取速度比较快

TreeSet ：TreeSet类实现了SortedSet接口，能够对集合中的对象进行排序。

List的特征是其元素以线性方式存储，集合中可以存放重复对象。
ArrayList() : 代表长度可以改变得数组。可以对元素进行随机的访问，向ArrayList()中插入与删除元素的速度慢。
LinkedList(): 在实现中采用链表数据结构。插入和删除速度快，访问速度慢。
Map 是一种把键对象和值对象映射的集合，它的每一个元素都包含一对键对象和值对象。 Map没有继承于Collection接口 从Map集合中检索元素时，只要给出键对象，就会返回对应的值对象。
HashMap：Map基于散列表的实现。插入和查询“键值对”的开销是固定的。可以通过构造器设置容量capacity和负载因子load factor，以调整容器的性能。
LinkedHashMap： 类似于HashMap，但是迭代遍历它时，取得“键值对”的顺序是其插入次序，或者是最近最少使用(LRU)的次序。只比HashMap慢一点。而在迭代访问时发而更快，因为它使用链表维护内部次序。
TreeMap ： 基于红黑树数据结构的实现。查看“键”或“键值对”时，它们会被排序(次序由Comparabel或Comparator决定)。TreeMap的特点在 于，你得到的结果是经过排序的。TreeMap是唯一的带有subMap()方法的Map，它可以返回一个子树。
WeakHashMao ：弱键(weak key)Map，Map中使用的对象也被允许释放: 这是为解决特殊问题设计的。如果没有map之外的引用指向某个“键”，则此“键”可以被垃圾收集器回收。

### 数组和链表的区别

数组：是将元素在内存中连续存储的；它的优点：因为数据是连续存储的，内存地址连续，所以在查找数据的时候效率比较高；它的缺点：在存储之前，我们需要申请一块连续的内存空间，并且在编译的时候就必须确定好它的空间的大小。在运行的时候空间的大小是无法随着你的需要进行增加和减少而改变的，当数据两比较大的时候，有可能会出现越界的情况，数据比较小的时候，又有可能会浪费掉内存空间。在改变数据个数时，增加、插入、删除数据效率比较低。    

链表：是动态申请内存空间，不需要像数组需要提前申请好内存的大小，链表只需在用的时候申请就可以，根据需要来动态申请或者删除内存空间，对于数据增加和删除以及插入比数组灵活。还有就是链表中数据在内存中可以在任意的位置，通过应用来关联数据（就是通过存在元素的指针来联系）    

### 开启线程的三种方式？

Java有三种创建线程的方式，分别是继承Thread类、实现Runable接口和使用线程池

### 线程和进程的区别？

线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。别把它和栈内存搞混，每个线程都拥有单独的栈内存用来存储本地数据。

线程是指进程内的一个执行单元,也是进程内的可调度实体。与进程的区别:    
(1)调度：线程作为调度和分配的基本单位，进程作为拥有资源的基本单位。    
(2)并发性：不仅进程之间可以并发执行，同一个进程的多个线程之间也可并发执行。    
(3)拥有资源：进程是拥有资源的一个独立单位，线程不拥有系统资源，但可以访问隶属于进程的资源.    
(4)系统开销：在创建或撤消进程时，由于系统都要为之分配和回收资源，导致系统的开销明显大于创建或撤消线程时的开销。    

### run()和start()方法区别

start()方法被用来启动新创建的线程，而且start()内部调用了run()方法，这和直接调用run()方法的效果不一样。当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动，start()方法才会启动新线程。

### 在Java中wait和seelp方法的不同

Java程序中wait 和 sleep都会造成某种形式的暂停，它们可以满足不同的需要。wait()方法用于线程间通信，如果等待条件为真且其它线程被唤醒时它会释放锁，而sleep()方法仅仅释放CPU资源或者让当前线程停止执行一段时间，但不会释放锁。

### 谈谈wait/notify关键字的理解

等待对象的同步锁,需要获得该对象的同步锁才可以调用这个方法,否则编译可以通过，但运行时会收到一个异常：IllegalMonitorStateException。
调用任意对象的 wait() 方法导致该线程阻塞，该线程不可继续执行，并且该对象上的锁被释放。    
唤醒在等待该对象同步锁的线程(只唤醒一个,如果有多个在等待),注意的是在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且不是按优先级。    
调用任意对象的notify()方法则导致因调用该对象的 wait()方法而阻塞的线程中随机选择的一个解除阻塞（但要等到获得锁后才真正可执行）。    

### 如何实现线程同步？

1. synchronized关键字修改的方法。
2. synchronized关键字修饰的语句块3、
3. 使用特殊域变量（volatile）实现线程同步

### 死锁的四个必要条件？

死锁产生的原因
1. 系统资源的竞争
系统资源的竞争导致系统资源不足，以及资源分配不当，导致死锁。
2. 进程运行推进顺序不合适
互斥条件：一个资源每次只能被一个进程使用，即在一段时间内某 资源仅为一个进程所占有。此时若有其他进程请求该资源，则请求进程只能等待。
请求与保持条件：进程已经保持了至少一个资源，但又提出了新的资源请求，而该资源 已被其他进程占有，此时请求进程被阻塞，但对自己已获得的资源保持不放。
不可剥夺条件:进程所获得的资源在未使用完毕之前，不能被其他进程强行夺走，即只能 由获得该资源的进程自己来释放（只能是主动释放)。
循环等待条件: 若干进程间形成首尾相接循环等待资源的关系
这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之一不满足，就不会发生死锁。
死锁的避免与预防：
死锁避免的基本思想:
系统对进程发出每一个系统能够满足的资源申请进行动态检查,并根据检查结果决定是否分配资源,如果分配后系统可能发生死锁,则不予分配,否则予以分配。这是一种保证系统不进入死锁状态的动态策略。
理解了死锁的原因，尤其是产生死锁的四个必要条件，就可以最大可能地避免、预防和解除死锁。所以，在系统设计、进程调度等方面注意如何让这四个必要条件不成立，如何确定资源的合理分配算法，避免进程永久占据系统资源。此外，也要防止进程在处于等待状态的情况下占用资源。因此，对资源的分配要给予合理的规划。
死锁避免和死锁预防的区别：
死锁预防是设法至少破坏产生死锁的四个必要条件之一,严格的防止死锁的出现,而死锁避免则不那么严格的限制产生死锁的必要条件的存在,因为即使死锁的必要条件存在,也不一定发生死锁。死锁避免是在系统运行过程中注意避免死锁的最终发生。

### 线程间操作List

	List list = Collections.synchronizedList(new ArrayList());

### synchronized 和volatile 关键字的区别

1. volatile本质是在告诉jvm当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取；synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
2. volatile仅能使用在变量级别；synchronized则可以使用在变量、方法、和类级别的
3. volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性
4. volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。
5. volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化

### ReentrantLock 、synchronized和volatile比较

Java在过去很长一段时间只能通过synchronized关键字来实现互斥，它有一些缺点。比如你不能扩展锁之外的方法或者块边界，尝试获取锁时不能中途取消等。Java 5 通过Lock接口提供了更复杂的控制来解决这些问题。 ReentrantLock 类实现了 Lock，它拥有与 synchronized 相同的并发性和内存语义且它还具有可扩展性。

### 进程间通讯的方式有哪些，各有什么优缺点：

1）管道：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程之间使用。进程的亲缘关系通常是指父子进程关系。    
2）有名管道（FIFO）：有名管道也是半双工的通信方式，但是允许在没有亲缘关系的进程之间使用，管道是先进先出的通信方式。    
3）信号量：信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。    
4）消息队列：消息队列是有消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。    
5）信号 ( sinal ) ：信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。    
6）共享内存( shared memory ) ：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。    
7）套接字( socket ) ：套接字也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。    

### Java 集合类框架的基本接口有哪些？

Java 集合框架主要包括两种类型的容器，一种是集合（Collection），存储一个元素集合，另一种是图（Map），存储键/值对映射
Collection 接口又有 3 种子类型，List、Set 和 Queue，再下面是一些抽象类，最后是具体实现类，常用的有 ArrayList、LinkedList、HashSet、LinkedHashSet、HashMap、LinkedHashMap 等等。

Collection 是最基本的集合接口，一个 Collection 代表一组 Object，即 Collection 的元素, Java不提供直接继承自Collection的类，只提供继承于的子接口(如List和set)

Set：不包含重复元素的 Collection，没有顺序。    
SortedSet 元素唯一不重复，有序，继承自Set    
List：有顺序的 collection，并且可以包含重复元素。    
Map：可以把键(key)映射到值(value)的对象，键不能重复。    
Map.Entry     描述在一个Map中的一个元素（键/值对）。是一个Map的内部类。    

Set和List的区别    

1. Set 接口实例存储的是无序的，不重复的数据。List 接口实例存储的是有序的，可以重复的元素。
2. Set检索效率低下，删除和插入效率高，插入和删除不会引起元素位置改变 <实现类有HashSet,TreeSet>。
3. List和数组类似，可以动态增长，根据实际存储的数据的长度自动增长List的长度。查找元素效率高，插入删除效率低，因为会引起其他元素位置改变 <实现类有ArrayList,LinkedList,Vector> 。

ArrayList

该类也是实现了List的接口，实现了可变大小的数组，随机访问和遍历元素时，提供更好的性能。该类也是非同步的,在多线程的情况下不要使用。ArrayList 增长当前长度的50%，插入删除效率低。

LinkedList

该类实现了List接口，允许有null（空）元素。主要用于创建链表数据结构，该类没有同步方法，如果多个线程同时访问一个List，则必须自己实现访问同步，解决方法就是在创建List时候构造一个同步的List。例如：
Listlist=Collections.synchronizedList(newLinkedList(...));
LinkedList 查找效率低。

Vector
该类和ArrayList非常相似，但是该类是同步的，可以用在多线程的情况，该类允许设置默认的增长长度，默认扩容方式为原来的2倍。

List是线程不安全的，在多线程环境中需要加锁保持同步

### java中的soft reference是个什么东西

即对象的软引用。如果一个对象具有软引用，内存空间足够，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。使用软引用能防止内存泄露，增强程序的健壮性。 

### 一个应用程序有几个Context

在应用程序中Context的具体实现子类就是: Activity, Service, Application. 那么Context数量 = Activity数量 + Service数量 + 1
Broadcast Receiver, Content Provider 并不是 Context 的子类, 它们所持有的 Context 都是其他地方传过去的, 所以并不计入Context总数.

### Android中Activity, Intent, Content Provider, Service各有什么区别。

Activity： 活动，是最基本的android应用程序组件。一个活动就是一个用户可以操作的可视化用户界面，每一个活动都被实现为一个独立的类，并且从活动基类继承而来。    
Intent： 意图，描述应用想干什么。最重要的部分是动作和动作对应的数据。    
Content Provider：内容提供器，android应用程序能够将它们的数据保存到文件、SQLite数据库中，甚至是任何有效的设备中。当你想将你的应用数据和其他应用共享时，内容提供器就可以发挥作用了。    
Service：服务，具有一段较长生命周期且没有用户界面的程序组件。    

### 什么是Activity

四大组件之一,一般的,一个用户交互界面对应一个activity    
setContentView() ,// 要显示的布局     
activity 是Context的子类,同时实现了window.callback和keyevent.callback, 可以处理与窗体用户交互的事件.     
我开发常用的的有FragmentActivitiyListActivity  ,PreferenceActivity ,TabAcitivty等    

### 请描述一下Activity 生命周期

Activity从创建到销毁有多种状态，从一种状态到另一种状态时会激发相应的回调方法，这些回调方法包括：onCreate onStart onResume onPause onStop onDestroy    
其实这些方法都是两两对应的，onCreate创建与onDestroy销毁；    
onStart可见与onStop不可见；onResume可编辑（即焦点）与onPause；    
如果界面有共同的特点或者功能的时候,还会自己定义一个BaseActivity.     

### Service的生命周期

### 如何保存Activity的状态或者(Activiy 重启怎么保存数据？

Activity的状态通常情况下系统会自动保存的，只有当我们需要保存额外的数据时才需要使用到这样的功能。
一般来说, 调用onPause()和onStop()方法后的activity实例仍然存在于内存中, activity的所有信息和状态数据不会消失, 当activity重新回到前台之后, 所有的改变都会得到保留。
但是当系统内存不足时, 调用onPause()和onStop()方法后的activity可能会被系统摧毁, 此时内存中就不会存有该activity的实例对象了。如果之后这个activity重新回到前台, 之前所作的改变就会消失。为了避免此种情况的发生, 我们可以覆写onSaveInstanceState()方法。onSaveInstanceState()方法接受一个Bundle类型的参数, 开发者可以将状态数据存储到这个Bundle对象中, 这样即使activity被系统摧毁, 当用户重新启动这个activity而调用它的onCreate()方法时, 上述的Bundle对象会作为实参传递给onCreate()方法, 开发者可以从Bundle对象中取出保存的数据, 然后利用这些数据将activity恢复到被摧毁之前的状态。
需要注意的是, onSaveInstanceState()方法并不是一定会被调用的, 因为有些场景是不需要保存状态数据的. 比如用户按下BACK键退出activity时, 用户显然想要关闭这个activity, 此时是没有必要保存数据以供下次恢复的, 也就是onSaveInstanceState()方法不会被调用. 如果调用onSaveInstanceState()方法, 调用将发生在onPause()或onStop()方法之前。

### 如何退出Activity？如何安全退出已调用多个Activity的Application？

1. Application中定义Activity列表,应用关闭的时候遍历这个列表关闭所有的Activity
2. 在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。
3. 在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。

### Activity的启动模式

singleTop 跟standard 模式比较类似。唯一的区别就是，当跳转的对象是位于栈顶的activity（应该可以理解为用户眼前所 看到的activity）时，程序将不会生成一个新的activity实例，而是直接跳到现存于栈顶的那个activity实例。拿上面的例子来说，当Act1 为 singleTop 模式时，执行跳转后栈里面依旧只有一个实例，如果现在按返回键程序将直接退出。
	singleTask模式和singleInstance模式都是只创建一个实例的。在这种模式下，无论跳转的对象是不是位于栈顶的activity，程序都不会生成一个新的实例（当然前提是栈里面已经有这个实例）。这种模式相当有用，在以后的多activity开发中，常会因为跳转的关系导致同个页面生成多个实例，这个在用户体验上始终有点不好，而如果你将对应的activity声明为singleTask 模式，这种问题将不复存在。在主页的Activity很常用

### 性能调优的经验

UI的样式减少嵌套,多使用RelativeLayout和ConstraintLayout来布局,减少屏幕的控件数量    
ListView的每一个单元格中这样调优效果明显,减少滑动的卡顿感    

图片Bitmap尽量压缩尺寸,原图尽量减少尺寸,设置inSampleSize(缩放比例)和decode format(解码格式)    
选择ARGB_8888/RBG_565/ARGB_4444/ALPHA_8，存在很大差异。    

Activity退出的时候释放Bitmap资源    

ListView列表中使用RecyclerView来提高效率    

UI线程中不允许网络访问,Android官方已在4.0以后禁止了    
加载图片,访问文件这种耗时的操作也应该放到单独的线程中操作    
ASyncTask就是多线程操作的辅助类    

### Android 屏幕适配

屏幕适配的方式：xxxdpi， wrap_content,match_parent. 获取屏幕大小，做处理。    
dp来适配屏幕，sp来确定字体大小    
drawable-xxdpi, values-1280*1920等 这些就是资源的适配。    
wrap_content,match_parent, 这些是view的自适应    
weight，这是权重的适配。    

在res目录下创建不同的layout文件夹    
9.png 使用点9图保证图片拉伸的时候不失真    

### 对Handler ，looper，MessageQueue 的理解

andriod提供了 Handler 和 Looper 来满足线程间的通信。Handler 先进先出原则。Looper类用来管理特定线程内对象之间的消息交换(Message Exchange)。
1）Looper: 一个线程可以产生一个Looper对象，由它来管理此线程里的Message Queue(消息队列)。
2）Handler: 你可以构造Handler对象来与Looper沟通，以便push新消息到Message Queue里；或者接收Looper从Message Queue取出)所送来的消息。
3） Message Queue(消息队列):用来存放线程放入的消息。
4）线程：UI thread 通常就是main thread，而Android启动程序时会替它建立一个Message Queue。

### Activity InstanceState详解
Activity的 onSaveInstanceState() 和 onRestoreInstanceState()并不是生命周期方法，它们不同于 onCreate()、onPause()等生命周期方法，它们并不一定会被触发。当应用遇到意外情况（如：内存不足、用户直接按Home键）由系统销毁一个Activity时，onSaveInstanceState() 会被调用。但是当用户主动去销毁一个Activity时，例如在应用中按返回键，onSaveInstanceState()就不会被调用。因为在这种情况下，用户的行为决定了不需要保存Activity的状态。通常onSaveInstanceState()只适合用于保存一些临时性的状态，而onPause()适合用于数据的持久化保存。

在activity被杀掉之前调用保存每个实例的状态,以保证该状态可以在onCreate(Bundle)或者onRestoreInstanceState(Bundle) (传入的Bundle参数是由onSaveInstanceState封装好的)中恢复。这个方法在一个activity被杀死前调用，当该activity在将来某个时刻回来时可以恢复其先前状态。

例如，如果activity B启用后位于activity A的前端，在某个时刻activity A因为系统回收资源的问题要被杀掉，A通过onSaveInstanceState将有机会保存其用户界面状态，使得将来用户返回到activity A时能通过onCreate(Bundle)或者onRestoreInstanceState(Bundle)恢复界面的状态。


