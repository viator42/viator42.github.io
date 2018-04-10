---
layout: post
title:  "Java 基础相关笔记"
date:   2018-2-24
categories: Java
---


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
3. 对线程进行管理

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

* FixedThreadPool 创建线程数量固定大小的线程池,只有核心线程,任务队列大小没有限制.适用于快速响应请求
* CachedThreadPool 创建一个可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它们。只有非核心线程,最大线程数没有限制,适合执行大量耗时较少的任务
* ScheduledThreadPool 核心线程数固定,非核心线程数没有限制，此线程池支持定时以及周期性执行任务的需求。
* SingleThreadExecutor 创建只有一个线程的线程池

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

线程池创建

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

