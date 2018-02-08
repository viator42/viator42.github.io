---
layout: post
title:  "Django学习笔记"
date:   2018-1-27
categories: Python
---

安装Django

    pip install Django==1.10.3

创建项目

    django-admin startproject projectname

项目结构

    mysite/
        manage.py
        mysite/
            __init__.py
            settings.py
            urls.py
            wsgi.py

运行

    python manage.py runserver

创建App

    python manage.py startapp myappName

url.py设置路由

数据库

settings.py中配置数据源

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
    }

engine数据库类型    
     django.db.backends.sqlite3
     django.db.backends.mysql

MySql配置

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql', #设置为mysql数据库
            'NAME': 'lemon',  #mysql数据库名
            'USER': 'root',  #mysql用户名，留空则默认为当前linux用户名
            'PASSWORD': '123123',  #mysql密码
            'HOST': 'localhost',  #留空默认为localhost
            'PORT': '3306',  #留空默认为3306端口
        }
    }

在app下创建创建models.py

    from django.db import models

    class Person(models.Model):
        name = models.CharField(max_length=30)
        age = models.IntegerField()

model关联到数据库

    python manage.py makemigrations
    python manage.py migrate

admin用户模块使用

    你可以通过命令 python manage.py createsuperuser 来创建超级用户

项目部署(以Nginx作为服务器)

    yum install python-devel
    pip install uwsgi

uwsgi 配置

创建配置文件/etc/uwsgi9000.ini

    [uwsgi]
    socket = 127.0.0.1:9090
    master = true         //主进程
    vhost = true          //多站模式
    no-site = true        //多站模式时不设置入口模块和文件
    workers = 2           //子进程数
    reload-mercy = 10     
    vacuum = true         //退出、重启时清理文件
    max-requests = 1000   
    limit-as = 512
    buffer-size = 30000
    pidfile = /var/run/uwsgi9090.pid    //pid文件，用于下面的脚本启动、停止该进程
    daemonize = /website/uwsgi9090.log

Nginx 配置

找到nginx的安装目录（如：/usr/local/nginx/），打开conf/nginx.conf文件，修改server配置：

    server {
        listen       80;
        server_name  localhost;
        
        location / {            
            include  uwsgi_params;
            uwsgi_pass  127.0.0.1:9000;              //必须和uwsgi中的设置一致
            uwsgi_param UWSGI_SCRIPT demosite.wsgi;  //入口文件，即wsgi.py相对于项目根目录的位置，“.”相当于一层目录
            uwsgi_param UWSGI_CHDIR /demosite;       //项目根目录
            index  index.html index.htm;
            client_max_body_size 35m;
        }
    }

设置完成后，在终端运行：

    uwsgi --ini /etc/uwsgi9000.ini &

Python3相关

由于Python3没有Python-mysql组件，连接数据库需要安装PyMySQL

    pip install PyMySQL

在项目的__init__.py文件中添加以下代码

    import pymysql
    pymysql.install_as_MySQLdb()

## 用户系统，自定义 User Model

__方法1: 扩展 AbstractUser类__

如果你对django自带的User model刚到满意, 又希望额外的field的话, 你可以扩展AbstractUser类:

    # myapp/models.py
        from django.contrib.auth.models import AbstractUser
        from django.db import models

        class NewUser(AbstractUser):
            new_field = models.CharField(max_length=100)

需要在settings.py中设置

    AUTH_USER_MODEL = "myapp.NewUser"

__方法2: 扩展 AbstractBaseUser类__

AbstractBaseUser中只含有3个field: password, last_login和is_active. 如果你对django user model默认的first_name, last_name不满意, 或者只想保留默认的密码储存方式, 则可以选择这一方式.

__方法3: 使用OneToOneField__

如果你想建立一个第三方模块发布在PyPi上, 这一模块需要根据用户储存每个用户的额外信息. 或者我们的django项目中希望不同的用户拥有不同的field, 有些用户则需要不同field的组合, 且我们使用了方法1或方法2:

    # profiles/models.py
    from django.conf import settings
    from django.db import models

    from flavors.models import Flavor

    class EasterProfile(models.Model):
        user = models.OneToOneField(settings.AUTH_USER_MODEL)
        favorite_ice_cream = models.ForeignKey(Flavor, null=True, blank=True)
        
        
    class ScooperProfile(models.Model):
        user = models.OneToOneField(settings.AUTH_USER_MODEL)
        scoops_scooped = models.IntergerField(default=0)
        
        
    class InventorProfile(models.Model):
        user = models.OneToOneField(settings.AUTH_USER_MODEL)
        flavors_invented = models.ManyToManyField(Flavor, null=True, blank=True)