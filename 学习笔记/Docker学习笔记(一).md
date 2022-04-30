---
title: Docker学习笔记(一)
tags: Docker
categories: 学习笔记
description: Docker第一部分学习笔记，主要介绍了Docker基本知识、安装和使用、基本命令、以及容器化技术和镜像原理
---

## 1、Docker概述

### 1.1、出现场景

一款产品：开发-->上线 两套环境！应用环境，应用配置！

开发--->运维，问题：版本更新，导致服务不可用，对运维考验十分大

环境配置是十分的麻烦。每一个机器都要配属环境，费时费力！

**发布一个项目(jar+(redis mysql jdk es))，项目能不能带上环境安装打包**

之前在服务器配置一个应用的环境Redis MySQL ....配置很麻烦，windows-->linux

传统：开发jar，运维来做

现在：开发打包部署上线，一套流程做完

![image-20210302105607886](http://fl.ljuuu.com/image-20210302105607886.png)

以上问题Docker能解决！

Docker的思想来自于集装箱

隔离：Docker核心思想，打包装箱，每个箱子是互相隔离！

### 1.2、Docker功能

- Docker与虚拟机技术的不同：
  - 传统虚拟机虚拟出一套硬件，运行一个完整的操作系统
  - 容器化技术不是模拟一个完整的操作系统
  - 每个容器间相互隔离，每个容器都有一个属于自己的文件系统，互不影响
- DevOps(开发、运维)：
  - **应用更快速的交付和部署**，Docker打包镜像发布测试，一键运行
  - **更便捷的升级和扩缩容**，部署更搭积木一样
  - **更简单的系统运维**，容器化之后我们的开发，测试环境都是高度一致的
  - **更高效的计算资源利用**，服务器的性能可以被压榨到极致

## 2、Docker安装

### 2.1、Docker名词

![点击查看源网页](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=793314303,480081697&fm=26&gp=0.jpg)

**镜像(image)：**docker镜像好比一个模板，可以通过这个模板来创建容器服务，tomcat镜像-->run-->tomcat01容器(提供服务器)，通过这个镜像可以创建多个容器(最终服务运行或者项目运行就是在容器中)

**容器(container)：**Docker利用容器技术，独立运行一个或者一个组应用，通过镜像来创建的

启动，停止，删除，基本命令！

可以把容器理解为一个简易的linux系统

**仓库(repository)：**仓库就是存在镜像的地方，分为公有仓库和私有仓库

### 2.2、Docker安装

- 卸载旧的docker

  ```bash
  yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-engine
  ```

- 安装需要的安装包

  ```bash
  yum install -y yum-utils
  ```

- 设置镜像仓库

  ```bash
  yum-config-manager \
      --add-repo \
      #https://download.docker.com/linux/centos/docker-ce.repo  #默认是国外的仓库，十分慢，尽量使用国内的
     http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  ```

- 更新软件包索引

  ```bash
  yum makecache fast
  ```

- 安装docker相关内容：ce社区版，ee企业版

  ```bash
  yum install docker-ce docker-ce-cli containerd.io
  ```

- 启动docker

  ```bash
  systemctl start docker
  ```

- 查看docker版本

  ```bash
  docker version
  ```

- 启动hello-world测试

  ```bash
  docker run hello-world
  ```

- 查看下载的hello-world镜像

  ```bash
  docker images
  ```

- 若要卸载docker

  ```bash
  yum remove docker-ce docker-ce-cli containerd.io
  rm -rf /var/lib/docker  #docker默认工作路径
  ```

### 2.3、配置阿里云镜像加速

- 登录阿里云，找到镜像加速地址

- 设置密码

  ```
  ******
  ```

- 找到镜像加速器

  ![image-20210302184015817](http://fl.ljuuu.com/\image-20210302184015817.png)

- 配置使用

  ```bash
  sudo mkdir -p /etc/docker
  sudo tee /etc/docker/daemon.json <<-'EOF'
  {
    "registry-mirrors": ["https://21iz339s.mirror.aliyuncs.com"]
  }
  EOF
  sudo systemctl daemon-reload
  sudo systemctl restart docker
  ```

### 2.4、Docker执行流程

- run的执行过程

![image-20210302184551342](http://fl.ljuuu.com//image-20210302184551342.png)

### 2.5、Docker底层原理

docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问！

DockerSever接收到Docker-Client的指令，就会执行这个指令

![image-20210302185207660](http://fl.ljuuu.com//image-20210302185207660.png)

**为什么Docker会比虚拟机快？**

1. Dokcer有着比虚拟机更少的抽象层
2. docker利用的是宿主机的内核，vm需要的是guest os

所有说，新建一个容器的时候，docker不需要向虚拟机一样重新加载一个操作系统内科，避免引导。

## 3、Docker命令

### 3.1、帮助命令

```bash
docker version  #显示docker的版本信息
docker info  #显示docker更详细的信息
docker 命令 --help  #帮助命令
```

帮助文档的地址：https://docs.docker.com/reference/

### 3.2、镜像的基本命令

- `docker images`：查看本机的所有镜像

- `docker search mysql`：搜索mysql镜像

- `docker pull mysql:5.7`：下载5.7的mysql

- `docker rmi -f 镜像id`：删除指定id的镜像

### 3.3、容器的基本命令

说明：有了镜像才可以创建容器，linux，下载一个centos镜像来测试学习

- `docker pull centos`：下载一个centos

- `docker run [可选参数] image`：运行一个镜像
  - --name="Name"：容器名字  tomcat01  tomcat02，用来区分容器
  - -d：后台方式运行
  - -it：使用交互方式运行，进入容器查看内容
  - -p
    - -p ip：主机命令
    - -p 主机端口：容器端口(常用)
    - -p 容器端口
  - -p：随机指定端口

- `docker ps`：列出所有正在运行的容器
  - -a：列出当前正在运行的容器+历史运行过的容器
  - -n=?：列出最进创建的几个容器
  - -q：显示容器的编号
- `docker rm 容器id`：删除指定id的容器
- `docker start 容器id`：启动容器
- `docker restart 容器id`：重启容器
- `docker stop 容器id`：停止容器
- `docker kill 容器id`：强制停止容器

测试：

- 启动并进入容器**(容器就是一个小型的服务器)**

  ![image-20210302194016793](http://fl.ljuuu.com//image-20210302194016793.png)

- 容器内部文件：

  ![image-20210302194047986](http://fl.ljuuu.com//image-20210302194047986.png)

- 使用命令`exit`退出当前容器，并返回主机

- 使用Ctrl+p+q返回主机，容器依旧运行

### 3.4、常用的其他命令

- `docker run -d centos`：通过后台启动容器，**注意：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止！**
- `docker logs -f -t --tail 10 容器id`：查看日志
- `docker top 容器id`：查看容器的进程信息
- `docker inspect 容器id`：查看容器的元数据
- `docker exec -it 容器id`：进入某个容器，进入一个容器开启一个新的命令行
- `docker attach 容器id`：进入某个容器，进入的是当前的命令行
- `docker cp 容器id:容器内文件路径 主机路径`：把容器中文件拷贝到主机

### 3.5、安装Nginx

- 搜索和安装nginx，在docker hub搜索Nginx的版本

  ```bash
  docker pull nginx
  ```

- 运行nginx。**端口映射，外网可以通过3344访问服务器内里面的容器(80)**

  ```bash
  docker run -d --name nginx01 -p 3344:80 nginx
  ```

- 本地自测

  ```bash
  curl localhost:3344
  ```

### 3.6、安装Tomcat

- 搜索tomcat

  ```
  docker run -it --rm tomcat:9.0  #一般用来测试，用完就删除
  docker pull tomcat:9.0 #下载tomcat 用完不会删除
  ```

- 运行tomcat。

  ```bash
  docker run -d -p 3355:8080 --name tomcat01 tomcat
  ```

- 进入容器，发现问题：阿里云镜像的原因，默认是最小的镜像，所以把所有不必要的东西剔除了

  - linux命令少了
  - 没有webapp

### 3.7、可视化面板安装

**先使用portainer，后面会使用Rancher**

- 下载使用

  ```bash
  docker run -d -p 8088:9000 \
  --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

- 外网访问。

- 先登录账户，设置密码

- 选择`local`进入就行了

## 4、Docker镜像

### 4.1、什么是镜像

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包成为一个docker镜像，就可以直接跑起来

如何得到镜像：

- 从远程仓库下载
- 拷贝
- 自己制作

### 4.2、镜像加载原理

- UnionFS，联合文件系统

### 4.3、Commit镜像

自己制作一个镜像

```bash
docker commit 提交让其成为一个新的副本

#命令和git类似
#官方的tomcat的webapps没有文件，我们想要发布一个有webapps的镜像
#得到自己的镜像
cp -r /webapps.dist/* /webapps
#commit自己的镜像
docker commit -a="ferron" -m="add webapps app" 容器id tomcat02 tomcat02:1.0
#查看自己的镜像
docker images
```
