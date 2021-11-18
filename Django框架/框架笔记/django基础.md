# 第一章：django项目的创建

### 1.首先要创建虚拟环境

```python
#1.创建虚拟环境
mkvirtualenv -p /usr/bin/python3 环境的名称
#2.要知道的东西
workon	#查看所有的虚拟环境
deactivate #退出当前的虚拟环境
workon 虚拟环境的名字 #切换成虚拟环境
```

### 2.django准备

```python
1.django-admin startproject 项目名称（django9_10） #创建项目
2.pip install django==2.1.7 -i 网址 #下载django项目
```

- 在pycharm里面控制django项目
  - 首先先创建一个本地项目，在创建的同时配置好相应的环境。
  - 配置好环境后，再到tool里面的deployment下的configraution设置配置，记得改mapping中的两个/，/

- 运行django项目
  - 在服务器上运行 python manage.py runserver 0.0.0.0:8000
  - 在pycharm上运行，在django server 里的fix配置

### 3.django的app创建

```
1.python manage.py startapp app的名字
2.在pycharm中，tool里的run manage.py task 下的startapp 名字
```

# 第二章：url路由分配和渲染方式

### 1.url分配

- 一开始会有主要的url，但是要分配到相对应的app里面去。

```
1.from django.urls import include #导入要分配的东西,分配子路由
	1.path('music/', include('music.urls')),#分配子路由
	记着要把app注册进去，在这其中可以把{}键值对传给每一个url，在app里的**kwargs中获取
2.path('test1/<slug:name>/<int:age>',views.test1),这个路由的变量名与函数的变量名必须一致。
3.re_path('^test1/(?P<name>[A-Za-z0-9]{4,12})/(?P<age>[0-1][0-9{0,2})/$',views.test1),通过正则来匹配path规则，但是要先导入re_path
```

### 2.重定向

- 给一个url一个新的名字，name ='名字'

```python
path('index_new/',views.index_new,name='new')
def index(request):
    return redirect(reverse('new'))
def index_new(request):
    return HttpResponse('这是新的book页面')
```

### 3.模板

- 新建文件夹——templates,在这之下新建app的文件夹
- 再在settings文件里面注册模板文件'DIRS': [os.path.join(BASE_DIR,'templates')],

### 4.渲染模板render

```python
return render(request,'渲染的页面')
```

### 5.模板变量

```python
#渲染页面的时候，可以传给页面的一些值
return render(request,'book/index.html',context={
        'book_name':'python',
        'hello':hello,
        'anyan':anyan,
        'say':anyan.say,
        'li':li,
        'dic':dic,
    })
#html——从后台来获取数据，并在前端显示数据。
{{book_name}}#正常变量
{{anyan.name}}#类的名称
{{anyan.say}}#类的方法
{{list.1}}#列表的数据
{{dic.a}}#字典的数据
```

### 6.过滤器

- 使用参数:过滤器可以使用参数，在过滤器名称后面使用冒号”:”再加上参数，比如要把一个字符串中所有的空格去掉，则可以使用cut过滤器,

- ```
  {{fruit|cut:" "}}
  ```

  ![image-20210908192945017](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210908192945017.png)

![image-20210908193023944](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210908193023944.png)

### 7.静态文件配置及使用

```python
#首先创建static文件夹
STATIC_URL = '/static/'
STATICFILES_DIRS=[
    os.path.join(BASE_DIR,'static')
]
```

静态文件的引用

```python
#现在最开头将静态文件加载进来
{% load static %}

#之后进行加载文件
<link rel="stylesheet"  href="{ % static '要加载的样式' %}">
<img src="{ % static '要加载的样式' %}">
```

# 第三章：模板标签

### 1.定义

- 由花括号与%组成
- ifelse for循环。

```python
{#常用的模板标签#}
{% if name == 'anyan' and age == 18 %}
我是{{ name }},今年{{ age }}岁
 {% elif name == 'yige' and age == 28 %}
今年不是{{ name }}
{% endif %}

{% for foo in li %}
 这是循环出来的数据{{ foo }}<br>
{% endfor %}

下标是从一开始的——forloop.counter0(这是下标从0开始的)
{% for foo in dic %}

拿到的下标是{{ forloop.counter }}
{% endfor %}
也可以拿到双循环的父级元素的下标。forloop.parentloop.counter0
    
通过name来跳转（无参数）
<a href="{% url 'new' %}">这是去新网站</a>
    有参数时在name后面加上参数
    
取消转义
{{ hlml | safe }}

进行统一取消转义
{% autoescape off %}
{{ html }}
{% endautoescape %}
进行多行注释
{% comment %}

{% endcomment %}
```

### 2.模板继承

```python
#base.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}
    
    {% endblock %}</title>
</head>
<body>
{% block head %}
这是网页头部的统一样式
{% endblock %}

{% block content %}
这是网页中间的统一样式
{% endblock %}
{% block bottom %}
这是网页尾部的统一样式
{% endblock %}
</body>
</html>

继承模板页面
{% extends 'book/base.html' %}

修改各个部分
修改bottom的样式
{% block bottom %}
这是网页尾部的统一样式
{% endblock %}

{% block bottom %}
{{block.supper}}——拥有父类的格式
这是网页尾部的统一样式
{% endblock %}

{% block head %}
{% include 'book/index2.html' %}
{% endblock %}
```

- 只能继承一个，但是可以引用其它类型的

# 第四章：自定义模板和标签

### 1.自定义过滤器

- 首先创建app，再其下的文件里面创建templatetags文件
- 在templatetags文件夹下创建py文件

```python
from django import template
register=template.Library()  #生成注册器，但是名字不能被更改

#注册器的名称默认是函数本身的名字

#第二种方式
@register.filter()
def my_upper(value):
    return value.upper()
#第一种方式
register.filter(my_upper) #将函数注册进过滤器中
#第二种方式——加入装饰器
#使用：{% load common_extra %} #先加载过滤器，才能进行使用
```

### 2.自定义标签

- 简单标签与包含标签

#### 2.1简单标签

```python
#显示当前的时间——自定义模板标签
#参数用空格隔开
import datetime
@register.simple_tag
def current_time():
    format_time="%Y年%m月%d日 %H:%M:%S"
    return datetime.datetime.now().strftime(format_time)

#获取到试图函数里面传过去的数据
@register.simple_tag(takes_context=True)
def current_time1(context):
    format_time=context.get('format_time')
    return datetime.datetime.now().strftime(format_time)
```

#### 2.2包含标签

```python
#定义
@register.inclusion_tag('book/show_tag.html')
def show_tags():
    li=[1,2,3,5,6,]
    return {'values':li}
#在'book/show_tag.html'html中设置展示的形式
<ul>
        {% for foo in values %}
            <li>{{ foo }}</li>
        {% endfor %}
</ul>
#使用
{% show_tags %}

#如果要取视图函数中的数据跟上一个一样
```

# 第五章：模型基础——数据库

### 1.数据库的配置（mysql）

- 首先在数据库中创建表。

```python
#在settings文件中修改数据库的配置
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        'ENGINE':'django.db.backends.mysql',#数据库引擎
        'NAME':'django9_10',#数据库的使用
        'USER':'admin',
        'PASSWORD':'qwe123',
        'HOST':'127.0.0.1',
        'PORT':3306,
    }
}
#之后再__init__文件中加入
import pymysql
pymysql.install_as_MySQLdb()

#在新建的app项目中的models文件里
class User(models.Model):
    id = models.AutoField(primary_key=True)
    name=models.CharField(max_length=30)
    age=models.IntegerField()
#但是还要做映射
#在终端中makemigrations model_test
#migrate model_test
```

### 2.数据的增删改查

添加数据

```python
def add_user(request):
    # #方法一：数据必须进行保存
    # anyan = User(name='铵盐',age=18)
    # anyan.save()    #保存数据
    # return HttpResponse('数据添加成功')
    # 方法二：数据必须进行保存
    # anyan=User()
    # anyan.name='小黑'
    # anyan.age=32
    # anyan.save()  # 保存数据
    #方法三：
    User.objects.create(name='小黄',age=64)
    #方法四：
    User.objects.get_or_create(name='小黄',age=64)
    return HttpResponse('数据添加成功')
```

修改打印的数据

```python
class User(models.Model):
    id = models.AutoField(primary_key=True)
    name=models.CharField(max_length=30)
    age=models.IntegerField()
    def __str__(self):
        return f'id={self.id},name={self.name},age={self.age}'
def update_user(request):
    # res = User.objects.get(id=1)
    # res.name = '笑意'
    # res.save()
    #不能用get
    User.objects.filter(name='铵盐').update(age=40)

    return HttpResponse('数据查询成功')
```

查找数据

```python
def select_user(request):
    res = User.objects.filter(name='铵盐')#能查询多条数据
    res1 = User.objects.get(name='铵盐')#只允许有单条数据
    # res = User.objects.all()
    print(res)
    return HttpResponse('数据查询成功')
```

删除数据

```python
def delete_user(request):
    # User.objects.filter(id=2).delete()
    User.objects.all().delete()
    return HttpResponse('所有数据删除成功')
```

