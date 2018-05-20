---
layout: post
title:  "Python3学习笔记"
date:   2018-5-19
categories: Python
---

## 文件模板

    #!/usr/bin/env python
    # -*- coding: utf-8 -*-

    def main():
        print("Hello吱吱吱吱")

    if __name__ == '__main__':
        main()

## 语法

\# 开头的是单行注释
''' ''' 三个引号包裹的是多行注释

## 数据类型

a = 'This is an String'     #字符串    
a = 123     #整数    
a = 123.45     #浮点数     
布尔值用True， False表示    
空值: None    

## 表达式

/ 除法，两个数有一个是浮点数结果就是浮点数    
// 整除    
% 取余数     
** 乘方  比如 2 ** 3 = 8    

## Console输入输出

print(”text“)

x = input("Input X:")    
y = raw_input("what is your name:")    
raw_input会将用户输入的所有内容作为原始数据，放入字符串中    

## 列表和元组

列表可以修改，元组不能

__列表__

list = ["1", "2", "3"]

__列表方法__

append("value") 在末尾增加
count() 返回列表元素的数目
extend(list) list添加多个元素，参数同为list
insert(4, 'a') 在任意位置添加
sort() 排序

## 元组

tuple = (1, 2, 3)

## 字符串

格式化操作字符串    

    ‘This is an Mixed string: %d %s' % 1, 'aaa'    

__字符串查找__

    index = dest_str.find('value')

在一个较长的字符串中查找子字符串，返回子串所在位置最左边的索引，没有找到返回-1

## 字典

    dict1 = {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}

__字典操作__

* len(dict) 返回dict中项的数量
* dict[k] 返回dict中k项的值
* dict[k] = v 赋值
* del dict[k] 删除项
* k in dict 检查dict中是否有含有建k的项
* dict.clear() 清空
* value = dict.get(key) 获取值，不存在时返回None

__字典遍历__

    for (d,x) in dict.items():
        print "key:"+d+",value:"+str(x)

## 判断

    if True:
        print ("aaa")
    elif True:
        print ("bbb")
    else:
        print ("ccc")
     
表达式的与或非使用and、or和not

## 循环

    for item in list:
        print(item)

## 方法

    def func(param1, param2):
        return param1 + param2

    func(param1 = 111, param2 = 222)

调用的时候参数的顺序可以不按照定义的顺序，必须赋值给参数名

## 类和对象

    class Student(object):
        def __init__(self, name, score):
            self.name = name
            self.score = score

        def print_score(self):
            print('%s: %s' % (self.name, self.score))

## GUI库wxPython

    #!/bin/env python
    import wx

    class MyFrame(wx.Frame):
        def __init__(self):
            wx.Frame.__init__(self, None, -1, "My Frame", size=(300, 300))
            panel = wx.Panel(self, -1)
            panel.Bind(wx.EVT_MOTION, self.OnMove)
            wx.StaticText(panel, -1, "Pos:", pos=(10, 12))
            self.posCtrl = wx.TextCtrl(panel, -1, "", pos=(40, 10))
        def OnMove(self, event):
            pos = event.GetPosition()
            self.posCtrl.SetValue("%s, %s" % (pos.x, pos.y))

    if __name__ == '__main__':
        app = wx.PySimpleApp()
        frame = MyFrame()
        frame.Show(True)
        app.MainLoop()

## 正则表达式re

__使用方法__

导入包

    import re

将正则表达式编译成Pattern对象

    pattern = re.compile(r"\d+")

使用Pattern匹配文本

pattern.search方法返回一个match对象,其中的group列表里有所有的匹配文本
获取第一个匹配对象

    if match:
        match = serialPattern.search(url).group(0)

pattern.findAll方法返回所有匹配的子串,并返回一个list

__正则表达式语句__

 |表达式 | 描述 |
 |----|----|
 | a, X, 9, < | 普通字符完全匹配。|
 | 	. | 匹配任何单个字符，除了换行符’\n‘ |
 | 	\w | 匹配“单词”字符：字母或数字或下划线[a-zA-Z0-9_]。 |
 | 	\W | 匹配任何非字词。 |
 | 	\b | 字词与非字词之间的界限 |
 | 	\s | 匹配单个空格字符 - 空格，换行符，返回，制表符 |
 | 	\S | 匹配任何非空格字符。 |
 | 	\t, \n, \r | 制表符，换行符，退格符 |
 | 	\d | 十进制数[0-9] |
 | 	^ | 匹配字符串的开头 |
 | 	$ | 匹配字符串的末尾 |
 | 	\ | 抑制字符的“特殊性”，也叫转义字符。 |





