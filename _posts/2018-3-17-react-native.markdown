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

指定宽高
    
    <View style={{width: 50, height: 50}} />

Flex弹性宽高

类似Android中的weight属性,代表占空间的权重比,数字越大占的相对空间越大,总和是父元素的尺寸

    <View style={{flex: 1}}>
        <View style={{flex: 1, backgroundColor: 'powderblue'}} />
        <View style={{flex: 2, backgroundColor: 'skyblue'}} />
        <View style={{flex: 3, backgroundColor: 'steelblue'}} />
    </View>

屏幕布局结构

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

## ECMAScript基础相关

箭头函数

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

箭头左边是参数, 右边是单行的表达式,表达式的结果作为函数返回值.
如果是多行的话用{}包裹函数体并用return返回结果

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

