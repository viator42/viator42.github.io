---
layout: post
title:  "Java 基础相关笔记"
date:   2018-2-24
categories: Java
---

面向对象编程（OOP）

面向对象编程有很多重要的特性，比如：封装，继承，多态和抽象

“static”关键字表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。    
Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法跟类的任何实例都不相关，所以概念上不适用。    

4.是否可以在static环境中访问非static变量？    

static变量在Java中是属于类的，它在所有的实例中的值是一样的。当类被Java虚拟机载入的时候，会对static变量进行初始化。如果你的代码尝试不用实例来访问非static的变量，编译器会报错，因为这些变量还没有被创建出来，还没有跟任何实例关联上。    

ArrayList和LinkedList有什么区别？    

ArrayList和LinkedList都实现了List接口，他们有以下的不同点：    

ArrayList是基于索引的数据接口，它的底层是数组。它可以以O(1)时间复杂度对元素进行随机访问。与此对应，LinkedList是以元素列表的形式存储它的数据，每一个元素都和它的前一个和后一个元素链接在一起，在这种情况下，查找某个元素的时间复杂度是O(n)。    

相对于ArrayList，LinkedList的插入，添加，删除操作速度更快，因为当元素被添加到集合任意位置的时候，不需要像数组那样重新计算大小或者是更新索引。   

LinkedList比ArrayList更占内存，因为LinkedList为每一个节点存储了两个引用，一个指向前一个元素，一个指向下一个元素。

数组(Array)和列表(ArrayList)有什么区别？什么时候应该使用Array而不是ArrayList？

下面列出了Array和ArrayList的不同点：

Array可以包含基本类型和对象类型，ArrayList只能包含对象类型。    
Array大小是固定的，ArrayList的大小是动态变化的。    
ArrayList提供了更多的方法和特性，比如：addAll()，removeAll()，iterator()等等。    
对于基本类型数据，集合使用自动装箱来减少编码工作量。但是，当处理固定大小的基本数据类型的时候，这种方式相对比较慢。    

| 作用域 | 当前类 | 同包 | 子类 | 其他 |
| ----  | ---- | ---- | ----- |
| public | √ | √ | √ | √ |
| protected |  √ | √ | √ | × |
| default | √ | √ | × | × |
| private | √ | × | × | × |

---------

## 三元操作符if-else

        boolean-exp ? value-true : value-false;

示例:

        int a = 100, b = 102;
        int result = a > b ? a : b;

## enum枚举类型

定义枚举类型

        public static enum colors {RED, BLUE, YELLOW};

ordinal方法输出ini值    

        colors c = colors.BLUE;
        System.out.println(c.ordinal());

遍历

        for (colors c : colors.values()) {
            System.out.println((c));
        }

在switch语句中使用

        colors c = colors.BLUE;
        switch (c) {
            case BLUE:
                System.out.println("enum blue called");
                break;
        }

--------

## 类成员的访问权限    

* friendly      (不加修饰符的默认) 同一个类和同一个包中的类可以访问
* public        其他所有类都可以访问
* protected     同一个类和同一个包中的类可以访问,同一个包或者不同包的子类可以访问
* private       只有同一个类中可以访问

### 类class的访问权限

类的访问修饰符public,friendly,private的区别

* public        可以在任何地方被引用,所在的包或者其他包,每个java文件有一个public类与文件同名,作为访问接口
* friendly      (不加修饰符的默认) 只能被同一个包的类引用,包外无法访问
* private       无法被之外的其他的类访问

---------

## 继承

final修饰符    

类属性: 常量,初始化时必须赋值,不允许改变
方法:   不允许子类覆盖
类:     不允许其他类继承这个类

继承类构造方法的调用

对于没有参数的默认构造方法,初始化子类的时候会自动调用父类的构造方法.顺序是自顶向下依次调用    
带参数的构造方法需要子类使用super();调用父类的构造方法

---------

## 泛型

类属性的类型可以作为一个参数自定义

        public class Holder<T, B> {     //参数的类型
                private T t;        //属性的类型用符合代替
                private B b;

                public B getB() {
                        return b;
                }

                public void setB(B b) {
                        this.b = b;
                }

                public T getT() {
                        return t;
                }

                public void setT(T t) {
                        this.t = t;
                }

                public static void main(String[] args) {
                        System.out.println("Class Tester");

                        Holder<String, Integer> holder = new Holder<>();

                        holder.setT("aaaaa");
                        System.out.println(holder.getT());

                        holder.setB(123);
                        System.out.println(holder.getB());

                }
        }

---------

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

## Java反射机制

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

### 元注解

包括@Retention, @Target, @Inherited, @Documented，自定义注解的时候使用

* @Documented 是否会保存到 Javadoc 文档中    
* @Retention 表示注解在哪一个阶段可用，可选值 SOURCE（源码时），CLASS（编译时），RUNTIME（运行时），默认为 CLASS
* @Target 可以用来修饰哪些程序元素，如 TYPE, METHOD, CONSTRUCTOR, FIELD, PARAMETER 等，未标注则表示可修饰所有    
* @Inherited 是否可以被继承，默认为 false

@Target的可选值

* @Target(ElementType.ANNOTATION_TYPE)：指定该该策略的Annotation只能修饰Annotation.
* @Target(ElementType.TYPE) //接口、类、枚举、注解
* @Target(ElementType.FIELD) //成员变量（字段、枚举的常量）
* @Target(ElementType.METHOD) //方法
* @Target(ElementType.PARAMETER) //方法参数
* @Target(ElementType.CONSTRUCTOR) //构造函数
* @Target(ElementType.LOCAL_VARIABLE)//局部变量
* @Target(ElementType.PACKAGE) ///修饰包定义

### 自定义注解

自定义注解表示自己根据需要定义的注解，定义时需要用到上面的元注解

#### 只有一个属性的时候

__定义注解__

    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface Anno1 {
        String value() default "defaultName";
    }

__使用注解__

    public class AnnoClass {
        @Anno1("param1 Annotation Value")
        public String param1;
        
        @Anno1("param2 Annotation Value")
        public String param2;
    }

#### 有多个属性的时候

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

对定义的注解进行处理

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

### apt注解处理器

APT(Annotationprocessing tool)是一种注解处理工具,能对源代码进行分析并找出其中的注解进行额外处理

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

# Callable、Future和FutureTask

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

# Java 虚拟机笔记 

(内容整理自深入理解Java虚拟机)

## 堆和栈的结构

运行时数据区域分为堆Heap和栈Stack    
栈包括局部变量表,存放了基本数据类型(int,float,char,double)的对象引用,相当于指向对象起始地址的指针.本地方法栈.如果线程申请的栈深度大于虚拟机允许的深度就会报出StackOverFlow异常.虚拟机的栈可以动态扩展,取决于申请到的内存,如果无法申请到足够的内存,就会抛出OutOfMemory异常

堆是所有线程共享的一块内存区域,虚拟机启动的时候创建.作用是存放对象实例,所有的对象都在堆里分配内存.是垃圾处理器GC的主要清理区域.

方法区(Method Area)和堆一样,是各个线程的共享区域.用于存储已被虚拟机加载的类信息,常量,静态变量,即时编译后的代码数据.有一个别名叫做Non-Heap(非堆)

## 创建类对象的过程

虚拟机遇到一条new指令的时,先去检查这个指令的参数是否能在常量池中定位到一个类的符号引用.检查这个类是否被加载过.没有的话执行相应的加载过程    
类加载检查通过后在堆中把内存划分出来.如果内存是绝对规整的，用过的内存都放在一边，空闲的在另一边，中间放着一个指针作为分隔指示器，分配内存就是挪动指示器，这种分配方式叫做指针碰撞。    
如果已使用的内存和空闲的相互碰撞，虚拟机就必须维护一个列表记录哪些内存块是可用的。这种分配方式叫做空闲列表。

### 对象的内存布局

对象在内存中存储的布局可以分为3块区域:对象头(Header),实例数据(Instance Data)和对齐填充(Padding).

对象头包括自身的运行时数据(哈希码,GC分带年龄,锁状态标志...)和类型指针.类型指针是确定这个对象是哪个类的实例.如果对象是一个Java数组对象头还有一块记录数组的长度

为了保证分配内存的操作是线程安全的，有两个方法，一是虚拟机在分配内存的操作进行同步处理。另一种是每个线程在堆中预先分配一块内存，内存分配的动作在不同空间中进行。

## GC垃圾收集器

### 垃圾回收策略

1.引用计数算法

给对象一个引用计数器,有一个地方引用它时+1,引用失效时-1.为0的时候就会被GC回收      
引用计数算法无法解决对象之间循环引用的问题,一般使用可达性分析算法.从CG root开始向下搜索,搜索的路径被称为引用链,当一个对象到GC root没有引用链相连则判定这个对象为可回收

2.可达式分析算法

这个算法的基本思路就是通过一系列的成为GC Root的对象作为起点进行搜索，搜索的路径根据引用链，对象没有引用链相连的时候就判定这个对象是不可用的，所以就会被判定为可回收的对象。

### 垃圾回收算法

1. 标记/回收算法

首先标记所有需要回收的对象,然后统一回收,这种算法的缺点是回收后的空闲空间不连续,容易产生内存碎片

2. 复制算法

把内存分为两块,分配空间只在其中一块上操作.这一块用完之后就把所有存活的对象移动到另一块内存空间,然后已使用的空间一次清空.    
两块空间不一般大,存放存活对象的空间比较小

3. 标记/整理算法

标记所有需要回收的对象,然后把存活的对象向内存地址的一端移动,最后直接清理掉存活对象边界以外的空间.这样可以使空余的内存空间最大而且连续

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

## Class类文件的结构

__魔数__ 0xCAFEBABE 表示class文件

__常量池__ 保存的是字面量和符号引用     
字面量是指声明为final的常量,符号引用是类和接口的名称,字段,方法的名称    

__访问标志__ 是否是public类型,是否是abstract类型

__类索引__  父类索引,接口索引集合

__字段表__

__方法表__

__属性表__

--------

## 虚拟机的类加载机制

Java语音类型的加载,连接,初始化都是在程序运行时完成的

### 类加载的步骤

加载 -> 连接 -> 准备 -> 解析 -> 初始化 -> 使用 -> 卸载

触发类初始化的情况

* new创建类对象
* java.jang.reflect 反射调用
* 初始化一个类的时候,如果父类没有初始化则先触发父类的初始化
* 虚拟机启动的时候先触发主类(有main方法)的初始化

### 类加载的过程

#### 加载    
获取类的二进制字节流,将静态存储结构转化为运行时的数据结构,在内存中生成这个类的java.lang.Class对象,作为这个类各种数据的访问接口

#### 验证   
确保Class文件中的字节流是否符合当前Java虚拟机的要求

* 文件格式验证
* 元数据验证
* 字节码验证
* 符号引用验证

#### 准备

为类变量(static修饰的变量)分配内存并设置初始值

#### 解析

解析类中的字段和方法

#### 初始化

--------

## 虚拟机字节码执行

栈帧是Java虚拟机进行方法调用和方法执行的数据结构,是虚拟机运行时数据区中虚拟机栈的栈元素.方法调用的过程就是栈帧在虚拟机栈中从入栈道出栈的过程.

--------

## Java内存模型

Java的内存模型包括主内存和工作内存    
主内存是虚拟机内存的一部分,存储所有的变量.每条线程都有自己的工作内存.线程的工作内存保存了用到的主内存中变量的副本.不能直接读写主内存中的变量,不同线程之间也不能直接访问对方工作内存中的变量.    
线程中变量值的传递需要通过主内存来完成

### 内存间的交互操作

变量的交互操作指令

* lock
* unlock
* read
* load
* use
* assgin
* store
* write

特性

* 原子性 交互操作指令粒度无法再拆分
* 可见性 一个线程修改了共享变量的值,其他的线程能够立即得知这个修改
* 有序性 线程内所有的指令执行都是有序的,线程之间的指令操作是乱序的.使用synchronize指令保证线程之间的操作是有序的,原理是通过给变量加互斥锁,同一时间只能有一个线程操作变量.先加锁操作完成之后解锁等待下一个线程使用.

## 线程

线程和进程的区别

### 线程调度

线程调度的两种方法

* 协同式线程调度
线程执行时间由线程本身控制，线程的工作执行完毕之后通知系统进行线程切换，线程执行时间不可控制，如果线程阻塞的话会导致系统崩溃。

* 抢占式线程调度
系统分配线程的执行时间，线程的切换由系统控制。不会导致阻塞问题。

### 线程的状态

* 新建（New）
* 运行（Runnable）
* 无限期等待（Waitting）
* 限期等待（Timed Waitting）
* 阻塞（Blocked）
* 结束（Terminated）

### 线程安全

多个线程之间需要共享数据的情况下才需要考虑线程安全.

实现线程安全的方式

* 使用synchronized关键字    
* 基本数据类型使用final关键字修饰保证不被修改    
* 使用java.util.concurrent.atomic包里面AtomicBoolean、AtomicInteger、AtomicLong、AtomicReference这些原子类      

### java.util.concurrent.atomic包的使用

Atomic包的作用：    
方便程序员在多线程环境下，无锁的进行原子操作

Atomic包核心：    
Atomic包里的类基本都是使用Unsafe实现的包装类，核心操作是CAS原子操作

CAS(compare and swap)比较和替换技术，将预期值与当前变量的值比较(compare)，如果相等则使用新值替换(swap)当前变量，否则不作操作

使用示例

        package cn.com.example.concurrent.atomic;
        import java.util.concurrent.atomic.AtomicInteger;
        
        public class AtomicIntegerTest extends Thread {
                private AtomicInteger atomicInteger;
                
                public AtomicIntegerTest(AtomicInteger atomicInteger) {
                        this.atomicInteger = atomicInteger;
                }
                
                @Override
                public void run() {
                        int i = atomicInteger.incrementAndGet();
                        System.out.println("generated  out number:" + i);
                }
                
                public static void main(String[] args) {
                        AtomicInteger counter = new AtomicInteger();
                        for (int i = 0; i < 10; i++) {//10个线程
                        new AtomicIntegerTest(counter).start();
                        }
                }
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

### Socket通信的过程


