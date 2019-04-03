---
layout: post
title:  "Android 多线程/多进程相关"
date:   2017-08-09
categories: android
---

# 多线程

## 线程的状态

Java线程在运行的声明周期中可能会处于6种不同的状态

• New：新创建状态。线程被创建，还没有调用 start 方法，在线程运行之前还有一些基础工作要做。
• Runnable：可运行状态。一旦调用start方法，线程就处于Runnable状态。一个可运行的线程可能正在
运行也可能没有运行，这取决于操作系统给线程提供运行的时间。
• Blocked：阻塞状态。表示线程被锁阻塞，它暂时不活动。
• Waiting：等待状态。线程暂时不活动，并且不运行任何代码，这消耗最少的资源，直到线程调度器
重新激活它。
• Timed waiting：超时等待状态。和等待状态不同的是，它是可以在指定的时间自行返回的。
• Terminated：终止状态。表示当前线程已经执行完毕。导致线程终止有两种情况：第一种就是run方
法执行完毕正常退出；第二种就是因为一个没有捕获的异常而终止了run方法，导致线程进入终止状态。

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

AIDL(接口描述语言)是一种接口描述语言,常用于进程间通信      
就是定义一个接口,调用端调用bindService与被调用端建立一个连接,连接建立时返回一个IBinder对象,该对象时被调用端的BinderProxy,    
通过asInterface方法把Binder Proxy包装成本地Proxy并就能调用接口定义的方法了   
被调用端则是根据AIDL文件创建Binder对象,实现接口方法.再用Service的onBind方法绑定Binder    


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

# 线程池

代码示例

public class ThreadPollTask implements Runnable {
    // 保存任务所需要的数据
    private Object threadPoolTaskData;
    ThreadPollTask(Object tasks) {
        this.threadPoolTaskData = tasks;
    }
    @Override
    public void run() {
        // 处理一个任务，这里的处理方式太简单了，仅仅是一个打印语句
        System.out.println("start .." + threadPoolTaskData);
        try {
            //便于观察，等待一段时间
            Thread.sleep(2000);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public Object getTask() {
        return this.threadPoolTaskData;
    }
}

private static int produceTaskSleepTime = 2;
private static int produceTaskMaxNumber = 10;

        // 构造一个线程池
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(2, 4, 3,
                TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(3),
                new ThreadPoolExecutor.DiscardOldestPolicy());

        for (int i = 1; i <= produceTaskMaxNumber; i++) {
            try {
                // 产生一个任务，并将其加入到线程池
                String task = "task@ " + i;
                System.out.println("put " + task);
                threadPool.execute(new ThreadPollTask(task));
                // 便于观察，等待一段时间
                Thread.sleep(produceTaskSleepTime);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }


线程池类为 java.util.concurrent.ThreadPoolExecutor，常用构造方法为：

ThreadPoolExecutor(
    int corePoolSize, 
    int maximumPoolSize,
    long keepAliveTime, 
    TimeUnit unit,BlockingQueue<Runnable> workQueue,
    RejectedExecutionHandler handler)

corePoolSize：        线程池维护线程的最少数量 （core : 核心）
maximumPoolSize：线程池维护线程的最大数量
keepAliveTime：     线程池维护线程所允许的空闲时间
unit：           线程池维护线程所允许的空闲时间的单位
workQueue： 线程池所使用的缓冲队列
handler：      线程池对拒绝任务的处理策略

线程池对拒绝任务的处理策略
AbortPolicy
为java线程池默认的阻塞策略，不执行此任务，而且直接抛出一个运行时异常，切记ThreadPoolExecutor.execute需要try catch，否则程序会直接退出。
DiscardPolicy
直接抛弃，任务不执行，空方法
DiscardOldestPolicy
从队列里面抛弃head的一个任务，并再次execute 此task。
CallerRunsPolicy
在调用execute的线程里面执行此command，会阻塞入口
用户自定义拒绝策略（最常用）
实现RejectedExecutionHandler，并自己定义策略模式
下我们以ThreadPoolExecutor为例展示下线程池的工作流程图