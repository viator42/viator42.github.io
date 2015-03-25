---
layout: post
title:  "Git远程仓库操作"
date:   2015-03-25
categories: git
---

ssh登录到远程服务器,创建git仓库的目录并切换到该目录.
	
	mkdir /var/git/ProjevtName
	cd /var/git/ProjevtName

创建一个空的git仓库.
	
	git init --bare

客户端生成公钥/私钥对.

	ssh-keygen -t rsa

运行后 ~/.ssh/目录下生成 id_rsa(私钥)	id_rsa.pub(公钥).
id_rsa.pub中的内容复制到服务器的~/.ssh/authorized_keys文件中.如果没有文件创建即可.

添加远程库.

	git remote add [远程库名称] [登录用户名]@[ip或域名]:[仓库目录]
	git remote add origin root@192.168.1.223:/var/git/SupaiMarketing

远程库管理.

* git remote -v			查看远程库
* git remote rm origin	删除远程库(origin可替换成仓库名)