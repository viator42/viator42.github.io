---
layout: post
title:  "linux系统搭建transmission下载服务器"
date:   2015-03-13
categories: linux
---

####安装
    apt-get install transmission transmission-cli transmission-common transmission-daemon

默认端口是9091
访问会出现403 forbidden

####配置文件
/etc/transmission-daemon/settings.json

####默认内容
    {
        "alt-speed-down": 50, 
        "alt-speed-enabled": false, 
        "alt-speed-time-begin": 540, 
        "alt-speed-time-day": 127, 
        "alt-speed-time-enabled": false, 
        "alt-speed-time-end": 1020, 
        "alt-speed-up": 50, 
        "bind-address-ipv4": "0.0.0.0", 
        "bind-address-ipv6": "::", 
        "blocklist-enabled": false, 
        "blocklist-url": "http://www.example.com/blocklist", 
        "cache-size-mb": 4, 
        "dht-enabled": true, 
        "download-dir": "/var/lib/transmission-daemon/downloads", 
        "download-limit": 100, 
        "download-limit-enabled": 0, 
        "download-queue-enabled": true, 
        "download-queue-size": 5, 
        "encryption": 1, 
        "idle-seeding-limit": 30, 
        "idle-seeding-limit-enabled": false, 
        "incomplete-dir": "/root/Downloads", 
        "incomplete-dir-enabled": false, 
        "lpd-enabled": false, 
        "max-peers-global": 200, 
        "message-level": 2, 
        "peer-congestion-algorithm": "", 
        "peer-limit-global": 240, 
        "peer-limit-per-torrent": 60, 
        "peer-port": 51413, 
        "peer-port-random-high": 65535, 
        "peer-port-random-low": 49152, 
        "peer-port-random-on-start": false, 
        "peer-socket-tos": "default", 
        "pex-enabled": true, 
        "port-forwarding-enabled": false, 
        "preallocation": 1, 
        "prefetch-enabled": 1, 
        "queue-stalled-enabled": true, 
        "queue-stalled-minutes": 30, 
        "ratio-limit": 2, 
        "ratio-limit-enabled": false, 
        "rename-partial-files": true, 
        "rpc-authentication-required": true, 
        "rpc-bind-address": "0.0.0.0", 
        "rpc-enabled": true, 
        "rpc-password": "{288a53730dda6eade43b61cfd96706c6aa69b7bbUlXVk2Pg", 
        "rpc-port": 9091, 
        "rpc-url": "/transmission/", 
        "rpc-username": "transmission", 
        "rpc-whitelist": "127.0.0.1", 
        "rpc-whitelist-enabled": true, 
        "scrape-paused-torrents-enabled": true, 
        "script-torrent-done-enabled": false, 
        "script-torrent-done-filename": "", 
        "seed-queue-enabled": false, 
        "seed-queue-size": 10, 
        "speed-limit-down": 100, 
        "speed-limit-down-enabled": false, 
        "speed-limit-up": 100, 
        "speed-limit-up-enabled": false, 
        "start-added-torrents": true, 
        "trash-original-torrent-files": false, 
        "umask": 18, 
        "upload-limit": 100, 
       "upload-limit-enabled": 0, 
        "upload-slots-per-torrent": 14, 
        "utp-enabled": true
    }

需要修改的参数

下载文件目录

    "download-dir": "/home/viator42/BT/downloads"

远程访问ip白名单

    "rpc-whitelist": "192.168.1.*"

开启临时文件目录和临时文件目录位置

    "incomplete-dir": "/home/viator42/BT/Temp",
    "incomplete-dir-enabled": true, 

监视目录, 当向目录内添加种子文件时自动开始下载

    "watch-dir": "/home/viator42/BT/torrents",
    "watch-dir-enabled": true

其他参数如下

https://trac.transmissionbt.com/wiki/ConfigurationParameters

####修改完成后重启服务

    service transmission-daemon start

####默认访问地址

http://192.168.1.204:9091/
用户名: transmission	密码: transmission

