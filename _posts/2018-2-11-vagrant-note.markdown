---
layout: post
title:  "Vagrant使用笔记"
date:   2018-2-11
categories: Vagrant
---

# Markdown使用笔记
---------------------

需要安装Vagrant和VirtualBox

创建Vagrant实例，创建vagrant虚拟机目录,然后cd进此目录

    vagrant init

下载Vagrant虚拟机
修改vagrantfile文件,填入所需要的box文件

      config.vm.box = "centos/7"

完成之后运行

    vagrant up

进入虚拟机

    vagrant ssh

端口映射,把虚拟机的端口映射到宿主机本机的一个端口,注意仅限宿主机可以访问,局域网内的其他设备无法访问这个端口,有需求的话设置public_network

    config.vm.network :forwarded_port, host: 8000, guest: 80

网络桥接，让虚拟机可以使用宿主机的IP进行访问

    config.vm.network "public_network"

设置IP可以让虚拟机被局域网内的其他设备访问

    config.vm.network "public_network", ip: "192.168.0.155"

设置共享目录（可以设置多个

    config.vm.synced_folder "./vagrant", "/vagrant"


修改配置文件之后需要重置vagrant

    vagrant reload