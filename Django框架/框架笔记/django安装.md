# 一、django的基础使用

### 1.django的安装

##### 1.1用pycharm安装——强烈推荐

```
file ---> settings ----> project 解释器 ---> 点击+ ----> 输入Django ---> install(如果选择版本时,选择 CheckBox ,选择指定版本)
```

具体操作演示



##### 1.2正常安装

https://www.djangoproject.com/download/——网址

- 去官网下载 Django 压缩包，解压并和 Python安装目录放在同一个根目录
- 进入 Django 目录，执行 python setup.py install
- 然后开始安装，Django 将要被安装到 Python 的 Lib下site-packages。
- 然后是配置环境变量，将这几个目录添加到系统环境变量中： C:\Python33\Lib\site-packages\django;C:\Python33\Scripts。
-  添加完成后就可以使用Django的django-admin.py命令新建工程了。

#### 检查是否安装成功

进入python命令行

```
>>> import django
>>> django.get_version()
```

# 二、项目的创建及介绍

### 1.创建项目——3389

##### 1.1使用命令行创建

```
django-admin startproject mysite
```

##### 1.2正常创建

```
file ---> new project ---- 选择Django ---> 配置路径和项目名称 ---> 配置环境(默认用系统环境) ----> 点击create(完成创建)
```

```
|---mysite # 项目的/目录 
  |---mysite # 项目目录 
      |---__init__.py 
      |---settings.py # 配置文件 
      |---urls.py # 路由系统 ===> url与视图的对应关系 
      |---wsgi.py # runserver命令就使用wsgiref模块做简单的web server 
|---manage.py # 管理文件
```

### 2.创建应用

##### 2.1使用命令行创建应用

- 使用命令行模式创建应用

- 在 Terminal 中输入 **python manage.py startapp** 应用名:

**注意：**使用命令行创建的应用，一定要在 **settings.py-->INSTALLED_APPS** 中加入你的应用名字进行注册, 使用Pycharm创建的应用则不用。 认识应用结构

##### 2.2使用pycharm创建

不推荐

### 3.运行项目

##### 1.使用命令行创建

使用命令行运行项目：

1. \# 执行manage.py里的runserver命令，将项目运行起来
2. python manage.py runserver
3. 如果需要指定端口启动则为：python manage.py runserver 127.0.0.1:8001

**注意：**要在 manage.py 同级目录执行命令。

```
python3 manage.py runserver 0.0.0.0:8000
```

0.0.0.0 让其它电脑可连接到开发服务器，8000 为端口号。如果不说明，那么端口号默认为 8000。

##### 2.使用pycharm运行

点击右上角的绿色的三角按钮。

如需更改配置信息，请下拉选择 edit configurations，进行配置。

![image-20210813104703857](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210813104703857.png)



