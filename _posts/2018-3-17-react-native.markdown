---
layout: post
title:  "React Native学习笔记"
date:   2018-03-17
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

## 环境配置
安装nodejs

    npm install -g create-react-native-app

    create-react-native-app AwesomeProject
    cd AwesomeProject
    npm start

构建完成后会生成一个链接

https://expo.io/    
在这个网站下载App,扫二维码查看运行的项目

    Success! Created ugo at D:\RNWorks\ugo
    Inside that directory, you can run several commands:

    yarn start
        Starts the development server so you can open your app in the Expo
        app on your phone.

    yarn run ios
        (Mac only, requires Xcode)
        Starts the development server and loads your app in an iOS simulator.

    yarn run android
        (Requires Android build tools)
        Starts the development server and loads your app on a connected Android
        device or emulator.


    yarn test
        Starts the test runner.

    yarn run eject
        Removes this tool and copies build dependencies, configuration files
        and scripts into the app directory. If you do this, you can’t go back!


使用国内镜像加速npm和yarn

    npm config set registry=https://registry.npm.taobao.org
    yarn config set registry https://registry.npm.taobao.org
    下载cnpm：npm install -g cnpm –registry=https://registry.npm.taobao.org

如果出现something went wrong的提示一般是npm源连接不上,解决方法是设置国内源或者安装自带国内源的cnpm,cyarn

__模拟器运行__

使用Android Studio创建虚拟机

列出所有的Android虚拟机

    emulator -avd NAME

运行虚拟机
    android list avd

---------

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

--------

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

SectionView 带滚动条的Container,内容需要手动设置

    <ScrollView 
      contentContainerStyle={styles.contentContainer}
      horizontal={true} >
        ....
    </ScrollView>

__图片ImageView__

<Image source={require('./res/drawables/dummy.jpg')} />

__StackNavigation导航栏标题居中 以及定制样式__

    const deviceWidth = Dimensions.get('window').width;      //设备的宽度

headerTitle返回一个View,设置Style为宽度等于屏幕宽度减去左右按钮的宽度,然后alignItems:'center'

    export default class GoodsDetail extends React.Component {
    static navigationOptions = {
        headerTitle: (
        <View
            style={{width:StaticValues.deviceWidth-100,alignItems:'center'}} >
            <Text>商品详情Title</Text>
        </View>
        ),
        // title: '商品详情Title',
        // headerTitle: '商品详情headerTitle',
        headerStyle: {
        backgroundColor: '#f4511e',
        },
        headerTintColor: '#fff',
        headerTitleStyle: {
        fontWeight: 'bold',
        textAlign: 'center',
        },
        // headerLeft: <Text> headerLeft </Text>,
        // headerRight: <Text> headerRight </Text>,
    };

__tabNavigation设置图标__

    class Main extends React.Component {
        static navigationOptions = {
            tabBarLabel: '主页',
            headeTitle: 'Main',
        };
    }

    export default createBottomTabNavigator(
    {
      Main: Main,
      Brands: Brands,
      Theme: Theme,
      Mine: Mine,
    },
    {
      navigationOptions: ({ navigation }) => ({
        tabBarIcon: ({ focused, tintColor }) => {
          const { routeName } = navigation.state;
          if(routeName === 'Main') {
            return <Image source={require('./res/drawables/tab1.png')} />;
          }
          if(routeName === 'Brands') {
            return <Image source={require('./res/drawables/dummy.jpg')} />;
          }
          
        },
      }),
      tabBarOptions: {
        activeTintColor: 'tomato',
        inactiveTintColor: 'gray',
      },
    }
  );

## React Native - 持久化存储（AsyncStorage）


相关方法
（1）根据键来获取值，获取的结果会放在回调函数中。

    static getItem(key: string, callback:(error, result))

（2）根据键来设置值。

    static setItem(key: string, value: string, callback:(error))

（3）根据键来移除项。
    
    static removeItem(key: string, callback:(error))

（4）合并现有值和输入值。

    static mergeItem(key: string, value: string, callback:(error))

（5）清除所有的项目

    static clear(callback:(error))

（6）获取所有的键

    static getAllKeys(callback:(error, keys))

（7）清除所有进行中的查询操作。

    static flushGetRequests()

（8）获取多项，其中 keys 是字符串数组，比如：['k1', 'k2']

    static multiGet(keys, callback:(errors, result))

（9）设置多项，其中 keyValuePairs 是字符串的二维数组，比如：[['k1', 'val1'], ['k2', 'val2']]

    static multiSet(keyValuePairs, callback:(errors))

（10）删除多项，其中 keys 是字符串数组，比如：['k1', 'k2']

    static multiRemove(keys, callback:(errors))

（11）多个键值合并，其中 keyValuePairs 是字符串的二维数组，比如：[['k1', 'val1'], ['k2', 'val2']]
	
    static multiMerge(keyValuePairs, callback:(errors))

参考代码:

    var user = [
        ['userId', data.userId],
        ['sessionid', data.sessionid],
        ['userName', data.userName],
        ['mobile', data.mobile],
        ['headImg', data.headImg],
    ];

    AsyncStorage.multiSet(user, function(errs) {
        if(errs) {
            console.log("存储成功");
        }
        else {
            console.log("存储失败");
        }
    });

参考链接: http://www.hangge.com/blog/cache/detail_1567.html

--------
## JSX语法

注释

    {/**/}

-------

## 生命周期

![component-lifecycle](http://7rf9ir.com1.z0.glb.clouddn.com/3-3-component-lifecycle.jpg)

生命周期的各个回调函数

### 初始化调用的方法

* getDefaultProps()        
组件实例创建前调用，多个实例间共享引用,用来初始化组件的状态,注意：如果父组件传递过来的Props和你在该函数中定义的Props的key一样，将会被覆盖。    
全局调用一次    

* getInitialState()     
组件示例创建的时候调用的第一个函数。主要用于初始化state。注意：为了在使用中不出现空值，建议初始化state的时候尽可能给每一个可能用到的值都赋一个初始值。    
全局调用一次

* componentWillMount()    
组件创建,并被初始化了状态之后调用,在第一次绘制render之前.在这里做一些业务初始化操作,或者设置组件状态.这个函数在生命周期中只调用一次

* render()    
绘制页面

* componentDidMount()    
页面构建完成之后调用.在这个函数中可以获取其中的元素和组件

### 页面更新调用的方法

* componentWillReceiveProps(nextProps)    
如果组件收到新的属性（props）的时候调用    

* shouldComponentUpdate()    
组件的属性prop改变的时候调用,返回true或者false决定是否需要更新组件,默认返回true保证更新流程
如果返回true则按照 componentWillUpdate() -> render() -> componentDidUpdate() 的顺序调用

* componentWillUnmount()
组件要从屏幕上移除的时候调用,这时候主要做一些清理工作,取消计时器,网络请求等

参考: https://race604.com/react-native-component-lifecycle/

--------

## 动画相关

React Native提供了两个互补的动画系统：用于全局的布局动画LayoutAnimation，和用于创建更精细的交互控制的动画Animated。

###  Animated

Animated旨在以声明的形式来定义动画的输入与输出，在其中建立一个可配置的变化函数，然后使用简单的start/stop方法来控制动画按顺序执行。 Animated仅封装了四个可以动画化的组件：View、Text、Image和ScrollView

    export default class FadeInView extends Component {
    constructor(props) {
        super(props);
        this.state = {
            fadeAnim: new Animated.Value(0),          // 设置动画变化的变量值
        };
    }
    componentDidMount() {
        // 动画开始运行
        Animated.timing(                            // 随时间变化而执行的动画类型
        this.state.fadeAnim,                      // 动画中的变量值
        {
            toValue: 1,                             // 透明度最终变为1，即完全不透明
        }
        ).start();                                  // 开始执行动画
    }
    render() {
        return (
        <Animated.View                            // 可动画化的视图组件
            style={{
            ...this.props.style,
            opacity: this.state.fadeAnim,          // 将透明度指定为动画变量值
            }}
        >
            {this.props.children}
        </Animated.View>
        );
    }
    }

--------

## 常见WARNING解决

__React Native Warning Failed child context type: Invalid child context ‘virtualizedCell.cellKey’__

FlatList每一个cell的key值必须是string类型,否则会报警告

解决方法:

<FlatList
    ......
    keyExtractor={ (item, index) => index.toString() }
    />

__Each child in an array or iterator should have a unique "key" prop. Check the top-level render call using__

解决方法:

在View中循环添加子view的时候加入key属性

    <View
        ...
        key={activity['id']}>
        ...
        </View>

--------

## 集成到Android原生应用

先创建ReactNative项目,在RN目录下创建android项目

在RN项目根目录下创建一个名为package.json的空文本文件，然后填入以下内容：

    {
        "name": "MyReactNativeApp",
        "version": "0.0.1",
        "private": true,
        "scripts": {
            "start": "node node_modules/react-native/local-cli/cli.js start"    //重点修改这里
        },
        "dependencies": {
            "react": "16.0.0-alpha.6",
            "react-native": "0.44.3"
        }
    }

Android项目的配置

app/build.gradle

    android {
        ...
        defaultConfig {
            ...
            ndk {
                abiFilters "armeabi-v7a", "x86"
            }

            packagingOptions {
                exclude "lib/arm64-v8a/libgnustl_shared.so"
            }

        }
    }

    dependencies {
        ...
        compile "com.facebook.react:react-native:+" // From node_modules.
    }

    configurations.all {
        resolutionStrategy.force 'com.google.code.findbugs:jsr305:2.0.1'
    }

在项目的 build.gradle 文件中为 React Native 添加一个 maven 依赖的入口，必须写在 "allprojects" 代码块中:

    allprojects {
        repositories {
            ...
            maven {
                // All of React Native (JS, Android binaries) is installed from npm
                url "$rootDir/../node_modules/react-native/android"
            }
        }
        ...
    }

androidManife.xml修改

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.viator42.nativeconnect">
        <uses-permission android:name="android.permission.INTERNET" />  //声明网络权限

        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">

            <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />   //开发者菜单页面

            <activity
                android:name=".MainActivity"
                android:theme="@style/Theme.AppCompat.Light.NoActionBar">   //Style修改
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>

RN端创建index.js入口文件

    import React from 'react';
    import {
    AppRegistry,
    StyleSheet,
    Text,
    View
    } from 'react-native';

    class HelloWorld extends React.Component {
    render() {
        return (
        <View style={styles.container}>
            <Text style={styles.hello}>Hello, World</Text>
        </View>
        )
    }
    }
    var styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
    },
    hello: {
        fontSize: 20,
        textAlign: 'center',
        margin: 10,
    },
    });

    AppRegistry.registerComponent('MyReactNativeApp', () => HelloWorld);


MainActivity.java

    public class MainActivity extends AppCompatActivity implements DefaultHardwareBackBtnHandler {
        private ReactRootView mReactRootView;
        private ReactInstanceManager mReactInstanceManager;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mReactRootView = new ReactRootView(this);
            mReactInstanceManager = ReactInstanceManager.builder()
                    .setApplication(getApplication())
                    .setBundleAssetName("index.android.bundle")
                    .setJSMainModulePath("index")
                    .addPackage(new MainReactPackage())
                    .setUseDeveloperSupport(BuildConfig.DEBUG)
                    .setInitialLifecycleState(LifecycleState.RESUMED)
                    .build();

            // 注意这里的MyReactNativeApp必须对应“index.js”中的
            // “AppRegistry.registerComponent()”的第一个参数
            mReactRootView.startReactApplication(mReactInstanceManager, "MyReactNativeApp", null);

            setContentView(mReactRootView);

        }

        @Override
        public void invokeDefaultOnBackPressed() {
            super.onBackPressed();
        }
    }

## 与原生项目交互

__创建模块类__

    public class ShowerModule extends ReactContextBaseJavaModule {
        public ShowerModule(ReactApplicationContext reactContext) {
            super(reactContext);
        }

        @Override
        public String getName() {
            return "ShowerModule";
        }

        @ReactMethod
        public void show(String content) {
            Log.v("ShowerModule showToast", content);
            Toast.makeText(getReactApplicationContext(), content, Toast.LENGTH_SHORT);
        }
    }

getName()方法返回模块的名称,就是在RN端调用时引用的名称    
被调用的方法必须添加@ReactMethod注解,无返回值    

__创建Package注册模块__

    public class ExampleReactPackage implements ReactPackage {
        @Override
        public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
            List<NativeModule> modules = new ArrayList<NativeModule>();
            modules.add(new ShowerModule(reactContext));
            return modules;
        }
        
        @Override
        public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
            return Collections.emptyList();
        }
    }

createNativeModules()方法返回一个List,其中添加module的对象

__Activity中使用模块__

    public class MainActivity extends AppCompatActivity implements DefaultHardwareBackBtnHandler {
        private ReactRootView mReactRootView;
        private ReactInstanceManager mReactInstanceManager;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mReactRootView = new ReactRootView(this);
            mReactInstanceManager = ReactInstanceManager.builder()
                    .setApplication(getApplication())
                    .setBundleAssetName("index.android.bundle")
                    .setJSMainModulePath("index")
                    .addPackage(new MainReactPackage())
                    .addPackage(new ExampleReactPackage())  //在这里添加注册的模块Package
                    .setUseDeveloperSupport(BuildConfig.DEBUG)
                    .setInitialLifecycleState(LifecycleState.RESUMED)
                    .build();

            // 注意这里的MyReactNativeApp必须对应“index.js”中的
            // “AppRegistry.registerComponent()”的第一个参数
            mReactRootView.startReactApplication(mReactInstanceManager, "MyReactNativeApp", null);

            setContentView(mReactRootView);
        }

        @Override
        public void invokeDefaultOnBackPressed() {
            super.onBackPressed();
        }
    }

__RN端的实现__

    import React from 'react';
    import {
    AppRegistry,
    StyleSheet,
    Text,
    View,
    Button,
    NativeModules
    } from 'react-native';

    class HelloWorld extends React.Component {
        render() {
            return (
            <View style={styles.container}>
                <Text style={styles.hello}>Hello, RN in android</Text>
                <Button 
                    style={styles.button}
                    title='Toast show' 
                    onPress={() => {
                    console.log("show toast");
                    NativeModules.ShowerModule.show('aaaaaa');  //根据 NativeModules.模块名.方法名 调用
                    }}/>
            </View>
            )
        }
    }

    AppRegistry.registerComponent('MyReactNativeApp', () => HelloWorld);

### RN端和Android原生的数据交互

RN端到原生端可以使用方法参数传递值,原生到RN不能使用方法返回值,因为调用是异步的.可以使用回调方法或者Promise机制

Android原生端

    public class ShowerModule extends ReactContextBaseJavaModule {

        public ShowerModule(ReactApplicationContext reactContext) {
            super(reactContext);
        }

        @Override
        public String getName() {
            return "ShowerModule";
        }

        @ReactMethod
        public void testAndroidCallbackMethod(String param, Callback callback) {
            Log.v("callback", param);
            callback.invoke("This is callback result");
        }

        @ReactMethod
        public void textAndroidPromiseMethod(String param, Promise promise) {
            Log.v("promise", param);
            promise.resolve("This is promise result");
        }
    }

RN端

    <Button 
        style={styles.button}
        title='Toast show' 
        onPress={() => {
            console.log("show toast");
            NativeModules.ShowerModule.testAndroidCallbackMethod('this is callback param', (result) => {
            console.log(result);
            });

        }}/>

    <Button 
        style={styles.button}
        title='Toast show' 
        onPress={() => {
            console.log("show toast");
            NativeModules.ShowerModule.textAndroidPromiseMethod('this is promise param').then((result) => {
            console.log(result);
            }).catch((error) => {
            console.log(error);
            });

        }}/>

