---
layout: post
title:  "Retrofit RxJava学习笔记"
date:   2018-2-11
categories: Java Android
---

## RxJava 笔记

__概念__

RxJava是一种响应式编程框架.响应式编程是一种基于异步数据流概念的编程模式。数据流就像一条河：它可以被观测，被过滤，被操作，或者为新的消费者与另外一条流合并为一条新的流。    
RxJava本质上是一个异步操作库，是一个能让你用极其简洁的逻辑去处理繁琐复杂任务的异步事件库。    
Android平台上为已经开发者提供了AsyncTask,Handler等用来做异步操作的类库，那我们为什么还要选择RxJava呢？答案是简洁！RxJava可以用非常简洁的代码逻辑来解决复杂问题；而且即使业务逻辑的越来越复杂，它依然能够保持简洁    
RxJava主要使用的是观察者模式    

__RxJava的基本原理__

在RxJava，Observable相当于被观察者，它是事件的源头，而OnSubscribe则是定义数据源如何发送事件，或者如何发送什么样的数据；Subscriber则是观察者（在代码实现上，Subscriber实现了接口Observer），定义了接收数据后对应的反应。observable.subscribe(subscriber)将两者进行了关联：即告诉Observable，它有一个Subscriber；同时触发OnSubscribe.onCall()，开启整个事件流。    
观察者模式一般是观察者注册到被观察者     
RxJava中是被观察者注册观察者    

__导入__

    compile 'io.reactivex.rxjava2:rxjava:2.1.1'
    compile 'io.reactivex.rxjava2:rxandroid:2.0.1'

__Example__

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

__使用流程__

1.创建Obserable被观察者对象

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

subscribeOn() 在指定的调度器上进行Observer的操作
observeOn() 方法将会在指定的调度器上返回结果

RxJava提供了5种调度器：

1. .io()
2. .computation()
3. .immediate()
4. .newThread()
5. .trampoline()

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

转换Observables
map方法

    .map(new Function<Integer, String>() {
        @Override
        public String apply(@NonNull Integer integer) throws Exception {
            return String.valueOf(integer);
        }
    })

将一种数据类型转换成另一种Function定义的两个范型分别是传入返回的类型,apply方法中完成转换

-------------------------------------------------------------------------------------

## Retrofit笔记

__配置__

AndroidManifest.xml添加网络访问权限

    <uses-permission android:name="android.permission.INTERNET" />

buildGradle,需要加载retrofit, converter-gson, gson, okhttp, okio

    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    compile 'com.squareup.retrofit2:converter-gson:2.3.0'
    compile 'com.google.code.gson:gson:2.8.2'
    compile files('libs/okhttp-3.9.1.jar')
    compile files('libs/okio-1.13.0.jar')

__使用方法__

1.创建Retrofit对象

需要设置数据解析器

    Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(StaticValues.SERVER_PATH)
        .addConverterFactory(GsonConverterFactory.create())
        .build();

2.定义接口文件

一组网络操作可以放到一个接口文件中,每个方法都是一个网络操作

    public interface MyGetActionInterfaces {
        @GET("main/test/get/{param1}/{param2}")
        Call<GetTesterRestult> getTest(
                @Path("param1") String param1,
                @Path("param2") String param2
        );

        @POST("main/test/post/")
        @FormUrlEncoded
        Call<List<User>> postTest(@Field("params") String postParams);

    }

Retrofit大量使用@注解来定义属性
GET把参数拼接到url中,POST使用表单

3.网络访问

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

--------------------------------------------------------------------------------------
## Retrofit和RxJava结合

导入库

    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    compile 'com.squareup.retrofit2:converter-gson:2.3.0'
    compile 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'

    compile 'io.reactivex.rxjava2:rxandroid:2.0.1'
    compile 'io.reactivex.rxjava2:rxjava:2.1.8'

    compile 'com.google.code.gson:gson:2.8.2'
    compile files('libs/okhttp-3.9.1.jar')
    compile files('libs/okio-1.13.0.jar')

注意导入的compile 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'

__使用__

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
