Docker入门基础

## 什么是Docker:
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

## Docker思想：
+ 集装箱
+ 标准化
  + 运输方式
  + 存储方式
  + API接口
+ 隔离
## Docker解决了什么问题？
- Docker解决了运行环境和配置问题，方便发布，也就方便做持续集成。
- 更轻量的虚拟化，节省了虚拟机的性能损耗

## Docker运行的过程:
去仓库把镜像拉取到本地，然后用一条命令把镜像运行起来变成容器

## Docker核心技术
包括: Docker镜像、Docker容器、Docker仓库

## 使用 yum 安装Docker（CentOS 7下）
从 2017 年 3 月开始 docker 在原来的基础上分为两个分支版本: Docker CE 和 Docker EE。Docker CE 即社区免费版，Docker EE 即企业版，强调安全，但需付费使用。
本文介绍 Docker CE 的安装使用。
1. Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。
```
uname -r
```
2. 移除旧的版本：
```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
3. 安装一些必要的系统工具：
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
4. 添加软件源信息：
```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
5. 更新 yum 缓存：
```
sudo yum makecache fast
```
6. 安装 Docker-ce：
```
sudo yum -y install docker-ce
```
7. 启动 Docker 后台服务
```
sudo systemctl start docker
```
8. 测试运行 hello-world
```
docker run hello-world
```
由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。返回以下信息证明成功
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete 
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
## Docker常用命令
- 查看本机所有的镜像列表
```
docker images
```
- pull镜像
```
docker pull hello-world
```
- 查看有哪些镜像在运行
```
docker ps
```
- 进入容器内部
```
docker exec -it f4 bash
```
- 查看已经创建的容器
```
docker ps -a
```
- 删除容器
```
a3b29fb2c795 为容器id
docker rm a3b29fb2c795
```
- 删除镜像
```
b3f057b66c08 为镜像id
docker rmi b3f057b66c08
```
- 重启docker服务
```
systemctl restart docker
```
## 运行nginx 镜像
- 持久运行的容器
- 前台挂起 & 后台运行
- 进入容器内部
1. 进入网易蜂巢镜像中心找到nginx的版本
2. 运行命令下载
```
docker pull hub.c.163.com/library/nginx:latest
```
3. 运行nginx
- 前台启动
```
docker run hub.c.163.com/library/nginx
```
- 后台启动（推荐）
```
docker run -d hub.c.163.com/library/nginx
```

## Docker网络
+ 网络类型
    + Bridge(默认)
    + Host
    + None
+ 端口映射

启动nginx,并对外提供服务
```
docker run -d -p 8080:80 hub.c.163.com/library/nginx
```
验证端口状态
```
netstat -na | grep 8080
```

浏览器输入网址验证是否可访问
```
http://yourIp:8080/
```

## docker 中部署springBoot项目
1. 创建Dockerfile
```
mkdir mydocker
touch Dockerfile
```
2. 添加以下内容到Dockerfile
```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD springboot.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```
3. 上传springboot的jar文件 到当前(mydocker)文件夹下
4. 创建docker镜像(springboot为要创建的镜像名)
```
docker build -t springboot .
```
5. 创建容器并允许镜像
```
docker run -d -p 8080:8080 springboot
```
6. 部署构建成功，通过浏览器或postman可验证springboot项目是否部署成功

## 其它
[可能是把Docker的概念讲的最清楚的一篇文章](https://zhuanlan.zhihu.com/p/38552635)
