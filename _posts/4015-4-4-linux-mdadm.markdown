---
layout: post
title:  "CentOS6下使用mdadm创建raid1"
date:   2015-03-13
categories: linux
---

磁盘 /dev/sdb /dev/sdc

使用fdisk进行磁盘分区.


	mdadm -Cv /dev/md0 -l1 -n2 /dev/sdb1 /dev/sdc1 

对raid分区进行格式化

	mkfs.ext4 /dev/md0

格式化完成后就能像普通分区一样进行挂载,编辑 /etc/fstab,添加到开机自动挂载

	/dev/md0                /home/viator42/storage  ext4    defaults        0 0



参考链接: http://linux.cn/article-4891-1.html