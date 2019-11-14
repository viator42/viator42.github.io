---
layout: post
title:  "Android 多线程/多进程相关"
date:   2017-08-09
categories: android
---

# 多线程

### 线程和进程有什么区别    

一个进程是一个独立(self contained)的运行环境，它可以被看作一个程序或者一个应用。而线程是在进程中执行的一个任务。线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。

### 类在多线程中的执行过程

多个线程共享一个类对象,这个对象是被创建在主内存(堆内存)中，每个线程都有自己的工作内存(线程栈).工作内存存储了主内存对象的一个副本，当线程操作C对象时，首先从主内存复制对象到工作内存中，然后执行代码最后用工作内存对象刷新主内存对象。当一个对象在多个内存中都存在副本时，如果一个内存修改了共享变量，其它线程也应该能够看到被修改后的值，此为可见性。保证多个线程按照顺序进行，此为有序性

## 线程的状态

Java线程在运行的声明周期中可能会处于6种不同的状态

* New：新创建状态。线程被创建，还没有调用 start 方法，在线程运行之前还有一些基础工作要做。
* Runnable：可运行状态。一旦调用start方法，线程就处于Runnable状态。一个可运行的线程可能正在运行也可能没有运行，这取决于操作系统给线程提供运行的时间。
* Blocked：阻塞状态。表示线程被锁阻塞，它暂时不活动。
* Waiting：等待状态。线程暂时不活动，并且不运行任何代码，这消耗最少的资源，直到线程调度器重新激活它。
* Timed waiting：超时等待状态。和等待状态不同的是，它是可以在指定的时间自行返回的。
* Terminated：终止状态。表示当前线程已经执行完毕。导致线程终止有两种情况：第一种就是run方法执行完毕正常退出；第二种就是因为一个没有捕获的异常而终止了run方法，导致线程进入终止状态。

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

阻塞队列与普通队列的区别在于，当队列是空的时，从队列中获取元素的操作将会被阻塞，或者当队列是满时，往队列里添加元素的操作会被阻塞。试图从空的阻塞队列中获取元素的线程将会被阻塞，直到其他的线程往空的队列插入新的元素。同样，试图往已满的阻塞队列中添加新元素的线程同样也会被阻塞，直到其他的线程使队列重新变得空闲起来，如从队列中移除一个或者多个元素，或者完全清空队列

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

* ArrayBlockingQueue ：     
用数组实现的有界阻塞队列，并按照先进先出（FIFO）的原则对元素进行排序。默认情况下不保证线程公平地访问队列。公平访问队列就是指阻塞的所有生产者线程或消费者线程，当队列可用时，可以按照阻塞的先后顺序访问队列。插入获取元素的时候使用阻塞的方式。即先阻塞的生产者线程，可以先往队列里插入元素；先阻塞的消费者线程，可以先从队列里获取元素。

* LinkedBlockingQueue ：    
它是基于链表的阻塞队列，同ArrayListBlockingQueue类似，此队列按照先进先出（FIFO）的原则对元素进行排序，其内部也维持着一个数据缓冲队列（该队列由一个链表构成）。生产者向队列中放入数据的时候会进入缓存区，直到缓存区满的时候才会触发阻塞队列。LinkedBlockingQueue对生产者和消费者端使用了独立的锁来控制同步，这也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。

* PriorityBlockingQueue ：  
它是一个支持优先级的无界队列。默认情况下元素采取自然顺序升序排列。这里可以自定义实现compareTo（）方法来指定元素进行排序规则；或者初始化PriorityBlockingQueue时，指定构造参数Comparator来对元素进行排序。但其不能保证同优先级元素的顺序。

* DelayQueue：              
它是一个支持延时获取元素的无界阻塞队列。队列使用PriorityQueue来实现。队列中的元素必须实现Delayed 接口。创建元素时，可以指定元素到期的时间，只有在元素到期时才能从队列中取走。

* SynchronousQueue：        
它是一个不存储元素的阻塞队列。每个插入操作必须等待另一个线程的移除操作，同样任何一个移除操作都等待另一个线程的插入操作。因此此队列内部其实没有任何一个元素，或者说容量是0，严格来说它并不是一种容器。由于队列没有容量，因此不能调用peek操作（返回队列的头元素）。

* LinkedTransferQueue：     
它是一个由链表结构组成的无界阻塞TransferQueue队列。LinkedTransferQueue实现了一个重要的接口TransferQueue。该接口含有5个方法，其中有3个重要的方法，它们分别如下所示。    
（1）transfer（E e）：若当前存在一个正在等待获取的消费者线程，则立刻将元素传递给消费者；如果没有消费者在等待接收数据，就会将元素插入到队列尾部，并且等待进入阻塞状态，直到有消费者线程取走该元素。    
（2）tryTransfer（E e）：若当前存在一个正在等待获取的消费者线程，则立刻将元素传递给消费者；若不存在，则返回 false，并且不进入队列，这是一个不阻塞的操作。与 transfer 方法不同的是，tryTransfer方法无论消费者是否接收，其都会立即返回；而transfer方法则是消费者接收了才返回。    
（3）tryTransfer（E e，long timeout，TimeUnit unit）：若当前存在一个正在等待获取的消费者线程，则立刻将元素传递给消费者；若不存在则将元素插入到队列尾部，并且等待消费者线程取走该元素。若在
指定的超时时间内元素未被消费者线程获取，则返回false；若在指定的超时时间内其被消费者线程获取，则返回true。    

* LinkedBlockingDeque：     
它是一个由链表结构组成的双向阻塞队列。双向队列可以从队列的两端插入和移出元素，因此在多线程同时入队时，也就减少了一半的竞争。由于是双向的，因此LinkedBlockingDeque多了addFirst、addLast、
offerFirst、offerLast、peekFirst、peekLast等方法。其中，以First单词结尾的方法，表示插入、获取或移除双端队列的第一个元素；以Last单词结尾的方法，表示插入、获取或移除双端队列的最后一个元素。

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

* FixedThreadPool    
创建线程数量固定大小的线程池,只有核心线程,任务队列大小没有限制.适用于快速响应请求.创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。    
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

* CachedThreadPool    
创建一个可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它们。只有非核心线程,最大线程数没有限制,适合执行大量耗时较少的任务.线程池为无限大，当执行第二个任务时第一个任务已经完成，会复用执行第一个任务的线程，而不用每次新建线程。

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

* ScheduledThreadPool    
核心线程数固定,非核心线程数没有限制，此线程池支持定时以及周期性执行任务的需求。      

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


* SingleThreadExecutor    
创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。    

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

--------

# ASyncTask相关

一般声明在Activity类中作为内内部类.标注三个参数的类型
第一个参数表示要执行的任务通常是网络的路径。第二个参数表示进度的刻度，第三个参数表示任务执行的结果。

重写方法完成操作.

*  onPreExecute 表示任务执行之前的操作.    
*  doInBackground方法实现耗时的任务。    
*  onPostExecute 主要是更新UI的操作.    

    public class ListAllTask extends AsyncTask<String, Void, String> {
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

新建WorkerRunnable对象，WorkerRunnable implements Callable接口，call方法调用doInBackground()方法，结果返回Result对象，最后调用postResult方法返回结果

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

创建FutureTask对象，FutureTask实现了done方法在任务完成时调用postResultIfNotInvoked() -> postResult()返回结果

    mFuture = new FutureTask<Result>(mWorker) {
            @Override
            protected void done() {
                try {
                    postResultIfNotInvoked(get());
                } catch (InterruptedException e) {
                    android.util.Log.w(LOG_TAG, e);
                } catch (ExecutionException e) {
                    throw new RuntimeException("An error occurred while executing doInBackground()",
                            e.getCause());
                } catch (CancellationException e) {
                    postResultIfNotInvoked(null);
                }
            }
        };

调用postResult返回结果

    private void postResultIfNotInvoked(Result result) {
        final boolean wasTaskInvoked = mTaskInvoked.get();
        if (!wasTaskInvoked) {
            postResult(result);
        }
    }

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

--------

execute()方法执行    
调用execute() -> executeOnExecutor()

executeOnExecutor()    

    onPreExecute()
    mWorker.mParams = params;   //Callable对象参数赋值
    exec.execute(mFuture);  //线程添加到线程池

finish() -> onPostExecute()

    private void finish(Result result) {
        if (isCancelled()) {
            onCancelled(result);
        } else {
            onPostExecute(result);
        }
        mStatus = Status.FINISHED;
    }

执行完成之后利用Handler Message机制返回结果

    private static InternalHandler sHandler;

    private static Handler getHandler() {
        synchronized (AsyncTask.class) {
            if (sHandler == null) {
                sHandler = new InternalHandler();
            }
            return sHandler;
        }
    }

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

    private Result postResult(Result result) {
        @SuppressWarnings("unchecked")
        Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
                new AsyncTaskResult<Result>(this, result));
        message.sendToTarget();
        return result;
    }

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

# Handler Looper机制

作用 用于在线程之间传递信息,子线程向主线程传递结果,进度信息.

Message：封装的消息体.    
其中包含了消息ID，消息处理对象以及处理的数据等，由MessageQueue统一列队，终由Handler处理。

参数:
* what 自定义消息id
* arg1, arg2 消息参数
* obj 消息内容,可以为任意类型
          
Handler：处理者，负责Message的发送及处理。在接收消息的地方定义.重写handleMessage(Message msg)方法来处理消息.

MessageQueue：消息队列，用来存放Handler发送过来的消息，并按照FIFO（先进先出）规则执行。当然，存放Message并非实际意义的保存，而是将Message以链表的方式串联起来的，等待Looper的抽取。内部封装的类,使用时不可见.

Looper：消息泵，不断地从MessageQueue中抽取Message执行。因此，一个MessageQueue需要一个Looper。UI线程自带Looper不需要定义.子线程在定义Hander的时候需要先初始化Looper,初始化Hander的代码写在Looper.prepare();和Looper.loop();之间.

ThreadLocal: ThreadLocal是一个线程内部的数据存储类，通过它可以在指定的线程中存储数据，数据存储以后，只有在指定线程中可以获取到存储的数据。 不同的线程存取获取的是不同的数据相互不会混淆.

__主线程和子线程通过Handler交互的实例__

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
                    //向子线程发送消息
                    Message message1 = Message.obtain();
                    String msg1 = "This is an message to sub thread";
                    message1.obj = msg1;
                    message1.what = MSG_TO_SUB;
                    toMainThreadHandler.sendMessage(message1);
                }
            });
            pushMsgToSubBtn.setEnabled(false);

            //定义handler
            toSubThreadHandler = new Handler() {
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);

                    switch (msg.what)
                    {
                        //接受来自子线程的消息
                        case MSG_TO_MAIN:
                            msgTextView.setText((String) msg.obj);
                            break;
                    }
                }
            };
        }

        private class SubThread extends Thread {
            @Override
            public void run() {
                super.run();

                //子线程向主线程发送消息
                Message toMainThreadMsg = new Message();
                toMainThreadMsg.what = 1;
                String msg = "A Messgae to Main Thread";
                toMainThreadMsg.obj = msg;
                toSubThreadHandler.sendMessage(toMainThreadMsg);

                //接收来自主线程的消息
                //需要初始化Looper
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

## ThreadLocal

ThreadLocal是一个线程内部的数据存储类，通过他可以在指定线程中存储数据，数据存储后，只有在指定线程中可以获取存储到的数据．　　　　
Handler使用ThreadLocal获取当前线程的Looper    

### ThreadLocal的原理

Thread类中有一个成员存储线程ThreadValue的数据,执行threadLocal.put()的时候先去获取当前的线程,然后再把数据放到对应的线程中.获取的时候也是一样,先获取当前的线程再查找数据.
从而实现每个线程的数据单独存取.

## 运行原理

Handler的运行需要MessageQueue和Looper的支持.Handler创建之后就会使用当前线程的Looper和MessageQueue.然后通过handler的send方法,把封装好数据的Message对象放到MessageQueue消息队列中.Looper不断的检查消息队列里的消息.发现有消息到来时就会取出并处理消息.最终handler的handleMessage方法被调用.

主线程运行MessageQueue和Looper用来获取消息,handler在子线程中的引用把消息存入到队列中.主线程的Looper收到消息后交由Handler处理


参考: Android开发艺术探索

--------

