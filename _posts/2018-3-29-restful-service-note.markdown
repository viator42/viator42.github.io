---
layout: post
title:  "Restful 笔记"
date:   2018-03-29
categories: web
---

RESTful Web Services是一种网络规范



RESTful架构风格的特点

1. 网络上所有的信息都定义成资源,包括图片,文字,网页,json数据.每一个资源对应一个URL.资源的类型不再使用后缀名来区分.比如xxx.jpg,xxx.html,xxx.action.资源类型的定义交由http头的Content-Type来定义.

对于资源的具体操作类型，由HTTP动词表示

* GET（SELECT）：从服务器取出资源（一项或多项）。
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
* DELETE（DELETE）：从服务器删除资源。
* HEAD：获取资源的元数据。
* OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

域名

应该尽量将API部署在专用域名之下。

    https://api.example.com

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

    https://example.org/api/

