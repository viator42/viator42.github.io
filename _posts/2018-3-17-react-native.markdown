---
layout: post
title:  "React Native学习笔记"
date:   2018-03-17
categories: android
---

## 环境配置
安装nodejs

    npm install -g create-react-native-app

    create-react-native-app AwesomeProject
    cd AwesomeProject
    npm start

构建完成后会生成一个链接

https://expo.io/    
在这个网站下载App,扫二维码查看运行的项目

## 文件结构
App.js为入口文件

App.js

    import React from 'react';
    import { StyleSheet, Text, View, Image } from 'react-native';   //导入组件

    export default class App extends React.Component {
    
    //这个方法负责页面渲染
    render() {
        let pic = {
        uri: 'http://imgsrc.baidu.com/baike/pic/item/e850352ac65c1038bcba9c0eb9119313b17e8932.jpg'
        };
        return (
        <View style={styles.container}>
            <Text>This is first RN App</Text>
            <Image source={pic} style={{width: 193, height: 110}} />
        </View>
        
        );
    }
    }

    //定义样式
    const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center',
    },
    });

## 指定宽高
    
    <View style={{width: 50, height: 50}} />

## Flex弹性宽高

类似Android中的weight属性,代表占空间的权重比,数字越大占的相对空间越大,总和是父元素的尺寸

    <View style={{flex: 1}}>
        <View style={{flex: 1, backgroundColor: 'powderblue'}} />
        <View style={{flex: 2, backgroundColor: 'skyblue'}} />
        <View style={{flex: 3, backgroundColor: 'steelblue'}} />
    </View>

## 屏幕布局结构

Flex Direction

在组件的style中指定flexDirection可以决定布局的主轴。子元素是应该沿着水平轴(row)方向排列，还是沿着竖直轴(column)方向排列呢？默认值是竖直轴(column)方向。

    View style={{flex: 1, flexDirection: 'row'}}>

Justify Content

在组件的style中指定justifyContent可以决定其子元素沿着主轴的排列方式。子元素是应该靠近主轴的起始端还是末尾段分布呢？亦或应该均匀分布？对应的这些可选项有：flex-start、center、flex-end、space-around以及space-between。

Align Items

在组件的style中指定alignItems可以决定其子元素沿着次轴（与主轴垂直的轴，比如若主轴方向为row，则次轴方向为column）的排列方式。子元素是应该靠近次轴的起始端还是末尾段分布呢？亦或应该均匀分布？对应的这些可选项有：flex-start、center、flex-end以及stretch。

注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸。以下面的代码为例：只有将子元素样式中的width: 50去掉之后，alignItems: 'stretch'才能生效。

注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸。

    <View style={{
        flex: 1,
        flexDirection: 'column',
        justifyContent: 'center',
        alignItems: 'center',
      }}>
    
TextInput组件

## State

用来控制一个组件,在父组件中指定.组件之间联动的时候需要使用

例如,TextInput改变文字的时候Text内容同时改变

    export default class App extends React.Component {
    
    constructor(props) {
        super(props);
        this.showText ='text AAAAAAA';
        this.state = {text: ''};
    }

    return (
        <View style={styles.container}>
            <TextInput
                style={{height: 40, width: 120}}
                placeholder="this is placeholder"
                onChangeText={(text) => {
                    this.setState({text});
                    console.log('state.text:', this.state.text);
                }}
            <Text>{this.state.text}</Text>
        </View>
        );
    }

设定值的使用this.setState({text}); 获取属性值的时候使用this.state.text

## 网络访问 使用Fetch

包含访问的url和参数键值对

    fetch('https://mywebsite.com/endpoint/', {
    method: 'POST',
    headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        firstParam: 'yourValue',
        secondParam: 'yourOtherValue',
    })
    })

如果你的服务器无法识别上面POST的数据格式，那么可以尝试传统的form格式，示例如下：

    fetch('https://mywebsite.com/endpoint/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: 'key1=value1&key2=value2'
    })

处理返回值

    return fetch('https://facebook.github.io/react-native/movies.json')
    .then((response) => response.json())
    .then((responseJson) => {
        return responseJson.movies;
    })
    .catch((error) => {
        console.error(error);
    });

## 获取StatusBar的高度

    import {StatusBar} from 'react-native';
    StatusBar.currentHeight

    应用全屏的时候会被StatusBar覆盖,所以最外层的View应该设置style margin: StatusBar.currentHeight

## 生命周期

![component-lifecycle](http://7rf9ir.com1.z0.glb.clouddn.com/3-3-component-lifecycle.jpg)

生命周期的各个回调函数

* getDefaultProps()    
组件创建之前会调用,全局调用一次

* getInitialState()     
初始化组件的状态

* componentWillMount()    
组件创建,并被初始化了状态之后调用,在第一次绘制render之前.在这里做一些业务初始化操作,或者设置组件状态.这个函数在生命周期中只调用一次

* render()    
绘制页面

* componentDidMount()    
页面构建完成之后调用.在这个函数中可以获取其中的元素和组件

* shouldComponentUpdate()    
组件的属性prop改变的时候调用,返回true或者false决定是否需要更新组件,默认返回true保证更新流程

* componentWillUpdate() -> render() -> componentDidUpdate()    
组件状态改变的时候调用的顺序

* componentWillUnmount()
组件要从屏幕上移除的时候调用,这时候主要做一些清理工作,取消计时器,网络请求等

参考: https://race604.com/react-native-component-lifecycle/

--------

# ECMAScript基础

## 定义变量

var定义的是全局变量,定义之后所有的地方都能引用.let声明的是局部变量,只在代码块范围内有效

const声明一个只读的常量。一旦声明，常量的值就不能改变。const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。

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

## 页面跳转 ReactNavigation

__安装__

    yarn add react-navigation
    # or with npm
    # npm install --save react-navigation

__顶部工具栏StackNavigator__

    import { createStackNavigator } from 'react-navigation';

    export class App extends React.Component {
    //导航栏的属性(文字居中)
    static navigationOptions = {
        headerTitle: 'HomeScreen',
        headerTitleStyle: { 
        width: '80%',
        textAlign: 'center',
        },
        headerRight: (<View />),
        headerLeft: (<View />),
    };

    render() {
        return (
        <View style={styles.container}>
            <Text>Open up App.js to start working on your app!</Text>
            <Button
            title="Go to Details"
            onPress={() => this.props.navigation.navigate('Details', {msg: "msg to details"})}
            />
        </View>
        
        );
    }
    }

    class DetailsScreen extends React.Component {
    render() {
        const { navigation } = this.props;
        const msg = navigation.getParam('msg', 'some default value');

        return (
        <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
            <Text>{msg}</Text>
        </View>
        );
    }
    }

    //设置导航器
    export default createStackNavigator({
    //所有导航需要的画面
    Home: App,
    Details: DetailsScreen,

    }, {
    initialRouteName: 'Home',   //初始画面
    });

__底部Tab栏__

    import { createBottomTabNavigator } from 'react-navigation';

    export class App extends React.Component {
    static navigationOptions = {
        headerTitle: 'HomeScreen',
        headerTitleStyle: { 
        width: '80%',
        textAlign: 'center',
        },
        headerRight: (<View />),
        headerLeft: (<View />),
    };

    render() {
        return (
        <View style={styles.container}>
            <Text>Open up App.js to start working on your app!</Text>
            <Button
            title="Go to Details"
            onPress={() => this.props.navigation.navigate('Details', {msg: "msg to details"})}
            />
        </View>
        
        );
    }
    }

    class Tab2Screen extends React.Component {
    render() {
        const { navigation } = this.props;
        const msg = navigation.getParam('msg', 'some default value');

        return (
        <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
            <Text>{msg}</Text>
        </View>
        );
    }
    }

    export default createBottomTabNavigator({
    Home: App,
    Tab2: Tab2Screen,
    });


__侧边导航栏__

    import { createDrawerNavigator } from 'react-navigation';
    
    export class App extends React.Component {
    render() {
        return (
        <View style={styles.container}>
            <Text>Open up App.js to start working on your app!</Text>
        </View>
        
        );
    }
    }

    class MyNotificationsScreen extends React.Component {
    static navigationOptions = {
        drawerLabel: 'Notifications',
        drawerIcon: ({ tintColor }) => (
        <Image
            source={require('./chats-icon.png')}
            style={[styles.icon, {tintColor: tintColor}]}
        />
        ),
    };

    render() {
        return (
        <Button
            onPress={() => this.props.navigation.goBack()}
            title="Go back home"
        />
        );
    }
    }

    export default createDrawerNavigator({
    Home: {
        screen: App,
    },
    Notifications: {
        screen: MyNotificationsScreen,
    },
    });

    const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center',
    },
    icon: {
        width: 24,
        height: 24,
    },
    });


