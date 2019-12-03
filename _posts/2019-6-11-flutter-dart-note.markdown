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

--------

## Provider

__导入包__

pubspec.yaml

    dependencies:
        provider: ^3.1.0

__创建model类__

这里需要混入ChangeNotifer，修改的时候还需要调用notifyListeners()通知刷新

    class Counter with ChangeNotifier {
        int _count;
        Counter(this._count);

        void add() {
            _count++;
            notifyListeners();
        }

        get count => _count;
    }

__外层页面__

创建共享的顶层数据，外层需要ChangeNotifierProvider包裹，builder()方法初始化数据对象，显示值的时候使用Provider.of()方法获取对象

    class ProviderExampleViewPage extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            // TODO: implement build
            return ChangeNotifierProvider<Counter> (
            builder: (context) => Counter(1),
            child: Scaffold(
                appBar: AppBar(
                title: Text('ProviderExampleViewPage'),
                ),

                body: Center(
                child: Column(
                    children: <Widget>[
                    Consumer<Counter>(
                        builder: (context, counter, _) => Text("${Provider.of<Counter>(context).count}"),
                    ),
                    ChangerWidget(),
                    ],
                ),
                ),
            ),
            );
        }
    }

__内层页面__

直接使用Privider.of()方法获取model对象，进行修改的时候就会在不同页面直接进行数据同步和刷新。

    class ChangerWidget extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            // TODO: implement build
            return RaisedButton(
            child: Text('Plus one'),
                onPressed: () {
                    var counter = Provider.of<Counter>(context);
                    counter.add();
                },
            );
        }
    }

Provider适用于在不同的Widget之间进行数据同步，同一个Widget中不起作用，widget内部的状态改变还是Stateful widget。

当有多个Provider的时候使用MultiPrivoder

    MultiProvider(
        providers: [
            Provider<Foo>.value(value: foo),
            Provider<Bar>.value(value: bar),
            Provider<Baz>.value(value: baz),
        ],
        child: someWidget,
    )


