---
layout: post
title:  "Java设计模式"
date:   2017-10-30
categories: Java
---

## 设计模式

整理自《设计模式：Java语言中的应用》

### Iteator模式

聚合接口，容器类继承这个接口，iterator方法用于生成迭代器    

        public interface Aggregate() {
                public abstract Iterator iterator()
        }

 迭代器接口，迭代器方法包含是否有下一个元素的hasNext方法和访问元素的next方法    

        public interface Iterator() {
                hasNext();
                next();
        }

定义迭代器类implements Iterator接口,实现迭代方法    
需要聚合的类implements Aggregate接口新建一个迭代器   
聚合类调用iterator方法新建迭代器,while循环访问元素

--------

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

--------

### Template Method模板模式

在父类中处理流程的框架,子类中实现具体处理.

Template Method是带有模板功能的模式,模板类定义成抽象类,包括模板的处理流程和处理方法.处理方法定义成抽象的由子类来实现.只要在不同的子类中实现不同的具体处理,父类的模板方法被调用的时候程序行为也会有所不同.不论子类的实现类如何,流程都会按照父类定义的那样进行.

        public abstrac class Processor {
                //具体的实现方法，需要子类来实现
                public abstract void step1();
                public abstract void step2();
                public abstract void step3();
                //定义处理流程
                public final void process {
                        step1();
                        step2();
                        step3();
                }
        }

--------

### Factory Method工厂方法模式

Template Method方式构建实例的工厂.父类定义生成实例的方法但不决定具体要生成的类,具体的处理全部交给子类来负责.这样可以将生成实例的框架和负责生成实例的类解耦.

        //抽象工厂类
        public sbstract class Factory {
                //构建对象类
                public final Product create(String param) {
                        Product p = createProduct(param);
                        registerProduct(p);
                        return p;
                }
                //需要子类实现的方法
                protected abstract Product createProduct(param);
                protected abstract void createProduct(Product);
        }
        
        //具体的工厂类
        public class Productfactory extends Factory {
                protected abstract Product createProduct(param){}
                protected abstract void createProduct(Product){}
        }

        //工厂类创建目标对象
        Factory factory = new Productfactory();
        factory.create("aaa");

--------

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

--------

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

--------

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

        //具体实现的工厂类
        public class BuildFactory extends Factory {
                public Part1 buildPart1(){
                        return new Part1();
                }
                public Part2 buildPart2(){
                        return new Part2();
                }
                public Part3 buildPart3(){
                        return new Part3();
                }
        }

使用

        Factory factory = Factory.getFactory("BuildFactory");
        factory.buildPart1();
        factory.buildPart2();
        factory.buildPart3();

--------

### Observer 观察者模式

分为Observer观察者和Observable被观察者.观察者定义update方法在被通知的时候调用.被观察者保存观察者的列表.定义addObserver和RemoveObserver方法管理观察者.被观察者通过遍历观察者列表调用update方法给观察者发送通知
        
        //观察者
        public class Observer {
                public void update();
        }

        public class Observable {
                //被观察者维护观察者列表
                List<Observer> observers;

                //被观察者通知观察者
                public void notifyObservers() {
                        for(Observer observer: observers) {
                                observer.update();
                        }
                }
        }

JDK中java.util.Observer有具体的实现    
RxJava也大量使用了观察者模式    

--------

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

