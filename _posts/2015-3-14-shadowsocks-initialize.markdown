---
layout: post
title:  "shadowsocks+privoxy实现http代理上网"
date:   2015-03-13
categories: linux,shadowsocks
---

iOS系统不越狱的情况下不支持socks代理,只能转成http代理.

此处服务端/客户端都使用Debian系统.

####服务端

安装python,pip

	pip install shadowsocks

修改配置文件
vim /etc/shadowsocks.json

	{
	    "server":"192.88.88.88",
	    "server_port":"450",
	    "local_address": "127.0.0.1",
	    "local_port":1080,
	    "password":"hahahaha",
	    "method":"aes-256-cfb",

	}

/etc/rc.local添加
	nohup ssserver -c /etc/shadowsocks.json

把shasowsocks添加到启动项中.

####客户端

安装shadowsocks和privoxy

	pip install shadowsocks
	apt-get install privoxy

修改配置文件

	vim /etc/shadowsocks.json

	{
        "server":"192.88.88.88",
        "server_port":"450",
        "local_address": "192.168.1.223",
        "local_port":"1080",
        "password":"hahahaha",
        "method":"aes-256-cfb"
	}     

server: 服务端IP地址
server_port: 服务端定义的端口
local_address: 本机IP地址
local_port: 提供ss服务的端口
password, method: 密码,加密方法.与服务端定义的必须一致.

运行shadowsocks客户端：
	
	nohup sslocal -c /etc/shadowsocks.json &

设置privioxy

	vim /etc/privoxy/config

修改 listen-address 127.0.0.1:1081 		端口号为转换后的http代理端口
修改 forward-socks5 / 127.0.0.1:1080 . 	与ss客户端的端口号一致

此处配置为

	forward-socks5   /               192.168.1.223:1080     .
	listen-address 192.168.1.223:1081

重启服务
	service privioxy restart







