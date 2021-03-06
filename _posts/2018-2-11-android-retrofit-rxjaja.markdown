---
layout: post
title:  "Retrofit RxJava学习笔记"
date:   2018-2-11
categories: Java Android
---

# Retrofit

Retrofit 是一个 RESTful 的 HTTP 网络请求框架的封装，基于okHttp，通过注解进行网络请求参数的配置

其他网络请求开源库    

Android-Async-Http 已经弃用
Volley    
OkHttp    

## 配置

AndroidManifest.xml添加网络访问权限

    <uses-permission android:name="android.permission.INTERNET" />

buildGradle,需要加载retrofit, converter-gson, gson, okhttp, okio

    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    compile 'com.squareup.retrofit2:converter-gson:2.3.0'
    compile 'com.google.code.gson:gson:2.8.2'
    compile files('libs/okhttp-3.9.1.jar')
    compile files('libs/okio-1.13.0.jar')

## 使用方法

__创建Retrofit对象__

需要设置数据解析器

    Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(StaticValues.SERVER_PATH)  //服务器地址
        .addConverterFactory(GsonConverterFactory.create())
        .build();

__定义接口文件__

一组网络操作可以放到一个接口文件中,每个方法都是一个网络操作

    public interface MyGetActionInterfaces {

        //get请求 使用参数拼接url
        @GET("main/test/get/{param1}/{param2}")
        Call<GetTesterRestult> getTest(
                @Path("param1") String param1,
                @Path("param2") String param2
        );

        //post请求,方法参数作为表单值
        @POST("main/test/post/")
        @FormUrlEncoded
        Call<List<User>> postTest(@Field("params") String postParams);

        //POJO类作为请求参数
        @POST("users/new")
        Call<User> createUser(@Body User user);

    }

Retrofit大量使用@注解来定义属性
GET把参数拼接到url中,POST使用表单

__网络访问__

GET请求

    public class GetTesterRestult {
        public String success;
        public String msg;
        public String data;
    }

    MyGetActionInterfaces myGetActionInterfaces = retrofit.create(MyGetActionInterfaces.class);
    Call<GetTesterRestult> call = myGetActionInterfaces.getTest("123", "456");
    try {
        GetTesterRestult getTesterRestult = call.execute().body();
        Log.v("RetrofitTester", getTesterRestult.success);
        Log.v("RetrofitTester", getTesterRestult.msg);
        Log.v("RetrofitTester", getTesterRestult.data);
    } catch (IOException e) {
        e.printStackTrace();
        Log.v("RetrofitTester", "error");
    }

POST请求

    PostParams postParams = new PostParams();
    postParams.param1 = "123456";
    postParams.param2 = "Param2 here";

    Gson gson = new Gson();
    String paramStr = gson.toJson(postParams);

构建参数

    Call<List<User>> call = myGetActionInterfaces.postTest(paramStr);
    try {
        ArrayList<User> userList = (ArrayList<User>) call.execute().body();

    } catch (IOException e) {
        e.printStackTrace();
        Log.v("RetrofitTester", "error");
    }

返回值直接从json格式映射成对象

## 源码解析

__具体请求过程__

* 通过解析网络请求接口的注解，配置网络请求参数

* 通过动态代理生成网络请求对象

* 通过网络请求适配器将网络请求对象进行平台适配

> 平台包括：Android、Rxjava、Guava和java8

* 通过网络请求执行器发送网络请求
* 通过数据转换器解析服务器返回的数据
* 通过回调执行器切换线程（子线程 ->>主线程）
* 用户在主线程处理返回结果

参考：https://www.jianshu.com/p/0c055ad46b6c

--------

# RxJava 笔记

### 概念

RxJava是一种响应式编程框架.响应式编程是一种基于异步数据流概念的编程模式。当数据到达的时候，消费者做出响应。响应式编程可以将事件传递给注册了的observer。    
RxJava本质上是一个异步操作库，是一个能让你用极其简洁的逻辑去处理繁琐复杂任务的异步事件库。    
Android平台上为已经开发者提供了AsyncTask,Handler等用来做异步操作的类库，那我们为什么还要选择RxJava呢？答案是简洁！RxJava可以用非常简洁的代码逻辑来解决复杂问题；而且即使业务逻辑的越来越复杂，它依然能够保持简洁    
RxJava主要使用的是观察者模式    

* 创建：Rx可以方便的创建事件流和数据流
* 组合：Rx使用查询式的操作符组合和变换数据流
* 监听：Rx可以订阅任何可观察的数据流并执行操作

### RxJava的基本原理

在RxJava，Observable相当于被观察者，它是事件的源头，而OnSubscribe则是定义数据源如何发送事件，或者如何发送什么样的数据；Subscriber则是观察者（在代码实现上，Subscriber实现了接口Observer），定义了接收数据后对应的反应。observable.subscribe(subscriber)将两者进行了关联：即告诉Observable，它有一个Subscriber；同时触发OnSubscribe.onCall()，开启整个事件流。    
观察者模式一般是观察者注册到被观察者     
RxJava中是被观察者注册观察者    

RxJava的原理就是创建一个Observable对象来干活，然后使用各种操作符建立起来的链式操作，就如同流水线一样，把你想要处理的数据一步一步地加工
成你想要的成品，然后发射给Subscriber处理。

### 导入

    implementation 'io.reactivex.rxjava2:rxjava:2.1.1'
    implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'

###  RxJava基本实现

RxJava的基本用法分为如下3个步骤。

1.创建Observer（观察者）    
它决定事件触发的时候将有怎样的行为    

    Observer<String> observer = new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {
                Log.v("RxJavaTester", "onSubscribe");
            }

            @Override
            public void onNext(String s) {
                Log.v("RxJavaTester", s + " onNext");
            }

            @Override
            public void onError(Throwable e) {
                Log.v("RxJavaTester", "onError");
            }

            @Override
            public void onComplete() {
                Log.v("RxJavaTester", "onComplete");
            }
        };

2.创建 Observable（被观察者）    
它决定什么时候触发事件以及触发怎样的事件。RxJava 使用 create 方法来创建一个Observable，并为它定义事件触发规则

    Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                e.onNext("111");
                e.onNext("222");
                e.onNext("333");
                e.onComplete();
            }
        })

3.Subscribe（订阅）   
    
    observable.subscribe(subscriber);


### Example

最简单的例子

    Observable.just("step1", "step2", "step3")
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Observer<String>() {
        @Override
        public void onSubscribe(@NonNull Disposable d) {

        }

        @Override
        public void onNext(@NonNull String s) {
            resultTextView.append(s);
        }

        @Override
        public void onError(@NonNull Throwable e) {

        }

        @Override
        public void onComplete() {
            resultTextView.append(" Complete");
        }
    });

### 使用流程

1.创建Obserable被观察者对象

使用Observable.just()将一个或者多个对象转换成使用Observable

    Observable.just("step1", "step2", "step3")

Observable.from()是从一个数据集中创建Observable对象

    Integer[] items = { 0, 1, 2, 3, 4, 5 };
    Observable myObservable = Observable.from(items);

Just类似于From，但是From会将数组或Iterable的数据取出然后逐个发射，而Just只是简单的
原样发射，将数组或Iterable当做单个数据。

Observable.create()是手动创建一个Observable对象

    Observable.create(new ObservableOnSubscribe<Integer>() {
        @Override
        public void subscribe(@NonNull ObservableEmitter<Integer> e) throws Exception {
            e.onNext(1);
            e.onNext(2);
            e.onNext(3);
        }
    })

subscribe方法中定义需要执行的操作序列(向操作序列中发射数据)


2.指定调度器

    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())

subscribeOn() 指定Observable自身在哪个调度器上执行
observeOn() 指定一个观察者在哪个调度器上观察这个Observable

RxJava提供了5种调度器：

1. .io()    
用于IO密集型任务，如异步阻塞IO操作，这个调度器的线
程池会根据需要增长；对于普通的计算任务，请使用
Schedulers.computation()；Schedulers.io( )默认是一个
CachedThreadScheduler，很像一个有线程缓存的新线程
调度器

2. .computation()    
用于计算任务，如事件循环或和回调处理，不要用于IO操
作(IO操作请使用Schedulers.io())；默认线程数等于处理器
的数量

3. .immediate()    
在当前线程立即开始执行任务

4. .newThread()    
为每个任务创建一个新线程

5. .trampoline()    
当其它排队的任务完成后，在当前线程排队开始执行

3.定义Observer

    .subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(@NonNull Disposable d) {

    }

    @Override
    public void onNext(@NonNull Integer integer) {
        resultTextView.append(String.valueOf(integer));
    }

    @Override
    public void onError(@NonNull Throwable e) {

    }

    @Override
    public void onComplete() {

    }
});

Subscribe方法用于将观察者连接到Observable，你的观察者需要实现以下方法的一个子集：

* onNext(T item)    
Observable调用这个方法发射数据，方法的参数就是Observable发射的数据，这个方法
可能会被调用多次，取决于你的实现。

* onError(Exception ex)    
当Observable遇到错误或者无法返回期望的数据时会调用这个方法，这个调用会终止
Observable，后续不会再调用onNext和onCompleted，onError方法的参数是抛出的异
常。

* onComplete    
正常终止，如果没有遇到错误，Observable在最后一次调用onNext之后调用此方法。
根据Observable协议的定义，onNext可能会被调用零次或者很多次，最后会有一次
onCompleted或onError调用（不会同时），传递数据给onNext通常被称作发射，
onCompleted和onError被称作通知。

### 转换Observables

__map方法__

    .map(new Function<Integer, String>() {
        @Override
        public String apply(@NonNull Integer integer) throws Exception {
            return String.valueOf(integer);
        }
    })

将一种数据类型转换成另一种Function定义的两个范型分别是传入返回的类型,apply方法中完成转换

__scan方法__

    .scan(new Func2<Integer, Integer, Integer>() {
        @Override
        public Integer call(Integer sum, Integer item) {
        return sum + item;
        }
    })

Scan 操作符对原始Observable发射的第一项数据应用一个函数，然后将那个函数的结果作为
自己的第一项数据发射。它将函数的结果同第二项数据一起填充给这个函数来产生它自己的
第二项数据。它持续进行这个过程来产生剩余的数据序列

__merge方法__

    Observable<Integer> odds = Observable.just(1, 3, 5).subscribeOn(someScheduler);
    Observable<Integer> evens = Observable.just(2, 4, 6);

    Observable.merge(odds, evens)

用来合并多个Observable

--------

# RxJava运行原理

## 实现一个简单的RxJava

Subsribler在RxJava里面是一个抽象类，它实现了Observer接口。

    public interface Observer<T> {
        void onCompleted();
        void onError(Throwable t);
        void onNext(T var1);
    }

    public abstract class Subscriber<T> implements Observer<T> {
        public void onStart() {
        }
    }

定义Observable被观察者

    public class Observable<T> {
        //OnSubscribe对象用来产生数据流
        final OnSubscribe<T> onSubscribe;

        //构造函数设为私有
        private Observable(OnSubscribe<T> onSubscribe) {
            this.onSubscribe = onSubscribe;
        }

        //创建Observable实例对象，参数为OnSubscribe对象
        public static <T> Observable<T> create(OnSubscribe<T> onSubscribe) {
            return new Observable<T>(onSubscribe);
        }

        //观察者并注入到被观察者中
        public void subscribe(Subscriber<? super T> subscriber) {
            subscriber.onStart();
            onSubscribe.call(subscriber);
        }

        /**
        * call方法是把数据注入到被观察者中，产生数据流
        * @param <T>
        */
        public interface OnSubscribe<T> {
            void call(Subscriber<? super T> subscriber);
        }
    }

使用自定义的RxJava

    //创建被观察者对象
    Observable.create(new Observable.OnSubscribe<Integer>() {
        @Override
        public void call(Subscriber<? super Integer> subscriber) {
            //生成数据序列，onNext是将数据发射到subscriber进行处理
            for (int i = 0; i < 10; i++) {
                System.out.println("OnSubscribe@ "+Thread.currentThread().getName());
                subscriber.onNext(i);
            }
        }
    })
        //生成观察者并注入到被观察者中，Subscriber观察者的作用是定义对数据做什么样的处理
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable t) {

            }

            @Override
            public void onNext(String var1) {
                System.out.println(var1);
            }
    });

这里的顺序实际是颠倒的，先定义执行顺序再定义处理方式

## 添加操作符

先执行操作符的call方法，然后再递归执行OnSubscribe的call方法。先执行操作符中的onNext方法，再执行subscriber的onNext方法。    
Transformer的call方法中进行数据的转换。

    public class Observable<T> {
        //OnSubscribe对象用来产生数据流
        final OnSubscribe<T> onSubscribe;

        //构造函数设为私有
        private Observable(OnSubscribe<T> onSubscribe) {
            this.onSubscribe = onSubscribe;
        }

        //创建Observable实例对象，参数为OnSubscribe对象
        public static <T> Observable<T> create(OnSubscribe<T> onSubscribe) {
            return new Observable<T>(onSubscribe);
        }

        //观察者并注入到被观察者中
        public void subscribe(Subscriber<? super T> subscriber) {
            subscriber.onStart();
            onSubscribe.call(subscriber);
        }

        /**
        * call方法是把数据注入到被观察者中，产生数据流
        * @param <T>
        */
        public interface OnSubscribe<T> {
            void call(Subscriber<? super T> subscriber);
        }

        //----------添加操作符--------
        public <R> Observable<R> map(Transformer<? super T, ? extends R> transformer) {
            return create(new OnSubscribe<R>() {
                @Override
                public void call(Subscriber<? super R> subscriber) {
                    Observable.this.subscribe(new Subscriber<T>() {
                        @Override
                        public void onCompleted() {
                            subscriber.onCompleted();
                        }

                        @Override
                        public void onError(Throwable t) {
                            subscriber.onError(t);
                        }

                        @Override
                        public void onNext(T var1) {
                            //调用transformer的call方法
                            subscriber.onNext(transformer.call(var1));
                        }
                    });
                }
            });
        }

        public interface Transformer<T, R> {
            R call(T from);
        }
    }

使用操作符

    //创建被观察者对象
    Observable.create(new Observable.OnSubscribe<Integer>() {
        @Override
        public void call(Subscriber<? super Integer> subscriber) {
            //生成数据并注入到被观察者
            for (int i = 0; i < 10; i++) {
                System.out.println("OnSubscribe@ "+Thread.currentThread().getName());
                subscriber.onNext(i);
            }
        }
    })
        .map(new Observable.Transformer<Integer, String>() {
                @Override
                public String call(Integer from) {
                    return "mapping" + String.valueOf(from);
                }
            })
            //生成观察者并注入到被观察者中
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable t) {

            }

            @Override
            public void onNext(String var1) {
                System.out.println(var1);
            }
    });

## 添加线程切换功能

利用subscribeOn()结合observeOn()来实现线程控制，让事件的产生和消费发生在不同的线程。observeOn()作用的是Subscriber，而subscribeOn()作用的是OnSubscribe。

RxJava完成线程的切换使用的是Scheduler，创建的参数是线程池的Executor

Scheduler.java

    public class Scheduler {
        final Executor executor;

        public Scheduler(Executor executor) {
            this.executor = executor;
        }

        //创建worker对象
        public Worker createWorker() {
            return new Worker(executor);
        }

        //Worker是Scheduler的内部类，它是具体任务的执行者。当要提交任务给Worker执行需要调用Worker的schedule(Action0 aciton)方法。
        public static class Worker {
            final Executor executor;

            public Worker(Executor executor) {
                this.executor = executor;
            }

            public void schedule(Runnable runnable) {
                executor.execute(runnable);
            }
        }
    }

要获得一个Scheduler并不需要我们去new，一般是调用Schedulers的工厂方法。

Schedulers.java

    public final class Schedulers {
        private static final Scheduler ioScheduler = new Scheduler(Executors.newSingleThreadExecutor());
        public static Scheduler io() {
            return ioScheduler;
        }
    }

Observable类中实现subscribeOn和observeOn

    public class Observable<T> {
        //OnSubscribe对象用来产生数据流
        final OnSubscribe<T> onSubscribe;

        //构造函数设为私有
        private Observable(OnSubscribe<T> onSubscribe) {
            this.onSubscribe = onSubscribe;
        }

        //创建Observable实例对象，参数为OnSubscribe对象
        public static <T> Observable<T> create(OnSubscribe<T> onSubscribe) {
            return new Observable<T>(onSubscribe);
        }

        //观察者并注入到被观察者中
        public void subscribe(Subscriber<? super T> subscriber) {
            subscriber.onStart();
            onSubscribe.call(subscriber);
        }

        /**
        * call方法是把数据注入到被观察者中，产生数据流
        * @param <T>
        */
        public interface OnSubscribe<T> {
            void call(Subscriber<? super T> subscriber);
        }

        //--------添加线程切换功能--------

        //实现subscribeOn
        //subscribeOn是作用于上层OnSubscribe的，可以让OnSubscribe的call方法在新线程中执行。
        public Observable<T> subscribeOn(Scheduler scheduler) {
            return Observable.create(new OnSubscribe<T>() {
                @Override
                public void call(Subscriber<? super T> subscriber) {
                    subscriber.onStart();
                    // 将事件的生产切换到新的线程。
                    scheduler.createWorker().schedule(new Runnable() {
                        @Override
                        public void run() {
                            Observable.this.onSubscribe.call(subscriber);
                        }
                    });
                }
            });
        }

        //实现observeOn
        //subscribeOn是作用于下层Subscriber的，需要让下层Subscriber的事件处理方法放到新线程中执行。
        public Observable<T> observeOn(Scheduler scheduler) {
            return Observable.create(new OnSubscribe<T>() {
                @Override
                public void call(Subscriber<? super T> subscriber) {
                    subscriber.onStart();
                    Scheduler.Worker worker = scheduler.createWorker();
                    Observable.this.onSubscribe.call(new Subscriber<T>() {
                        @Override
                        public void onCompleted() {
                            subscriber.onCompleted();
                        }

                        @Override
                        public void onError(Throwable t) {
                            subscriber.onError(t);
                        }

                        @Override
                        public void onNext(T var1) {
                            subscriber.onNext(var1);
                        }
                    });
                }
            });
        }
    }

使用线程切换

    Observable.create(new Observable.OnSubscribe<Integer>() {
        @Override
        public void call(Subscriber<? super Integer> subscriber) {
            //生成数据并注入到被观察者
            for (int i = 0; i < 10; i++) {
                System.out.println("OnSubscribe@ "+Thread.currentThread().getName());
                subscriber.onNext(i);
            }
        }
    })
        .subscribeOn(Schedulers.io())
        .observeOn(Schedulers.io())
        //生成观察者并注入到被观察者中
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable t) {

            }

            @Override
            public void onNext(String var1) {
                System.out.println(var1);
            }
        });

## Observables：Single、Completable、Maybe。

除了标准的Observable类以外还可以使用其他的类型的Observable。比如 Single、Completable、Maybe。

### Single
Single是用来发送单一的数据，只有 onSuccess 和 onError 事件。对数据进行异步操作的时候常用。

onSuccess()用于发射数据(在Observable/Flowable中使用onNext()来发射数据)。而且只能发射一个数据，后面即使再发射数据也不会做任何处理。

    Single.create(new SingleOnSubscribe<Address>() {
        @Override
        public void subscribe(@NonNull SingleEmitter<Address> emitter) throws Exception {
            emitter.onSuccess(address);
        }
    }).subscribeOn(Schedulers.newThread()) // "work" on io thread
    .observeOn(AndroidSchedulers.mainThread()) // "listen" on UIThread);
    .subscribe(new SingleObserver<Address>() {
        @Override
        public void onSubscribe(@NonNull Disposable d) {
        }

        @Override
        public void onSuccess(@NonNull Address address) {
            dismiss();
        }

        @Override
        public void onError(@NonNull Throwable e) {
            e.printStackTrace();
        }
    });

### Completable

Completable在创建后，不会发射任何数据。

### Maybe

Maybe 是 RxJava2.x 之后才有的新类型，可以看成是Single和Completable的结合。

Maybe创建之后，MaybeEmitter 和 SingleEmitter 一样并没有onNext()方法，同样需要通过onSuccess()方法来发射数据。

    Maybe.create(new MaybeOnSubscribe<String>() {
        @Override
        public void subscribe(@NonNull MaybeEmitter<String> e) throws Exception {
            e.onSuccess("testA");
        }
    }).subscribe(new Consumer<String>() {

        @Override
        public void accept(@NonNull String s) throws Exception {

            System.out.println("s="+s);
        }
    });

## Flowable背压策略

被观察者发送事件的速度大于观察者接收事件的速度时，观察者内会创建一个无限制大少的缓冲池存储未接收的事件，因此当存储的事件越来越多时就会导致OOM的出现。（注：当subscribeOn与observeOn不为同一个线程时，被观察者与观察者内存在不同时长耗时任务，就会使发送与接收速度存在差异。）

Flowable的例子

    Flowable.create(new FlowableOnSubscribe<Integer>() {
        @Override
        public void subscribe(FlowableEmitter<Integer> e) throws Exception {
            for(int j = 0;j<=150;j++){
                e.onNext(j);
                Log.i(TAG," 发送数据："+j);
                try{
                    Thread.sleep(50);
                }catch (Exception ex){
                }
            }
        }
    },BackpressureStrategy.ERROR)
    .subscribeOn(Schedulers.newThread())
    .observeOn(Schedulers.newThread())
    .subscribe(new Subscriber<Integer>() {
        @Override
        public void onSubscribe(Subscription s) {
            s.request(Long.MAX_VALUE); //观察者设置接收事件的数量,如果不设置接收不到事件
        }
        @Override
        public void onNext(Integer integer) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Log.e(TAG,"onNext : "+(integer));
        }
        @Override
        public void onError(Throwable t) {
            Log.e(TAG,"onError : "+t.toString());
        }
        @Override
        public void onComplete() {
            Log.e(TAG,"onComplete");
        }
    });

跟Observable的区别

* 被观察者使用FlowableOnSubscribe而不是ObservableOnSubscribe
* 观察者使用Subscriber
* 需要添加BackpressureStrategy策略类

BackpressureStrategy 背压策略

* ERROR    
    缓存池默认大少为：128，当被观察者发送事件大于128时，观察者抛出异常并终止接收事件，但不会影响被观察者继续发送事件。
* BUFFER    
    与Observable一样存在背压问题，但是接收性能比Observable低，因为BUFFER类型通过BufferAsyncEmitter添加了额外的逻辑处理，再发送至观察者。
* DROP    
    每当观察者接收128事件之后，就会丢弃部分事件。
* LATEST    
    LATEST与DROP使用效果一样，但LATEST会保证能接收最后一个事件，而DROP则不会保证。
* MISSING     
    MISSING就是没有采取背压策略的类型，效果跟Obserable一样。

request(int count)：设置接收事件的数量,如果不设置接收不到事件

--------

## Retrofit和RxJava结合

### 导入库

    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    compile 'com.squareup.retrofit2:converter-gson:2.3.0'
    compile 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'

    compile 'io.reactivex.rxjava2:rxandroid:2.0.1'
    compile 'io.reactivex.rxjava2:rxjava:2.1.8'

    compile 'com.google.code.gson:gson:2.8.2'
    compile files('libs/okhttp-3.9.1.jar')
    compile files('libs/okio-1.13.0.jar')

注意导入的compile 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'

### 使用

    public interface MyGetActionInterfaces {
        @POST("main/test/post/")
        @FormUrlEncoded
        Single<List<User>> rxjavaTest(@Field("params") String postParams);
    }

    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(StaticValues.SERVER_PATH)
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
            .build();

    PostParams postParams = new PostParams();
    postParams.param1 = "123456";
    postParams.param2 = "Param2 here";
    Gson gson = new Gson();
    String paramStr = gson.toJson(postParams);

以下有两种方法

方法1

    CompositeDisposable mCompositeDisposable = new CompositeDisposable();
    MyGetActionInterfaces myGetActionInterfaces = retrofit.create(MyGetActionInterfaces.class);
    mCompositeDisposable.add(myGetActionInterfaces.rxjavaTest(paramStr)
        .subscribeOn(Schedulers.newThread()) // "work" on io thread
        .observeOn(AndroidSchedulers.mainThread()) // "listen" on UIThread);
        .subscribe(new Consumer<List<User>>() {
            @Override
            public void accept(List<User> users) throws Exception {
                for (User user :users) {
                    Log.v("RetrofitTester", user.toString());
                }
            }
        }));

方法2

    MyGetActionInterfaces myGetActionInterfaces = retrofit.create(MyGetActionInterfaces.class);
    myGetActionInterfaces.rxjavaTest(paramStr)
        .subscribeOn(Schedulers.newThread()) // "work" on io thread
        .observeOn(AndroidSchedulers.mainThread()) // "listen" on UIThread);
        .subscribe(new SingleObserver<List<User>>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onSuccess(@NonNull List<User> users) {
                for (User user :users) {
                    Log.v("RetrofitTester", user.toString());
                }
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }
        });

--------

## Retrofit 使用缓存

    CacheControl.Builder cacheBuilder  = new CacheControl.Builder();
    cacheBuilder.maxAge(0, TimeUnit.SECONDS);//这个是控制缓存的最大生命时间
    cacheBuilder.maxStale(365,TimeUnit.DAYS);//这个是控制缓存的过时时间
    final CacheControl cacheControl = cacheBuilder.build();

    File cacheFile = new File(view.getContext().getCacheDir(), "ugou");
    Cache cache = new Cache(cacheFile, 1024 * 1024 * 100); //100Mb

    OkHttpClient okHttpClient = new OkHttpClient.Builder()
            .cache(cache)
            .addInterceptor(new Interceptor() {
                @Override
                public okhttp3.Response intercept(Chain chain) throws IOException {
                    Request.Builder requestBuilder = chain.request().newBuilder();
                    requestBuilder.cacheControl(cacheControl);

                    Request request = requestBuilder.build();
                    okhttp3.Response response = chain.proceed(request);
                    return response;
                }
            })
            .build();

    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(EnvValues.SERVER_PATH)
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
            .client(okHttpClient)
            .build();

    Gson gson = new Gson();
    String paramStr = gson.toJson(homeParam);

    CommonUtils.log(paramStr);

    MainpageActions mainpageActions = retrofit.create(MainpageActions.class);

    mainpageActions.home(paramStr)
            .subscribeOn(Schedulers.newThread()) // "work" on io thread
            .observeOn(AndroidSchedulers.mainThread()) // "listen" on UIThread);
            .subscribe(new Observer<Response<HomeResult>>() {
                @Override
                public void onSubscribe(Disposable d) {
                }

                @Override
                public void onNext(Response<HomeResult> homeResultResponse) {
                    HomeResult homeResult = homeResultResponse.body();
                    homeResult.aesKey = homeResultResponse.headers().get("Set-Cookie");
                    view.listHome(homeResult);
                }

                @Override
                public void onError(Throwable e) {

                }

                @Override
                public void onComplete() {

                }
            });
    
    --------