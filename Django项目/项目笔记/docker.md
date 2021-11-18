# 一、Docker

- 开源的应用容器引擎。

- 容器是完全使用沙箱（网络编程虚拟执行环境）机制，相互之间不会有任何接口，独立的。

- 开发者可以打包应用以及依赖到一个可移植的镜像（光盘）中，然后发布在任何流行的系统的机器中

- Docker是一个虚拟软件，虚拟执行环境-容器技术，每一个执行环境就是相互独立的

- 开发一个项目，需要很多中间件，依赖程序，开发完了就会要交付和部署。于是可以封装打包。

- docker可以制作一个镜像文件，虚拟一个环境，在这个环境里面有依赖程序，打包。下载docke镜像，运行就可以了

# 二、docker的组成

1.dockerclient客户端

2.docker daemon守护进程

3.docker image 镜像

4.docker container容器

# 镜像

- image就类似于一个模板，可以通过模板可以创建容器服务，通过这个镜像可以创建多个容器
- 轻量级可执行的独立软件包
- 包含代码程序库环境变量配置文件等
- 从远程仓库中下载，朋友拷贝，自己制作

#### 容器

- 虚拟环境，独立运行，通过镜像来创建的

#### 仓库

- 线上仓库，线上存放镜像的地方
- docker hub配置加速：阿里云镜像加速

#### docker作用

- 让应用更快速的交付和部署
- docke和虚拟机技术不同，容器化，容器没有自己的内核·，用的是宿主机的内核

# 三、docker的使用

#### 1.安装

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun	#ubantu安装

sudo docker version #查看版本，测试是否安装成功
```

#### 2.使用

1. 删除安装包

   ```shell
   sudo apt-get purge docker-ce
   ```

   

2. 删除镜像、容器、配置文件等

   ```shell
   sudo rm -rf /var/lib/docker
   ```

3. 去阿里云找到容器镜像加速服务，选择镜像工具，并进行配置

4. docker run 命令：如果本机有镜像则执行，没有则去dockerhub去找，有则下载与运行，无则报错

   ```shell
   docker run hello-world
   
   #帮助命令
   docker 命令 --help
   docker info #查看docker的具体信息。
   ```

#### 3.镜像命令

```shell
#列出镜像——docker images：仓库源，标签代表版本，唯一ID，创建时间，大小
	docker images -q#只列出镜像的ID
	docker images -a#列出详细的信息
#查找镜像——Docker Hub 搜索，
			docker search mysql --filter=stars=2000#指定点赞数高于2000的文件
#拉取镜像下载：docker pull mysql——默认的就是最新的
			docker pull mysql:5.7#——指定版本下载，已经存在的则不会拉取
#删除镜像：
			docker rmi -f #镜像ID——删除指定的镜像
			docker rmi -f `docker images -aq`#——删除所有的镜像
```

#### 4.容器命令

```shell
#新建容器
	docker run [可选参数] image 标识
	--name 给容器命名
	-d 后台运行
	-it 交互式运行，进入容器内，开机并启动进入终端
		-i 交互式
		-t 终端
	-p端口转发，指定容器的端口
		-p 主机端口：容器端口
	-P 随机端口
	docker run -it 容器 /bin/bash:进入容器的终端
#查看容器状态	
	docker run -d --name ubuntu01 ubuntu
	docker ps -a :查看所有的容器的状态
	docker ps:判断当前正在进行的容器
	docker ps -n=1:查看最近创建的1个容器
#启动和停止容器
	docker start ID:启动容器
	docker restart ID :重启容器
#容器的进入
	docker start ID:开启容器
	docker attach ID ：进入容器，exit退出后会关闭容器的运行
	docker exec -it ID /bin/bash:进入容器，exit退出后不会关闭容器
#查看容器内部的进程
	docker top ID:查看容器内部的进程
#停止容器
	docker stop:暂停，内存不会释放相当于注销
	docker kill:杀掉容器，内存释放，相当于关机
#删除容器
	docker rm 容器ID	#删除容器
	docker rm -f `docker images -aq`#删除所有容器
```

- docker run会一直创建容器，如果已经创建了，之后要进入的就是要docker start ,docker attach。

#### 5.主机与容器文件进行传输

```shell
docker cp ubuntu01:/root/test.py /home/bd	#ubuntu01容器中test.py文件传输到本机上面。
docker cp /home/bd/PythonEnv/ ubuntu01:/root #主机上传到虚拟机上的
```

#### 6.容器数据卷

- 数据在容器中，删除容器，数据必定丢失。
- 数据可以持久化到本地，需要容器之间有一个数据共享的技术
- Docker容器之间产生的数据，同步到本地。
- 容器数据卷，挂载，将容器的目录挂载到Linux上

##### 6.1使用容器卷

```shell
docker run -it --name ubuntu02 -v 主机目录：虚拟机要同步的目录 ubuntu /bin/bash
```

- 如果忘记同步到哪一个文件夹里就使用 docker inspect ubuntu02

```shell
"Mounts": [
            {
                "Type": "bind",
                "Source": "/home/docker/ubuntu02",#这就是那个资源目录
                "Destination": "/home",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
```

##### 6.2mysql同步数据

```shell
docker pull mysql:5.7
docker images
docker run -d --name mysql01 -p 3316:3306(端口转发) -v 主机目录:/etc/mysql/conf.d -v 主机目录:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=超级用户的密码 mysql(镜像)
```

##### 6.3具名和匿名挂载

- 匿名挂载
  - -v 容器内路径——自动生成同步路径
  - docker volume ls ——查看数据卷的情况
  - 默认在/var/lib/docker/volumes路径下创建容器卷
  - 文件名是随机生成的

- 具名
  - -v 卷名：容器内路径
  - 文件名不是随机生成的
- 指定路径挂载
  - -v  宿主机路径：容器内路径

- 挂在后面加上:ro（只读），rw(只写)