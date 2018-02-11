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

