---
layout: post
title:  "Android 多线程/多进程相关"
date:   2017-08-09
categories: android
---

# 多线程

线程和进程有什么区别    
一个进程是一个独立(self contained)的运行环境，它可以被看作一个程序或者一个应用。而线程是在进程中执行的一个任务。线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。

类在多线程中的执行过程

多个线程共享一个类对象,这个对象是被创建在主内存(堆内存)中，每个线程都有自己的工作内存(线程栈).工作内存存储了主内存对象的一个副本，当线程操作C对象时，首先从主内存复制对象到工作内存中，然后执行代码最后用工作内存对象刷新主内存对象。当一个对象在多个内存中都存在副本时，如果一个内存修改了共享变量，其它线程也应该能够看到被修改后的值，此为可见性。保证多个线程按照顺序进行，此为有序性

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

## 线程中断

如果run方法执行完毕，线程就会终止     
中途终止线程使用interrupt方法。这个方法是请求线程终止，线程中捕获到终止请求可以进行一些处理之后再终止线程    

    Thread thread = new Thread();
    thread.start();
    ...
    thread.interrupt(); //请求终止线程

    //线程中的操作
    public TestThread extends Thread {
        public void run() {
            //判断线程是否要终止
            while(!Thread.currentThread.isInterrupted()) {
                //进行终止线程的操作
            }
        }
    }

## 线程同步

synchronized的作用是给代码块加上互斥锁,每个时间点只能有一个线程可以访问.避免线程乱序执行导致的数据的不一致.保证数据一致性

使用synchronized包裹需要同步的代码块

        synchronized (this) {  
                dosomething;
        } 

将synchronized加在需要互斥的方法上

        public synchronized void func() {
        }

volatile变量

用volatile修饰的变量，线程在每次使用变量的时候，都会读取变量修改后的最的值。     
synchronized关键字可防止多个线程同时执行一段代码，那么这就会很影响程序执行效率。而volatile关键字在某些情况下的性能要优于synchronized。但是要注意volatile关键字是无法替代synchronized关键字的，因为volatile关键字无法保证操作的原子性。通常来说，使用volatile必须具备以下两个条件：   

1. 对变量的写操作不会依赖于当前值。
2. 该变量没有包含在具有其他变量的不变式中。

## 阻塞队列

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

__常见阻塞场景__

阻塞队列有两个常见的阻塞场景，它们分别是：

1. 当队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列。
2. 当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。

支持以上两种阻塞场景的队列被称为阻塞队列。

__BlockingQueue的核心方法__

放入数据：

* offer（anObject）：表示如果可能的话，将anObject加到BlockingQueue里。即如果BlockingQueue可以容纳，则返回true，否则返回false。（本方法不阻塞当前执行方法的线程。）
* offer（E o，long timeout，TimeUnit unit）：可以设定等待的时间。如果在指定的时间内还不能往队列中加入BlockingQueue，则返回失败。
* put（anObject）：将anObject加到BlockingQueue里。如果BlockQueue没有空间，则调用此方法的线程被阻断，直到BlockingQueue里面有空间再继续。

获取数据：

* poll（time）：取走 BlockingQueue 里排在首位的对象。若不能立即取出，则可以等 time参数规定的时间。取不到时返回null。
* poll（long timeout，TimeUnit unit）：从BlockingQueue中取出一个队首的对象。如果在指定时间内，队列一旦有数据可取，则立即返回队列中的数据；否则直到时间超时还没有数据可取，返回失败。
* take（）：取走BlockingQueue里排在首位的对象。若BlockingQueue为空，则阻断进入等待状态，直到BlockingQueue有新的数据被加入。
* drainTo（）：一次性从BlockingQueue获取所有可用的数据对象（还可以指定获取数据的个数）。通过该方法，可以提升获取数据的效率；无须多次分批加锁或释放锁。

__阻塞队列分类__

* ArrayBlockingQueue ：     由数组结构组成的有界阻塞队列。
* LinkedBlockingQueue ：    由链表结构组成的有界阻塞队列。
* PriorityBlockingQueue ：  支持优先级排序的无界阻塞队列。
* DelayQueue：              使用优先级队列实现的无界阻塞队列。
* SynchronousQueue：        不存储元素的阻塞队列。
* LinkedTransferQueue：     由链表结构组成的无界阻塞队列。
* LinkedBlockingDeque：     由链表结构组成的双向阻塞队列。

--------

## 线程池

__线程池的优点__

1. 重用线程池中的线程,避免线程创建销毁带来的性能开销
2. 控制最大并发数,避免大量线程相互抢夺资源造成阻塞
3. 对线程进行管理,并提供定时执行和指定时间执行的功能

线程池类为 java.util.concurrent.ThreadPoolExecutor，常用构造方法为：

        ThreadPoolExecutor(
                int corePoolSize, 
                int maximumPoolSize,
                long keepAliveTime, 
                TimeUnit unit,
                BlockingQueue<Runnable> workQueue,
                ThreadFactory threadFactory)

corePoolSize：          线程池维护线程的最少数量 核心线程池数量
maximumPoolSize：       线程池维护线程的最大数量
keepAliveTime：         线程池维护线程所允许的空闲时间,超过这个时间非核心线程会被销毁
unit：                  线程池维护线程所允许的空闲时间的单位
workQueue：             线程池所使用的任务队列
threadFactory           新线程的创建工厂类

__创建新线程的策略__

如果线程池中的线程数量未达到核心线程池数量,则新建一个核心线程    
线程池中的线程数量已经超过核心线程数量,任务会被添加到BlockingQueue阻塞队列中    
如果阻塞队列已满,线程池会创建一个非核心线程来处理任务.直到达到线程池最大数量    
达到最大值的话线程池会拒绝执行任务    

线程池创建后,核心线程会一直存活    


__常见的线程池__

* FixedThreadPool 创建线程数量固定大小的线程池,只有核心线程,任务队列大小没有限制.适用于快速响应请求.创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。

        ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 10; i++) {
                final int index = i;
                fixedThreadPool.execute(new Runnable() {
                        @Override
                        public void run() {
                                try {
                                        System.out.println(index);
                                        Thread.sleep(2000);
                                } catch (InterruptedException e) {
                                        // TODO Auto-generated catch block
                                        e.printStackTrace();
                                }
                        }
                });
        }

* CachedThreadPool 创建一个可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它们。只有非核心线程,最大线程数没有限制,适合执行大量耗时较少的任务.线程池为无限大，当执行第二个任务时第一个任务已经完成，会复用执行第一个任务的线程，而不用每次新建线程。

        ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
        for (int i = 0; i < 10; i++) {
                final int index = i;
                try {
                        Thread.sleep(index * 1000);
                } catch (InterruptedException e) {
                        e.printStackTrace();
                }
        
                cachedThreadPool.execute(new Runnable() {
                        @Override
                        public void run() {
                                System.out.println(index);
                        }
                });
        }

* ScheduledThreadPool 核心线程数固定,非核心线程数没有限制，此线程池支持定时以及周期性执行任务的需求。

表示延迟3秒执行。

        ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);
        scheduledThreadPool.schedule(new Runnable() {
        
                @Override
                public void run() {
                        System.out.println("delay 3 seconds");
                }
        }, 3, TimeUnit.SECONDS);

表示延迟1秒后每3秒执行一次。

        scheduledThreadPool.scheduleAtFixedRate(new Runnable() {
                @Override
                public void run() {
                        System.out.println("delay 1 seconds, and excute every 3 seconds");
                }
        }, 1, 3, TimeUnit.SECONDS);


* SingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

        ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 10; i++) {
                final int index = i;
                singleThreadExecutor.execute(new Runnable() {
        
                        @Override
                        public void run() {
                                try {
                                        System.out.println(index);
                                        Thread.sleep(2000);
                                } catch (InterruptedException e) {
                                        // TODO Auto-generated catch block
                                        e.printStackTrace();
                                }
                        }
                });
        }

### __代码示例__

需要执行的Runnable任务

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

手动创建线程池

        private static int produceTaskSleepTime = 2;
        private static int produceTaskMaxNumber = 10;

        // 构造一个线程池
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(2, 4, 3,
                TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(3),
                new ThreadPoolExecutor.DiscardOldestPolicy());

执行Callable任务

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
实现RejectedExecutionHandler

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

--------


