---
layout: post
title:  "Debian7搭建LNMP笔记"
date:   2015-5-8
categories: linux
---

安装软件包
    
    apt-get install php5 php5-mysql php5-fpm mysql-server nginx

启动服务

    service mysql start
    service nginx start
    service php5-fpm start

修改php配置文件.

    vim /etc/php5/fpm/php.ini

找到这一行 cgi.fix_pathinfo=1 然后把值从1改为0.

    cgi.fix_pathinfo=0

修改php-fpm的配置文件

vim /etc/php5/fpm/pool.d/www.conf

找到listen = 127.0.0.1:9000 这一行然后把 127.0.0.1:9000 改为 /var/run/php5-fpm.sock.

修改Nginx的配置文件

    vim /etc/nginx/sites-available/default

需要修改的部分

1. root 网站的根目录.
2. index 添加index.php.
3. server_name 改为自己的域名.
4. 确保配置文件中以下的部分不被注释.

    server {
        listen   80; ## listen for ipv4; this line is default and implied
        #listen   [::]:80 default_server ipv6only=on; ## listen for ipv6

        root /var/www/supai-server/supai;
        index index.php index.html index.htm;

        # Make site accessible from http://localhost/
        server_name supai.im;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ /index.html;
                # Uncomment to enable naxsi on this location
                # include /etc/nginx/naxsi.rules
        }

        error_page 404 /404.html;

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
                root /usr/share/nginx/www;
        }

        location ~ \.php$ {
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini

                # With php5-cgi alone:
                #fastcgi_pass 127.0.0.1:9000;
                # With php5-fpm:
                fastcgi_pass unix:/var/run/php5-fpm.sock;
                fastcgi_index index.php;
                include fastcgi_params;
        }
    }

重启所有服务,配置完成.

设置允许外部访问MySQL数据库.生产环境下慎用.

vim /etc/mysql/my.conf 
找到 bind-address = 127.0.0.1 并注释掉此行.


