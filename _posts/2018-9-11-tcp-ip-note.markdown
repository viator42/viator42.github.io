---
layout: post
title:  "TCP/IP相关笔记"
date:   2018-9-11
categories: notes tcp/ip
---


### HTTP方法

* GET 从服务器向客户端发送命名资源, 客户端请求数据
* PUT 客户端的数据存储到服务器
* POST 客户端发送数据到服务器
* DELETE 请求服务器删除资源
* HEAD 请求服务器返回请求HTTP头部

### 状态码

* 200 文档正常返回
* 302 重定向到另一个URL
* 404 无法找到资源
* 500 服务器出错

### http报文

http报文分为

* 起始行

请求报文的起始行，或称为请求行。包含了一个方法和一个请求的URL。这个方法描述了服务器应该执行的操作，请求URL描述了要对哪个资源执行这个方法。请求行中还包含HTTP的版本，用来告知服务器，客户端使用的是哪种HTTP版本。如：

GET /info/123.html HTTP/1.1 //方法为GET URL为 /info/123.html HTTP协议版本为1.1

* 首部字段

HTTP首部字段向请求和响应报文中添加了一些附加信息。本质上来说，它们只是一些名/值对的列表。


* 主体

分层

OSI七层模型

1. 应用层    网络进程到应用程序。针对特定应用规定各层协议、时序、表示等，进行封装 。在端系统中用软件来实现，如HTTP等
2. 表示层    数据表示形式，加密和解密，把机器相关的数据转换成独立于机器的数据。规定数据的格式化表示 ，数据格式的转换等
3. 会话层    主机间通讯，管理应用程序之间的会话。规定通信时序 ；数据交换的定界、同步，创建检查点等
4. 传输层    在网络的各个节点之间可靠地分发数据包。所有传输遗留问题；复用；流量；可靠
5. 网络层    在网络的各个节点之间进行地址分配、路由和（不一定可靠的）分发报文。路由（ IP寻址）；拥塞控制。
6. 数据链路层    一个可靠的点对点数据直链。检错与纠错（CRC码）；多路访问；寻址
7. 物理层    一个（不一定可靠的）点对点数据直链。定义机械特性；电气特性；功能特性；规程特性

TCP/IP模型

1. 网络接入层 提供网络的物理连接
2. 网络互联层 提供数据报分组,路由选择. 包括IP,ICMP,ARP,RARP协议,
3. 主机对主机层 提供主机对主机的连接,包括TCP,UDP协议
4. 进程/应用层 给用户提供应用程序协议,暴扣FTP,DNS,SMTP等协议

--------

## Restful API 架构

### url规范

应该尽量将API部署在专用域名之下。

    https://api.example.com

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

    https://example.org/api/

应该将API的版本号放入URL。

    https://api.example.com/v1/

### HTTP动词

* GET（SELECT）：从服务器取出资源（一项或多项）。
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
* DELETE（DELETE）：从服务器删除资源。

还有两个不常用的HTTP动词。

        HEAD：获取资源的元数据。
        OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

### 过滤信息

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。

    ?limit=10：指定返回记录的数量
    ?offset=10：指定返回记录的开始位置。
    ?page=2&per_page=100：指定第几页，以及每页的记录数。
    ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
    ?animal_type_id=1：指定筛选条件

### 状态码

服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

* 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
* 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
* 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
* 204 NO CONTENT - [DELETE]：用户删除数据成功。
* 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
* 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
* 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
* 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
* 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
* 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
* 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
* 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

### 错误处理

如果状态码是4xx，就应该向用户返回出错信息。一般来说，返回的信息中将error作为键名，出错信息作为键值即可。

    {
        error: "Invalid API key"
    }

### 返回结果的规范

* GET /collection：返回资源对象的列表（数组）
* GET /collection/resource：返回单个资源对象
* POST /collection：返回新生成的资源对象
* PUT /collection/resource：返回完整的资源对象
* PATCH /collection/resource：返回完整的资源对象
* DELETE /collection/resource：返回一个空文档

参考: http://www.ruanyifeng.com/blog/2014/05/restful_api.html

--------


