---
layout: post
title:  "Restful 笔记"
date:   2018-03-29
categories: web
---

## 概念

RESTful Web Services是一种互联网软件架构,客户端和服务器传递资源的一个表现层

## 特点

1. 网络上所有的信息都定义成资源,包括图片,文字,网页,json数据.每一个资源对应一个URL.    
资源的类型不再使用后缀名来区分.比如xxx.jpg,xxx.html,xxx.action.资源类型的定义交由http头的Content-Type来定义.

2. Restful定义了一系列动词来表示对资源的操作

* GET（SELECT）：从服务器取出资源（一项或多项）。
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
* DELETE（DELETE）：从服务器删除资源。
* HEAD：获取资源的元数据。
* OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

资源操作的参数和返回值

* GET /items    返回item列表    参数:分页过滤信息?=limit=10&offset=10  返回:item列表
* GET /item/id  返回单个item    参数:id 返回:指定的item
* POST /items   创建按item      参数:创建item所需的数据 返回:创建的item实体
* PUT /item/id  更新指定item    参数:更新的item实体     返回:更新后的item实体
* PATCH /item/id 更新item的属性 参数:属性key-value      返回:更新后的item实体
* DELETE /item/id 删除指定item  参数:id                 返回:success

## 注意点

应该尽量将API部署在专用域名之下。

    https://api.example.com

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

    https://example.org/api/

应该将API的版本号放入URL。

    https://api.example.com/v1/



## 参考

http://www.ruanyifeng.com/blog/2014/05/restful_api.html