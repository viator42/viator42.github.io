---
layout: post
title:  "Kotlin学习笔记"
date:   "2019-1-16"
categories: Android Kotlin
---

## 基本数据类型

类型 | 位宽度
---|---
Double | 64    
Float | 32     
Long | 64    
Int | 32    
Short | 16    
Byte | 8    

类型的转换方法

> toByte():     Byte    
> toShort():    Short    
> toInt():      Int    
> toLong():     Long    
> toFloat():    Float    
> toDouble():   Double    
> toChar():     Char    

## 定义变量常量

    var <标识符> : <类型> = <初始化值>  //可变变量定义
    val <标识符> : <类型> = <初始化值>  //不可变常量定义，相当于java中的final

## 可空类型

    var a:String?   //在类型后面加个？表示可以为空

安全调用运算符，可以在调用方法之前先判断是否为空

    s?.toUpperCase()等同于if(s != null) s.toUpperCase() else null

__?:__ Elvis运算符，null合并运算符

    val result = param ?: ""    //param为null的时候result赋值为""




--------

## 字符串

    var str: String = "This is String"
    //多行字符串
    var str1: String = '''
        This is String'''


### 字符串模板

$ 表示一个变量名或者变量值

$varName 表示变量值    
${varName.fun()} 表示变量的方法返回值:    

## 函数

    fun sum(a: Int, b: Int): Int {   // Int 参数，返回值 Int
        return a + b
    }

    //表达式作为函数体，返回类型自动推断：
    fun sum(a: Int, b: Int) = a + b

无返回值的函数(类似Java中的void)：

    fun printSum(a: Int, b: Int): Unit { 
        print(a + b)
    }

    // 如果是返回 Unit类型，则可以省略(对于public方法也是这样)：
    public fun printSum(a: Int, b: Int) { 
        print(a + b)
    }

可变长参数函数

函数的变长参数可以用 vararg 关键字进行标识，参数作为数组

    fun vars(vararg v:Int){
        for(vt in v){
            print(vt)
        }
    }

    // 测试
    fun main(args: Array<String>) {
        vars(1,2,3,4,5)  // 输出12345
    }

lambda(匿名函数)

    val sumLambda: (Int, Int) -> Int = {x,y -> x+y}
    println(sumLambda(1,2))  // 输出 3

## NULL检查机制

Kotlin的空安全设计对于声明可为空的参数，在使用时要进行空判断处理，有两种处理方式，字段后加!!像Java一样抛出空异常，另一种字段后加?可不做处理返回值为 null或配合?:做空判断处理

    //类型后面加?表示可为空
    var age: String? = "23" 
    //抛出空指针异常
    val ages = age!!.toInt()
    //不做处理返回 null
    val ages1 = age?.toInt()
    //age为空返回-1
    val ages2 = age?.toInt() ?: -1

## 声明导入包

    package com.runoob.main
    import java.util.*

## 控制语句

if语句的判断结果可以作为表达式返回

    val max = if (a > b) a else b

### 循环

迭代器迭代循环

    for (item in collection) {
    }

区间循环

使用in运算符来检测某个数字是否在指定区间内，区间格式为 x..y ：

    if (x in 1..8) {
        println(x)
    }

### When表达式

条件判断，相当于Java中的switch语句

    when (x) {
        1 -> print("x == 1")
        2 -> print("x == 2")
        3, 4 -> print("x == 0 or x == 1")   //同时满足多个条件的情况
        in 1..10 -> print("x is in the range")  //检测在或者不在一个区间中
        in validNumbers -> print("x is valid")
        !in 10..20 -> print("x is outside the range")
        else -> { // 注意这个块
            print("x 不是 1 ，也不是 2")
        }
    }

## 类和对象

    class Runoob {
        var name: String = 'aaa'    //定义属性
        fun foo() { print("Foo") } // 定义方法
    }

类可以有一个默认的构造方法，位于类名称之后

    class Person constructor(firstName: String) {}

getter和setter方法

    var no: Int = 100
        get() = field                // 后端变量
        set(value) {
            field = value
        }

类中可以定义初始化代码，init关键字作为前缀

    init {
            println("FirstName is $firstName")
        }

类也可以有次级构造方法，

class Person { 
    constructor(parent: Person) {
        parent.children.add(this) 
    }
}

构造方法和init语句块进行类的属性赋值

    class Computer(val _cpu: String, val _ram: String, val _storage: String) {
    init {
        cpu = _cpu
        ram = _ram
        storage = _storage
    }

--------

## 类的继承

如果一个类要被继承，需要使用open关键字进行修饰

    // 基类
    open class Base(p: Int)           
    // 子类
    class Derived(p: Int) : Base(p)

### 构造方法

如果子类有主构造函数， 则基类必须在主构造函数中立即初始化。

    open class BaseClass1(_param1: String, _param2: String) {
    }

    class Extended1(_param1: String, _param2: String): BaseClass2(_param1, _param2) {
    }

子类没有主构造函数的时候，子类的constructor函数必须先初始化基类的构造函数

    open class BaseClass2 {
        constructor(_param1: String, _param2: String)
    }

    class Extended2: BaseClass2 {
        constructor(_param1: String, _param2: String, _param3: String) : super(_param1, _param2) {
        }
    }

在基类中，使用fun声明函数时，此函数默认为final修饰，不能被子类重写。如果允许子类重写该函数，那么就要手动添加 open 修饰它, 子类重写方法使用 override 关键词，子类重写父类属性的时候也需要加override

--------

## 接口

接口定义

    interface TestInterface {
        var v1: String  //接口中的属性只能是抽象的，不允许初始化值，接口不会保存属性值，实现接口时，必须重写属性
        fun fun1()
        fun fun2() = return a   //接口中的方法可以有默认实现
    }

接口继承

    class TestInterfaceedClass : TestInterface{
        override var v1: String = "value1"
        override fun fun1() {
            println("implemented method")
        }
    }

## 数据类

    data class Client(val name: String, val age:Int)

--------

## Kotlin Android 扩展 

不需要findViewById()绑定view。导入activity的xml就可以直接使用

    import kotlinx.android.synthetic.main.activity_main.*
    import kotlinx.android.synthetic.main.content_main.*

Model类序列化Parcelize

    import kotlinx.android.parcel.Parcelize

    @Parcelize
    class User(val firstName: String, val lastName: String, val age: Int): Parcelable

## 集合类

### List

    var list = ArrayList<String>() //定义
    list.add(item) //添加
    list.removeAt(i) //删除指定的项目
    list.get(i) //获取指定的项目
    //List遍历
    var list = ArrayList<String>()

    list.add("111")
    list.add("222")
    list.add("333")
    list.add("444")
    list.add("555")

    list.set(0, "000")
    list.removeAt(2)

    for (i in list) {
        println(i)
    }

### Map

    //创建map（不可修改
    val map = mapOf<String, Any>("id" to 1, "name" to "张三", "age" to 18, "address" to "hello street")

    //获取值
    val name = map["name"]
    println(name)

    println(map.get("name"))

    //遍历
    map.forEach {
        key, value -> println("$key : ${value.toString()}")
    }

    //创建可修改的map
    val mutableMap = mutableMapOf<String, Any>("id" to 1, "name" to "张三", "age" to 18, "address" to "hello street")

    //修改value
    mutableMap["name"] = "李四"
    //删除值
    mutableMap.remove("address")
    //添加值
    mutableMap["rank"] = "Level 1"

    mutableMap.forEach {
        key, value -> println("$key : ${value.toString()}")
    }

### Set

    //创建set（不可修改
    val set = setOf<Int>(1, 2, 3, 4, 5, 6, 7, 8)

    //遍历
    for (i in set) {
        print("${i.toString()} ")
    }
    
    //创建可修改的set
    val mutableSet = mutableSetOf<Any>(1, 2, "3", 4, 5, "6")
    
    //添加元素
    mutableSet.add(7)
    //删除元素
    mutableSet.remove(1)

    for (i in mutableSet) {
        print("${i.toString()} ")
    }

--------

## Lambda表达式

Lambda表达式语法

    参数列表 -> 函数体
    {x: Int, y: Int -> x + y}

表达式始终在花括号内    

可以把Lambda表达式存储在变量内，直接调用

    var sum = {x: Int, y: Int -> x + y}
    println(sum(1, 2))

只有一个参数的情况下，可以忽略参数部分直接写方法体↓

    var list = ArrayList<Int>()
        list.add(1)
        list.add(2)
        list.add(3)

        list.forEach {
            println(it)
        }

--------

# Kotlin Android相关

## Kotlin Android Extensions

绑定视图功能

content_main.xml

    <LinearLayout
            android:layout_marginTop="@dimen/margin_standard"
            android:layout_centerHorizontal="true"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/tomatoView">
        <Button
                android:text="@string/start"
                android:id="@+id/start"
                android:layout_margin="@dimen/margin_standard"
                android:background="@color/colorPrimary"
                android:textColor="@color/textWhite"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>
        <Button
                android:text="@string/stop"
                android:layout_margin="@dimen/margin_standard"
                android:background="@color/colorPrimary"
                android:textColor="@color/textWhite"
                android:id="@+id/stop"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>
    </LinearLayout>

MainActivity.kt

    import kotlinx.android.synthetic.main.content_main.*

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        。。。

        //可以在代码中直接使用view，不再需要findViewById
        start.setOnClickListener(object : View.OnClickListener {
            override fun onClick(v: View?) {
                。。。
            }
        })

        stop.setOnClickListener(object : View.OnClickListener {
            override fun onClick(v: View?) {
                。。。
            }
        })

    }




