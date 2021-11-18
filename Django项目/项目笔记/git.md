# 一、git的使用

### 1.git基本命令

- **ubuntu上git下载——sudo apt-get install git**
- git --version——查看git版本。git init——仓库初始化
- git config user.name "你的名字"——设置你的仓库的名字
- git config user.email "你的邮箱"——设置你的仓库的邮箱，姓名和信息必须配置好才能提交。
- git status——查看仓库当前状态。git add 要添加的文件，git add .提交所有的文件，将要添加的文件存在暂存区。
- git commit -m "备注信息"——提交到工作区。git log或者git reflog——查看提交的历史信息

### 2.流程

**提交：**

1. git status
2. git add
3. git commit -m "备注信息"
4. git reflog

**修改**

1. git status
2. git commit -am "新的备注信息"
3. git reflog

### 3.回退版本

- git reset --hard HEAD^——回退到上一个版本
- git reset --hard 版本号——回退到特定的版本
- git reset 文件名——将暂存区的文件取消提交。

### 4.分支及合并

- git branch——查看自己所在的分支。
- git branch -v ——查看自己所在的分支的详细信息。分支，版本号，备注信息
- git branch 分支名——创建新的分支，git checkout 分支名——切换分支。
-  git merge 分支名——将分支合并到主分支里面去

如果合并的产生了冲突，就会讲两个分支的内容写入到工作区，而不是git仓库里去。然后自己去决定要不要保留哪一块，修改好了之后就提交到仓库里面去。

# 二、远程仓库

- 连接远程仓库——ssh-keygen -t rsa -C "姓名@邮箱"——生成密钥
- cat ~/.ssh/id_rsa.pub——查看生成的密钥，复制生成的密钥，添加到仓库的公钥中。
- ssh -T git@gitee.com ——查看是否连接成功
- git clone ——创建本地仓库，绑定远程仓库， 
- git remote rm 仓库名——删除本地仓库名
- git remote add 仓库名 网址——添加仓库
- git push 仓库 分支名——将本地仓库的东西更新到远程，git pull 仓库 分支名——将远程仓库的东西更新到本地。
- git branch——查看分支名，git branch -d 分支名——删除分支

# 三、前后端分离

### 1.后端

- 数据传输一般是json数据——本质上是一个字符串，是轻量级的数据交换格式，可以在不同的数据之间做转换。

- python可以做小程序或者app的后端功能接口。程序的开发不只是一门语言独立开发出来的。

### 2.前端

- 可以发起任意形式的请求。

### 3.接口

- 将开发好的模型试图路由，提供调用需要使用的信息，能够提供给其它语言使用调用，称之为接口，又称API。
- 提供访问地址，请求方式，参数，给第三方调用实现该功能。00000

# 四、RESTful：接口设计规范

**发起http/https协议请求需要的信息**

- 请求地址

  - ```
    要操作的事物的名词复数形式——表名尽量不要暴露
    例如：学生管理系统，
    1.获取所有学生信息——请求方式是GET,路由-请求地址/students，返回数据[]
    2.增加学生——请求方式：POST，请求地址 /students,返回增加的学生信息{}
    3.查找特定学生——请求方式：GET，请求地址 /students/1，返回学生信息{}
    4.修改指定的学生信息——请求方式：PUT，请求地址 /students/1，返回修改后的信息{}
    5.删除指定学生的信息——请求方式：DELETE，请求地址 /students/1，返回空，204
    ```

    

- 请求方式

  - ```
    GET——select——从服务器获取资源——查询
    POST——create——从客户端提交数据给服务器——添加
    PUT——update——修改
    DELETE——delete——删除
    ```

- 请求参数

- 返回的数据格式——json

#  五、django rest framework

- 一个内置在django里面的子应用，可以快速开发REST API接口应用，就是django的一个插件。是一个APP。
- 序列化器
  - 序列化：模型转json
- 丰富的类视图

# 六、restframework的使用

- create database `数据库名 `  charset = 'utf8'  
- 分配权限——grant all privileges on drfstudy.* to 'drfstudyu'@'%' identified by 'qwe123';给drystudyu这个人分配drfstudy数据库的所有权限，并且允许远程登陆
- flush privileges——刷新权限。

settings文件的配置：

```python
DATABASES = {
    'sqlite3': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    },
    'default':{
        'ENGINE': 'django.db.backends.mysql',
        'OPTIONS':{
            'read_default_file':'config/dbs/mysql.cnf'
        }
    }
    #重新定义数据库连接配置文件

}
```

在.gitignore文件中指定不会上传的文件名

表格式创建

```python
from django.db import models

# Create your models here.

class Student(models.Model):
    SEX_CHOICES = (
        (0,'女'),
        (1,'男')
    )
    name = models.CharField(max_length=30,verbose_name='姓名')
    age = models.IntegerField(null=True,verbose_name='年龄',blank=True)
    sex = models.IntegerField(null=True,verbose_name='性别',blank=True,choices=SEX_CHOICES)
    create_time = models.DateTimeField(verbose_name='创建时间',auto_now_add=True)
    update_time = models.DateTimeField(verbose_name='修改时间',auto_now=True)
    is_delete = models.BooleanField(default=False,verbose_name='逻辑删除')

    class Meta:
        db_table = 'student'
```

序列化器

```python
"""
序列化器：
    本质上是一个类，和模型类差不多的类
    用于json格式与对象模型的转换以及校验数据
    定义的属性就是参与转换的字段
"""
from rest_framework import serializers
class StudentSerializer(serializers.Serializer):
    SEX_CHOICES = (
        (0, '女'),
        (1, '男')
    )
    id = serializers.IntegerField(label='ID',read_only=True)
    name = serializers.CharField(max_length=30, label='姓名')
    age = serializers.IntegerField(label='年龄', required=False)
    sex = serializers.IntegerField(required=False, label='性别',  choices=SEX_CHOICES)
    create_time = serializers.DateTimeField(label='创建时间', auto_now_add=True)
    update_time = serializers.DateTimeField(label='修改时间', auto_now=True)
    is_delete = serializers.BooleanField(default=False, label='逻辑删除')
```



```python
# -*- coding: utf-8 -*-
# @Time: 2021/10/28 11:03
# @Author: 枫无痕
# @Email: thesky_27@163.com
# @File: serializers.py
# @Software: PyCharm

"""
序列化器：
    本质上是一个类，和模型类差不多的类
    用于json格式与对象模型的转换以及校验数据
    定义的属性就是参与转换的字段
"""
from .models import Student

from rest_framework import serializers
class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        # fields = '__all__'  #指定映射字段，__all__指定所有字段
        # fields = ['id','name','age']    #映射指定字段
        # exclude = ['id']    #映射除了id字段外的所有字段
        extra_kwargs={
            'age':{'min_value':0,'max_value':200},
        }   #改变字段的规则

        # 追加额外特定的校验逻辑,对谁校验就下划线之后跟对应的字段名
        def validate_name(self,value):
            if 'python' not in value.lower():
                raise serializers.ValidationError('name error')
            return value

        # 对象级别的额外校验
        def validate(self,data):
            if 'python' not in data['name'].lower():
                raise serializers.ValidationError('name error')

            return data




    # SEX_CHOICES = (
    #     (0, '女'),
    #     (1, '男')
    # )
    # id = serializers.IntegerField(label='ID',read_only=True)
    # name = serializers.CharField(max_length=30, label='姓名')
    # age = serializers.IntegerField(label='年龄', required=False)
    # sex = serializers.IntegerField(required=False, label='性别',  choices=SEX_CHOICES)
    # create_time = serializers.DateTimeField(label='创建时间', auto_now_add=True)
    # update_time = serializers.DateTimeField(label='修改时间', auto_now=True)
    # is_delete = serializers.BooleanField(default=False, label='逻辑删除')



```

# 七、使用git应注意的事项

- 当线上仓库更新时，要先pull拉取线上仓库的内容，在进行push推送
- 在仓库里面添加公钥只有可读的权限，如果要写入，就要添加个人模板
