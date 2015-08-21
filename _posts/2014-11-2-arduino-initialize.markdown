---
layout: post
title:  "Arduino开发环境搭建"
date:   2014-08-4
categories: arduino
---

准备做一个Arduino+Python的个人项目，发现Arduino找不到一个好用的开发环境.官方的IDE实在是太难用，不支持输入中文。，Sublime的插件的只能编辑文件和编译上传，没法创建项目，新建项目还得切换到官方IDE下。后来发现eclipse也支持Arduino开发，虽然对eclipse没什么好感，但起码是个正经的开发环境了。后来趁着周末折腾了一次，才发现把这货用起来需要花费的精力不比sublime少多少，不过总算是成功了，趁着还没忘记录下步骤。

1. 下载eclipse for C++ 3.8，Arduino IDE 1.5.8。
2. 打开help->Eclipse market place,安装arduino ide扩展。安装PyDev提供Python开发环境。color eheme用来更改编辑器配色，安装完成后在Gernel->Apperance->color theme里更改配色方案。我还装了个Markdown Text Editor用来写markdown，只要扩展名为md的文件eclipse就能自动识别。
3. 打开reference->arduino, 设置Arduino IDE path到你的arduino的安装目录。
4. 此时eclipse还不支持输入中文，下载rabel语言包。压缩包里的文件复制到eclipse安装目录中即可。
开发arduino需要eclipse以root用户身份启动，因此要么在root用户下开发，要么编写脚本sudo启动eclipse。

	#! /bin/bash
	sudo /opt/eclipse/eclipse

