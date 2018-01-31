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
