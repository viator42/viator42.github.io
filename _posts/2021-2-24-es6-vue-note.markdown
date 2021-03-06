---
layout: post
title:  "ES6 Vue学习笔记"
date:   2021-02-24
categories: android
---

# ECMAScript基础

## 定义变量

var定义的是全局变量,定义之后所有的地方都能引用.let声明的是局部变量,只在代码块范围内有效

var命令会发生”变量提升“现象，即变量可以在声明之前使用，值为undefined。let声明的变量一定要在声明后使用，否则报错。

const声明一个只读的常量。一旦声明，常量的值就不能改变。const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。

## 变量赋值

直接赋值

    let a = 1;

或者从数组中提取值,赋值给变量

    let [a, b, c] = [1, 2, 3];

可以对对象进行解构赋值

    let { bar, foo } = { foo: "aaa", bar: "bbb" };
    foo // "aaa"
    bar // "bbb"

变量的解构赋值用途

交换变量的值

    let x = 1;
    let y = 2;

    [x, y] = [y, x];

从函数返回多个值

    // 返回一个数组
    function example() {
    return [1, 2, 3];
    }
    let [a, b, c] = example();

    // 返回一个对象
    function example() {
    return {
        foo: 1,
        bar: 2
    };
    }
    let { foo, bar } = example();

遍历Map结构

    const map = new Map();
    map.set('first', 'hello');
    map.set('second', 'world');

    for (let [key, value] of map) {
    console.log(key + " is " + value);
    }

--------

## Set

类似于数组，但是成员的值都是唯一的，没有重复的值。

    const s = new Set();

操作方法

* add(value)：添加某个值，返回 Set 结构本身。
* delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
* has(value)：返回一个布尔值，表示该值是否为Set的成员。
* clear()：清除所有成员，没有返回值。

遍历操作

* keys()：返回键名的遍历器
* values()：返回键值的遍历器
* entries()：返回键值对的遍历器
* forEach()：使用回调函数遍历每个成员

    for (let item of set.keys()) {
    console.log(item);
    }

转换成数组

    let arr = Array.from(thisMap);

---------

## Map

遍历方法

Map 结构原生提供三个遍历器生成函数和一个遍历方法。

* keys()：返回键名的遍历器。
* values()：返回键值的遍历器。
* entries()：返回所有成员的遍历器。
* forEach()：遍历 Map 的所有成员。

forEach遍历

    map.forEach(function(value, key, map) {
        console.log("Key: %s, Value: %s", key, value);
    });

--------

## Proxy

Proxy用于对对象的操作进行拦截,修改默认行为

    var proxy = new Proxy(target, handler);

target:     目标对象
handler:    对象的操作函数

__handler对象__

定义需要被拦截的操作方法

    var handler = {
        get: function(target, name) {
            return target[name];
        },
        ...
    };


get方法,用于拦截对象属性的读取,属性分别是目标对象,属性名

    var person = {
        name: "张三"
    };

    var proxy = new Proxy(person, {
    get: function(target, property) {
        return target[property];
    }
    });

    proxy.name // "张三"

set方法用来拦截某个属性的赋值操作,参数依次为目标对象、属性名、属性值

    set: function(obj, prop, value) {
        if (prop === 'age') {
            return;
        }
        if (value > 200) {
            return;
        }
        obj[prop] = value;  //赋值操作
    }

apply方法拦截函数的调用

construct方法用于拦截new命令

--------

## Promise 对象

Promise是用来完成异步访问的方案.传入操作成功,失败的回调方法.异步操作结束之后进行调用

    const promise = new Promise(function(resolve, reject) {
    // ... some code

    // 根据操作是否成功调用成功或者失败的回调方法
    if (statement){
        resolve(value);
    } else {
        reject(error);
    }
    });

    // 指定操作完成后的回调方法(成功或者失败)
    promise.then(function(value) {
        // success
    }, function(error) {
        // failure
    });

Promise新建完成后就会立即执行,然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行

下面是一个用Promise对象实现的 Ajax 操作的例子    
function返回了一个promise对象,返回值之后链式调用then方法    

    const getJSON = function(url) {
        const promise = new Promise(function(resolve, reject){
            const handler = function() {
                if (this.readyState !== 4) {
                    return;
                }
                if (this.status === 200) {
                    resolve(this.response);
                } else {
                    reject(new Error(this.statusText));
                }
            };
            const client = new XMLHttpRequest();
            client.open("GET", url);
            client.onreadystatechange = handler;
            client.responseType = "json";
            client.setRequestHeader("Accept", "application/json");
            client.send();
        });

    return promise;
    };

    getJSON("/posts.json").then(function(json) {
        console.log('Contents: ' + json);
    }, function(error) {
        console.error('出错了', error);
    });

--------

## 类和对象

类使用class关键字

    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }

        toString() {
            return '(' + this.x + ', ' + this.y + ')';
        }
    }

构造方法的名称为constructor。

实例化类使用new关键字

    var point = new Point(123, 456);

取值函数（getter）和存值函数（setter）

    class MyClass {
        constructor() {
            // ...
        }
        get prop() {
            return 'getter';
        }
        set prop(value) {
            console.log('setter: '+value);
        }
    }

    let inst = new MyClass();

    inst.prop = 123;
    // setter: 123

    inst.prop
    // 'getter'

使用get和set方法重写类对象的赋值和取值操作。

Class 可以通过extends关键字实现继承。

    class Point {
    }

    class ColorPoint extends Point {
        constructor(x, y, color) {
            super(x, y); // 调用父类的constructor(x, y)
            this.color = color;
        }

        toString() {
            return this.color + ' ' + super.toString(); // 调用父类的toString()
        }
    }



--------

## 箭头函数

函数的定义就是用一个箭头

    x => x * x;

或者写成

    x => {
        return x * x;
    }

就相当于

    function (x) {
        return x * x;
    };

## import,export引用

utils.js

    export const param1 = "Test Param from utils";
    export function plus(x, y) {
        return x + y;
    }

App.js

    import * as utils from './utils.js'
    console.log('utils.add:', utils.plus(123, 456));
    console.log('utils.param:', utils.param1);


箭头左边是参数, 右边是单行的表达式,表达式的结果作为函数返回值.
如果是多行的话用{}包裹函数体并用return返回结果

--------

