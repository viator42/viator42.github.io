---
layout: post
title:  "Flutter，Dart学习笔记"
date:   "2019-6-11"
categories: Flutter, Dart, Android
---

## 变量和基本数据类型

#### 变量定义

    var name = '张三';

#### 常量定义

final，const都可以表示常量，const为编译时常量，final只能被设置一次     

#### 数据类型

类型 | 说明 | 使用
---|---|---
int | 整型 | var a = 123
double | 浮点型 | var b = 123.0;
boolean | 布尔型 | var isDone = true
string | 字符串 | var name = 'abcd'
list | 列表 | var list = [1, 2, 3, 4, 5]
map | 键值对 | var map = {'id': 'sn123', 'name': 'zhangsan'}

--------

## 函数

函数定义

    String getUserInfo(String name, [String form], [String sex = 'male']) {
        ...
        return result;
    }

方括号其中的是可选参数，可选参数可以指定默认值

main函数

    void main => runApp(MyApp())

运算符

三元运算符： condition条件为真，返回expr1，否则返回expr2

    condition ? expr1 : expr2

第二种??表达式：expr1非空返回其值，否则返回expr2

    expr1 ?? expr2

级联操作

    级联操作用两个点(..)表示，对同一个对象做一系列操作

--------

## State状态

* initState    
当插入渲染树的时候调用，这个函数在生命周期中只调用一次。这里可以做一些初始化工作，比如初始化State的变量。    

* didChangeDependencies    
初始化时，在initState()之后立刻调用    

* build
初始化后绘制界面的时候调用

* didUpdateWidget    
祖先节点rebuild widget时调用 .当组件的状态改变的时候就会调用didUpdateWidget.

* dispose
组件销毁的时候调用，这个函数一般会移除监听，清理环境。


--------

## 异步操作

一般使用async函数和await表达式实现异步操作

定义异步操作方法

    await readFile()

必须在一个使用了async关键字标记的函数中使用await表达式

    fileOperate() async {
        var file = await readFile();
    }

--------

## 网络访问

GET访问

    try {
      HttpClient httpClient = new HttpClient();
      HttpClientRequest request = await httpClient.getUrl(Uri.parse("https://www.v2ex.com/api/topics/hot.json"));

      request.headers.add("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:67.0) Gecko/20100101 Firefox/67.0");

      HttpClientResponse response = await request.close();

      List result = json.decode(await response.transform(utf8.decoder).join());
      print(result.toString());
      setState(() {
        _items = result;
      });

      httpClient.close();

    }catch(e) {
      e.toString();
    }

POST访问





