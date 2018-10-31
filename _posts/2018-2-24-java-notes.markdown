---
layout: post
title:  "Java 基础相关笔记"
date:   2018-2-24
categories: Java
---

## HashMap遍历

第一种:

　　Map map = new HashMap();
　　Iterator iter = map.entrySet().iterator();
　　while (iter.hasNext()) {
        　　Map.Entry entry = (Map.Entry) iter.next();
        　　Object key = entry.getKey();
        　　Object val = entry.getValue();
　　}

效率高,以后一定要使用此种方式！

第二种:

　　Map map = new HashMap();
　　Iterator iter = map.keySet().iterator();
　　while (iter.hasNext()) {
        　　Object key = iter.next();
        　　Object val = map.get(key);
　　}

效率低,以后尽量少使用！

## Set遍历

        Set set = new HashSet();
        set.add(new String("11"));
        set.add(new String("222"));
        Iterator i = set.iterator();//先迭代出来
        while(i.hasNext()){//遍历
                System.out.println(i.next());
        }

--------

##Java反射机制

包名： java.lang.reflect    
作用: 反射机制就是可以把一个类,类的成员(函数,属性),当成一个对象来操作,希望读者能理解,也就是说,类,类的成员,我们在运行的时候还可以动态地去操作他们.获取程序运行时的内部结构并进行交互    

1.通过一个对象获得完整的包名和类名

    reflectionTester.getClass().getName()

获取class对象的方法：

1. 引用名.getClass()
2. 类名.getClass()
3. Class.forName()

2.由于Class也可以作为一个对象，可以使用Class.forName("com.package.ClassName")来新建一个类对象。

    Class<?> demo1=null;
    demo1=Class.forName("Reflect.Demo");
    System.out.println("类名称   "+demo1.getName());

3.生成的类对象可以用newInstance()来实例化,然后可以调用方法赋值等操作

    demo=Class.forName("Reflect.Person");
    per=(Person)demo.newInstance();

4.获取类的所有方法

    获得所有的公开方法：
    Method[] declaredMethods = c.getDeclaredMethods();

### 反射实例代码

    package com.viator42.reftester;
    public class TargetCls {
        private long id;
        private String name;
        private String nickname;
        
        public long getId() {
                return id;
        }
        public void setId(long id) {
                this.id = id;
        }
        public String getName() {
                return name;
        }
        public void setName(String name) {
                this.name = name;
        }
        
        public void printInfo() {
                System.out.println(this.id + " " + this.name);
        }
        
        public void changeNickname(String nickname) {
                this.nickname = nickname;
                System.out.println(this.nickname);
        }
    }

    package com.viator42.reftester;
    public class TargetClsConstructor {
        private long id;
        private String name;
        
        public TargetClsConstructor(long id, String name) {
                super();
                this.id = id;
                this.name = name;
        }
        
        public long getId() {
                return id;
        }
        public void setId(long id) {
                this.id = id;
        }
        public String getName() {
                return name;
        }
        public void setName(String name) {
                this.name = name;
        }
    }

    package com.viator42.reftester;
    import java.lang.reflect.*;
    public class Main {
        public static void main(String[] args) {
                TargetCls target = new TargetCls();
                target.setId(1);
                target.setName("AAAAA");
                
                //获取类名
                System.out.println(target.getClass().getName());
                System.out.println(target.getClass().getPackage().getName());
                
                //反射创建类
                try {
                        Class<?> cls1 = Class.forName("com.viator42.reftester.TargetCls");
                        TargetCls target2 = (TargetCls) cls1.newInstance();
                        target2.setId(2);
                        target2.setName("BBBB");
                        System.out.println(target2.getId() + " " + target2.getName());
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
                //通过Java反射机制得到一个类的构造函数，并实现创建带参实例对象
                try {
                        Class<?> cls2 = Class.forName("com.viator42.reftester.TargetClsConstructor");
                        Constructor<?>[] constructors = cls2.getConstructors();
                        TargetClsConstructor target3 = (TargetClsConstructor) constructors[0].newInstance(123, "CCCC");
                        System.out.println(target3.getId() + " " + target3.getName());
                        
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
                //通过Java反射机制操作成员变量,即使变量为private
                try {
                        Class<?> cls1 = Class.forName("com.viator42.reftester.TargetCls");
                        TargetCls target2 = (TargetCls) cls1.newInstance();
                        Field idField = cls1.getDeclaredField("id");
                        Field nameField = cls1.getDeclaredField("name");
                        Field nicknameField = cls1.getDeclaredField("nickname");
                        idField.setAccessible(true);
                        idField.set(target2, 999); 
                nameField.setAccessible(true);
                        nameField.set(target2, "Viator42"); 
                        nicknameField.setAccessible(true);
                        nicknameField.set(target2, "haha");
                        
                        System.out.println(target2.getId() + " " + target2.getName());
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
                //通过Java反射机制调用类方法
                try {
                        Class<?> class4 = Class.forName("com.viator42.reftester.TargetCls");
                        TargetCls target4 = (TargetCls) class4.newInstance();
                        target4.setId(456);
                        target4.setName("CallMethod");
                        
                Method method = class4.getMethod("printInfo"); 
                method.invoke(target4); 
                
                Method method2 = class4.getMethod("changeNickname", String.class); 
                method2.invoke(target4, "NewNickname"); 
                
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
        }
    }

个人觉得使用反射机制的一些地方：    

1. 工厂模式：Factory类中用反射的话，添加了一个新的类之后，就不需要再修改工厂类Factory了
2. 数据库JDBC中通过Class.forName(Driver).来获得数据库连接驱动
3. 分析类文件：毕竟能得到类中的方法等等
4. 访问一些不能访问的变量或属性：破解别人代码

--------
## 注解Annotations

### 什么是Annotation?

Annotation翻译为中文即为注解，意思就是提供除了程序本身逻辑外的额外的数据信息。Annotation对于标注的代码没有直接的影响，它不可以直接与标注的代码产生交互，但其他组件可以使用这些信息。    
Annotation信息可以被编译进class文件，也可以保留在Java 虚拟机中，从而在运行时可以获取。甚至对于Annotation本身也可以加Annotation。    

### Annotation的作用    

a. 标记，用于告诉编译器一些信息    
b. 编译时动态处理，如动态生成代码    
c. 运行时动态处理，如得到注解信息    

### 那些对象可以加Annotation    

类，方法，变量，参数，包都可以加Annotation。

### Java内置的Annotation

@Override 是一个标记型Annotation，说明了被标注的方法覆盖了父类的方法，起到了断言的作用。如果给一个非覆盖父类方法的方法添加该Annotation，编译器将报编译错误。它有两个典型的使用场景，一是在试图覆盖父类方法却写错了方法名时报错，二是删除已被子类覆盖（且用Annotation修饰）的父类方法时报错。

@Deprecated 标记型Annotation，说明被修改的元素已被废弃并不推荐使用，编译器会在该元素上加一条横线以作提示。该修饰具有一定的“传递性”：如果我们通过继承的方式使用了这个弃用的元素，即使继承后的元素（类，成员或者方法）并未被标记为@Deprecated，编译器仍然会给出提示。

@SuppressWarnnings 用于通知Java编译器关闭对特定类、方法、成员变量、变量初始化的警告。此种警告一般代表了可能的程序错误，例如当我们使用一个generic collection类而未提供它的类型时，编译器将提示“unchecked warning”的警告。通常当这种情况发生时，我们需要查找引起警告的代码，如果它真的表示错误，我们就需要纠正它。然而，有时我们无法避免这种警告，例如，我们使用必须和非generic的旧代码交互的generic collection类时，我们无法避免这个unchecked warning，此时可以在调用的方法前增加@SuppressWarnnings通知编译器关闭对此方法的警告。

@SuppressWarnnings不是标记型Annotation，它有一个类型为String[]的成员，这个成员的值为被禁止的警告名。常见的警告名为下。

* unchecked 执行了未检查的转换时的警告。例如当使用集合时没有用泛型来指定集合的类型
* finally finally子句不能正常完成时的警告
* fallthrough 当switch程序块直接通往下一种情况而没有break时的警告
* deprecation 使用了弃用的类或者方法时的警告
* seriel 在可序列化的类上缺少serialVersionUID时的警告
* path 在类路径、源文件路径等中有不存在的路径时的警告
* all 对以上所有情况的警告

### 元Annotation

包括@Retention, @Target, @Inherited, @Documented，元 Annotation 是指用来定义 Annotation 的 Annotation

@Documented 是否会保存到 Javadoc 文档中    
@Retention 保留时间，可选值 SOURCE（源码时），CLASS（编译时），RUNTIME（运行时），默认为 CLASS，值为 SOURCE 大都为 Mark    Annotation，这类 Annotation 大都用来校验，比如 Override, Deprecated, SuppressWarnings     
@Target 可以用来修饰哪些程序元素，如 TYPE, METHOD, CONSTRUCTOR, FIELD, PARAMETER 等，未标注则表示可修饰所有    

* @Target(ElementType.ANNOTATION_TYPE)：指定该该策略的Annotation只能修饰Annotation.
* @Target(ElementType.TYPE) //接口、类、枚举、注解
* @Target(ElementType.FIELD) //成员变量（字段、枚举的常量）
* @Target(ElementType.METHOD) //方法
* @Target(ElementType.PARAMETER) //方法参数
* @Target(ElementType.CONSTRUCTOR) //构造函数
* @Target(ElementType.LOCAL_VARIABLE)//局部变量
* @Target(ElementType.PACKAGE) ///修饰包定义

@Inherited 是否可以被继承，默认为 false

### 自定义Annotation

自定义 Annotation 表示自己根据需要定义的 Annotation，定义时需要用到上面的元 Annotation

* Annotation只有一个属性的时候

    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface Anno1 {
        String value() default "defaultName";
    }

    public class AnnoClass {
        @Anno1("param1 Annotation Value")
        public String param1;
        
        @Anno1("param2 Annotation Value")
        public String param2;
    }

* Annotation有多个属性的时候

    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface Anno2 {
        String value1();
        String value2();
        String value3();
        String value4();
    }

    public class Anno2Class {
        @Anno2(value1 = "param1 Annotation Value",
                        value2 = "param2 Annotation Value",
                        value3 = "param3 Annotation Value",
                        value4 = "param4 Annotation Value")
        public String param1;
    }

### 解析Annotation(使用反射)

    public class Main {
        public static void main(String[] args) {
                System.out.println("Annotation Tester");
                
                AnnoClass annoClass = new AnnoClass();
                annoClass.param1 = "param1";
                annoClass.param2 = "param2";
                
                try {
                        Field[] fields = annoClass.getClass().getFields();
                        for(Field field : fields) {
                            Anno1 anno1 = field.getAnnotation(Anno1.class);
                            System.out.println(anno1.value());
                        }
                        
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
                Anno2Class anno2Class = new Anno2Class();
                anno2Class.param1 = "param1";
                
                try {
                        Field[] fields = anno2Class.getClass().getFields();
                        for(Field field : fields) {
                            Anno2 anno2 = field.getAnnotation(Anno2.class);
                                System.out.println(anno2.value1()+
                                    "\n"+anno2.value2()+
                                    "\n"+anno2.value3()+
                                    "\n"+anno2.value4());
                        }
                        
                } catch (Exception e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
                
        }

--------

## Java 强引用和弱引用

引用分为强引用、 软引用、 弱引用、虚引用

1. 强引用:    
不会被垃圾回收器回收,内存空间不足的时候会抛出OutOfMemoryError异常.    

 使用:

        Object obj = new Object();

2. 软引用(SoftReference)    
如果内存空间足够，垃圾回收器就不会回收它，如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。    

使用:

        import java.lang.ref.SoftReference;

        Object obj = new Object();
        SoftReference<Object> sr = new SoftRefernce<Object>(obj);
        obj = null; //释放强引用
        sr.get();  //获取对象
        sr.clear(); //清除引用

3. 弱引用(WeakReference)     
弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它 所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。    
使用与软引用类似    

4. 虚引用(PhantomReference)     
虚引用顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。
虚引用主要用来跟踪对象被垃圾回收的活动。虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列（ReferenceQueue）联合使用。当垃 圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是 否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。程序如果发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的行动。


__Android中弱引用与软引用的应用场景__

软引用用在内存开销比较大的类中,比如Bitmap.如果内存空间不足就会主动被释放防止OOM.由于在GC的时候无论内存空间是否足够都会被释放,在不常用的对象使用弱引用    

--------

## Java多线程

线程和进程有什么区别    
一个进程是一个独立(self contained)的运行环境，它可以被看作一个程序或者一个应用。而线程是在进程中执行的一个任务。线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。

类在多线程中的执行过程

多个线程共享一个类对象,这个对象是被创建在主内存(堆内存)中，每个线程都有自己的工作内存(线程栈).工作内存存储了主内存对象的一个副本，当线程操作C对象时，首先从主内存复制对象到工作内存中，然后执行代码最后用工作内存对象刷新主内存对象。当一个对象在多个内存中都存在副本时，如果一个内存修改了共享变量，其它线程也应该能够看到被修改后的值，此为可见性。保证多个线程按照顺序进行，此为有序性

### 线程同步

synchronized的作用是给代码块加上互斥锁,每个时间点只能有一个线程可以访问.避免线程乱序执行导致的数据的不一致.保证数据一致性

使用synchronized包裹需要同步的代码块

        synchronized (this) {  
                dosomething;
        } 

将synchronized加在需要互斥的方法上

        public synchronized void func() {
        }

### 线程池

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

## Callable、Future和FutureTask

__Callable与Runnable的区别__

Thread,Runnable的方式Runnable执行完成后无法返回结构

    public interface Runnable {  
        public abstract void run();  
    }  

Callable使用范型定义返回值

    public interface Callable<V> {   
        V call() throws Exception;   
    }

__Future<V>接口__

Future<V>接口是用来获取异步计算结果的，就是对具体的Runnable或者Callable对象任务执行的结果进行获取(get()),取消(cancel()),判断是否完成等操作。

    public interface Future<V> {  
        boolean cancel(boolean mayInterruptIfRunning);  
        boolean isCancelled();  
        boolean isDone();  
        V get() throws InterruptedException, ExecutionException;  
        V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;  
    }  

接口方法

* cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。    

* isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。

* isDone方法表示任务是否已经完成，若任务完成，则返回true；

* get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；

* get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。

也就是说Future提供了三种功能：

1. 判断任务是否完成；
2. 能够中断任务；
3. 能够获取任务执行结果。

Future只是一个接口，所以是无法直接用来创建对象使用的,应该创建继承了Future接口的FutureTask类    
FutureTask类实现了RunnableFuture接口

        public interface RunnableFuture<V> extends Runnable, Future<V> {
                void run();
        }

可以看出RunnableFuture继承了Runnable接口和Future接口，而FutureTask实现了RunnableFuture接口。所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值。

### 使用示例

__实现Callble接口的任务__

class Task implements Callable<String> {
    private String msg;

    public Task(String msg) {
        this.msg = msg;
    }

    @Override
    public String call() throws Exception {
        System.out.println(msg);
        return msg + " done";
    }
}

__Callable + Future__    
submit提交一个实现Callable接口的任务，并且返回封装了异步计算结果的Future。

        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task("Task 01");

        Future<String> future = executor.submit(task);
        try {
        String result = future.get();
        System.out.println(result);

        } catch (InterruptedException e) {
        e.printStackTrace();
        } catch (ExecutionException e) {
        e.printStackTrace();
        }

__Callable + FutureTask__
用FutureTask封装一个实现Callable接口的任务,并且返回封装了异步计算结果的Future

        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task("Task 02");
        FutureTask<String> futureTask = new FutureTask<String>(task);
        executor.submit(futureTask);
        try {
            String result = futureTask.get();
            System.out.println(result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

--------
## Socket相关

Socket不是一种协议，而是一个编程调用接口（API），属于传输层（主要解决数据如何在网络中传输）    

成对出现，一对套接字

        Socket ={(IP地址1:PORT端口号)，(IP地址2:PORT端口号)}

Socket的使用类型主要有两种：

* 流套接字（streamsocket） ：基于 TCP协议，采用 流的方式 提供可靠的字节流服务
* 数据报套接字(datagramsocket)：基于 UDP协议，采用 数据报文 提供数据打包发送的服务

### Socket 与 Http 对比

* Socket属于传输层，因为 TCP / IP协议属于传输层，解决的是数据如何在网络中传输的问题.HTTP协议 属于 应用层，解决的是如何包装数据     
* Http：采用 请求—响应 方式。即建立网络连接后，当 客户端 向 服务器 发送请求后，服务器端才能向客户端返回数据。
* Socket：采用 服务器主动发送数据 的方式,即建立网络连接后，服务器可主动发送消息给客户端，而不需要由客户端向服务器发送请求    

--------

## 设计模式

### Iteator模式

        public interface Aggregate() {
                public abstract Iterator iterator()
        }

        这个接口的方法会生成一个Iterator接口的迭代器

        public interface Iterator() {
                hasNext();
                next();
        }

迭代器方法包含是否有下一个元素的hasNext方法和访问元素的next方法    
定义迭代器类implements Iterator接口,实现迭代方法    
需要聚合的类implements Aggregate接口新建一个迭代器   
聚合类调用iterator方法新建迭代器,while循环访问元素

### Adapter适配器模式

Adapter适配器类提供一套接口,Adaptee被适配的对象当作参数传入.将被适配对象的接口在适配器接口方法中调用

        public class Adapter() {
                private Adaptee adaptee;

                public Adapter(Adaptee adaptee) {
                        this.adapee = adapee;
                }

                public void funcAdapted() {
                        adapee.func();
                }
        }

### Template Method模板模式

在父类中处理流程的框架,子类中实现具体处理.

Template Method是带有模板功能的模式,模板类定义成抽象类,包括模板的处理流程和处理方法.处理方法定义成抽象的由子类来实现.只要在不同的子类中实现不同的具体处理,父类的模板方法被调用的时候程序行为也会有所不同.不论子类的实现类如何,流程都会按照父类定义的那样进行.

### Factory Method工厂方法模式

Template Method方式构建实例的工厂.父类定义生成实例的方法但不决定具体要生成的类,具体的处理全部交给子类来负责.这样可以将生成实例的框架和负责生成实例的类解耦.

### Singleton单例模式

保证一个类只允许存在一个实例

操作方法

        class Singleton() {
                private static Singleton singleton;     //类中定义自己的静态实例

                //构造方法私有化
                private Singleton() {   
                }

                //通过静态方法获取实例
                public static Singleton getInstance() {
                        if(singleton != null) {
                                singleton = new Singleton();
                        }
                        return singleton;
                }

        }

### Builder模式

作用是用于组装复杂的实例

定义抽象的Builder类,包含组成各个组件的方法

        public abstract class Builder {
                public abstract buildPart1();
                public abstract buildPart2();
                public abstract buildPart3();
        }

子类继承这个抽象类,重写组成部件的方法

定义Director类

Directior类使用Builder类声明的方法来构造对象.把builder类的实例作为参数传到Directior中    
执行construct方法完成构建

public class Director {
        public Director(Builder builder) {

        }

        public void construct() {
                builder.buildPart1();
                builder.buildPart1();
                builder.buildPart1();
        }
}

可以定义不同的子类扩展自Builder完成构建不同的对象,但都共用同一个Driector的构建过程

### Abstract Factory抽象工厂模式

不关心零件的具体实现,只关心接口API.仅使用接口API将零件组装成产品.

每一类零件都创建对应的抽象类,

Part零件类

        public abstract class Part {
                public String output();
        }

继承零件类

        public class Part1 extends Part {
                public String output(){
                        return "Part1";
                }
        }

Facotoy工厂类

public abstract Factory {
        public static Factory getFactory (String className) {
                Factory factory = null;
                factory = (Factory) Class.forName(className).getInstance();

        }

        public abstract Part1 buildPart1();
        public abstract Part2 buildPart2();
        public abstract Part3 buildPart3();
}

### Observer 观察者模式

分为Observer观察者和Observable被观察者.观察者定义update方法在被通知的时候调用.被观察者保存观察者的列表.定义addObserver和RemoveObserver方法管理观察者.被观察者通过遍历观察者列表调用update方法给观察者发送通知

### Bridge 桥接模式

桥接模式是将类的功能层级结构和实现层级结构分离

实现类的基类,使用时定义这个类的子类并实现操作方法

public abstract class OperatorImpl {
        public void doSomething();
}


结构类,包含实现类的引用

        public class Operator {
                private OperatorImpl operatorImpl;

                public void doSomething() {
                        operatorImpl.doSomething();
                }
        }

### Stragegy 策略模式

策略模式是把策略从实体类中提取出来,随时更换

        //策略的接口
        interface Strategy {
                public void strategy();
        }

具体的策略类实现策略接口

        public class ActStrategy implements Strategy {
                public void strategy(){
                }
        }

使用策略的类,其中包含策略类的引用

        public class Game {
                public Strategy stragey;  //策略类的引用

                public void action() {
                        stragey.strategy();     //调用实际策略
                }

        }

### Composite 聚合模式

容器和内容保持一致性,创造出递归结构.    
文件和文件夹可以共享一个父类Enity,包含通用的属性比如名字,大小,创建日期等.文件夹下可以包括文件和文件夹,这些就可以用Enity类统一标识.方便进行遍历和组织.

        public abstract class Enity {
                public String getName();
                public String getSize();

                public void displayContent();
        }

实体类File

        public class File extends Enity {
                public void getContent() {
                        //输出文件内容
                }
        }

容器类Directory

        public class Directory extends Enity {
                List<Enity> content;   //文件夹的内容,包括文件和文件夹
                public void getContent() {
                        for(content) {
                                content.item.getContent();      //文件夹递归输出
                        }
                }
        }

### Decorator装饰模式

装饰元素和被装饰的元素继承同一个基类

        public abstract class BaseDecorator {
                //定义共通的操作方法
                public void show();
        }

被装饰的对象

        public abstract class Item extends BaseDecorator {
                public void show() {
                        //被装饰的对象操作
                        print("Item");
                }
        }

装饰对象

        public abstract class Decorator extends BaseDecorator {
                private BaseDecorator item;

                public Decorator(BaseDecorator item) {
                        this.item = item;
                }

                public void show() {
                        //装饰对象的操作
                        print("Decorator");
                        
                        //被装饰对象的操作
                        item.show();
                }
        }

调用: 被调用的对象当作参数传入装饰器对象中,由于共享同一套接口甚至可以进行嵌套调用,装饰器对象作为参数传入另一个装饰器中

        new Decorator(new Decorator(new Item()));

Java中装饰器应用较多的是java.io输入输出流相关的类

### Visitor 访问者模式

Visitor模式中,数据结构和处理被分开.编写一个访问者类访问数据中的元素,并把元素的处理交给访问者类.当增加新的处理时.只需要编写新的访问者,然后让数据接受新的访问者的访问即可.

创建Visitor访问者接口

        public abstract class Visitor {
                public abstract void visit(Enity enity);
                public abstract void visit(Enity2 enity);       //如果需要访问多个目标类,就重载visit方法
        }

扩展Visitor类,实现访问方法

        public class EnityVisitor extends Visitor {
                public void visit(Enity enity) {
                        //visitor对数据类进行访问操作
                }
                public void visit(Enity2 enity) {
                        //visitor对数据类进行访问操作
                }
        }

被访问的数据类,需要定义一个方法传入访问者

        public interface Element {
                public abstract void accept(Visitor v);
        }

        public class Enity implements Element {
                private String name;
                ...
                public void accept(Visitor v) {
                        v.visit(this);
                }
        }

使用

        Enity enity = new Enity();
        enity.accept(new EnityVisitor());

### 职责链模式

### Momento 备忘录模式

用于保存一个类的状态,并可以从保存的状态中恢复现场.

Momento类保存目标类的数据

        public class Momento {
                public int value;
        }

目标类定义创建备忘录和从备忘录恢复两个方法

        public class Target {
                public int value;

                public Momento createMomento() {
                        Momento m = new Momento();
                        m.value = value;
                        return value;
                }

                public void restoreMomento(Momento m) {
                        value = m.value;
                }
        }

### State状态模式

### Proxy 代理模式

代理类和被代理的类共同继承同一个接口,代理类中有被代理对象的引用

代理类和被代理的类共同继承的接口

        public interface Proxyable {
                public void doAction();
        }

实际调用的类,被代理

        public class Real implements Proxyable {
                public void doAction() {
                        //实际操作
                }
        }

代理类,包含被代理类的引用

        public class Proxy implements Proxyable {
                public Real real;

                public void doAction() {
                        real.doAction();
                }

        }

使用
        Real real = new Real();
        Proxy proxy = new Proxy();
        proxy.real = real;

        proxy.doAction();

### FlyWeight模式

--------

# Java 虚拟机笔记 

(内容整理自深入理解Java虚拟机)

## 堆和栈的结构

运行时数据区域分为堆Heap和栈Stack    
栈包括局部变量表,存放了基本数据类型(int,float,char,double)的对象引用,相当于指向对象起始地址的指针.本地方法栈.如果线程申请的栈深度大于虚拟机允许的深度就会报出StackOverFlow异常.虚拟机的栈可以动态扩展,取决于申请到的内存,如果无法申请到足够的内存,就会抛出OutOfMemory异常

堆是所有线程共享的一块内存区域,虚拟机启动的时候创建.作用是存放对象实例,所有的对象都在堆里分配内存.是垃圾处理器GC的主要清理区域.

方法区(Method Area)和堆一样,是各个线程的共享区域.用于存储已被虚拟机加载的类信息,常量,静态变量,即时编译后的代码数据.有一个别名叫做Non-Heap(非堆)

## 创建类对象的过程

虚拟机遇到一条new指令的时,先去检查这个指令的参数是否能在常量池中定位到一个类的符号引用.检查这个类是否被加载过.没有的话执行相应的加载过程    
类加载检查通过后在堆中把内存划分出来.

### 对象的内存布局

对象在内存中存储的布局可以分为3块区域:对象头(Header),实例数据(Instance Data)和对齐填充(Padding).

对象头包括自身的运行时数据(哈希码,GC分带年龄,锁状态标志...)和类型指针.类型指针是确定这个对象是哪个类的实例.如果对象是一个Java数组对象头还有一块记录数组的长度

## GC垃圾收集器

垃圾回收策略

引用计数算法无法解决对象之间循环引用的问题,一般使用可达性分析算法.从CG root开始向下搜索,搜索的路径被称为引用链,当一个对象到GC root没有引用链相连则判定这个对象为可回收

### 引用分类

__强引用__    
程序代码中必须的对象,一直存在,不会被GC回收

__软引用__    
用于描述有用但非必须的对象,将要发生内存溢出异常之前会被GC回收

__弱引用__    
弱引用的对象只能生存到下一次垃圾回收之前,无论当前内存是否足够都会被回收

__虚引用__    
对象的虚引用不会影响其生存时间,也无法通过虚引用获取对象实例.虚引用的用处是在对象被GC回收的时候收到一个系统通知

--------

## 虚拟机的类加载机制



--------