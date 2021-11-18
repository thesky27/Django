# 一、FastDFS分布式文件系统

### 一、简介

- 开源的轻量级分布式文件系统
- 对文件进行管理，功能包括：文件存储，文件同步，文件访问等
- 解决了大容量存储和负载均衡的问题
- fast：快速，D：分布式，F：文件，S：系统
- 使用c语言进行开发的。

### 二、构成以及服务开启

#### 2.1 FastDFS 构成

tracker：跟踪服务，可以有多个

storage：存储服务，仓库，可以有多个

分布式，可以分布在不同的地方机器联合工作。

#### 2.2 FastDFS 运行过程

1. 首先开启tracker server ,再开启storage server，storage定时向tracker server 发送状态信息。
2. 客户端上传文件请求给tracker server，它会查询可用的storage server，并返回信息和端口。
3. 然后文件上传到返回的端口和 ip 的服务器上，tracker server返回文件存储的地址，客户端再将文件存储之后的地址写入到数据库中。

- 会将 FastDFS 系统部署在nginx，没有nginx这个分布式系统和客户端就不能进行传输

### 三、docker安装fastdfs

开启tracker，storage这两个服务，并在后台挂载。

```shell
docker search fastdfs
docker pull delron/fastdfs
 docker run -dti --network=host --name traker01 -v /var/fdfs/tracker:/var/fdfs delron/fastdfs tracker
 docker run -dti --network=host --name storage01 -e TRACKER_SERVER=10.0.2.15:22122  -v /var/fdfs/storage:/var/fdfs delron/fastdfs storage#注意tracker_server后面的端口是你本机的ip加上22122这个端口
```

执行这些命令后，打开网站127.0.0.1:8888会看到nginx的页面,就表示安装成功

### 四、安装相关依赖

```shell
pip install requests -i https://pypi.douban.com/simple/
pip install mutagen -i https://pypi.douban.com/simple/
pip install py3Fdfs -i https://pypi.douban.com/simple/
```

在交互模式下测试上传的图片，访问127.0.0.1:8888/group1/M00/00/00/CgACD2GTcdeAKo66AAA3dPjZmls789.png就可以看到

```shell
(promote) bd@DF:~/git_workspace/drfstudy/promote$ python
Python 3.6.9 (default, Jan 26 2021, 15:33:00)
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from fdfs_client.client import Fdfs_client,get_tracker_conf
>>> client = Fdfs_client(tracker_path)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'tracker_path' is not defined
>>> tracker_path = get_tracker_conf('utils/fastdfs/client.conf')
>>> client = Fdfs_client(tracker_path)
>>> client.upload_by_filename('/home/bd/logo.png')
{'Group name': b'group1', 'Remote file_id': b'group1/M00/00/00/CgACD2GTcdeAKo66AAA3dPjZmls789.png', 'Status': 'Upload successed.', 'Local file name': '/home/bd/logo.png', 'Uploaded size': '13.86KB', 'Storage IP': b'10.0.2.15'}
>>>
```

之后 django 项目代码在https://gitee.com/thesky_27/drfstudy.git 中

