docker --version

docker info

docker ps

docker logs

docker stop 363dccf6f6ba

**docker images** 来列出本地主机上的镜像

使用版本为15.10的ubuntu系统镜像来运行容器时:docker run -t -i ubuntu:15.10 /bin/bash 

当我们在本地主机上使用一个不存在的镜像时 Docker 就会自动下载这个镜像。如果我们想预先下载这个镜像，我们可以使用 docker pull 命令来下载它

查找镜像:

我们可以从 Docker Hub 网站来搜索镜像，Docker Hub 网址为： *https://hub.docker.com/*

我们也可以使用 docker search 命令来搜索镜像。比如我们需要一个httpd的镜像来作为我们的web服务。我们可以通过 docker search 命令搜索 httpd 来寻找适合我们的镜像。

docker search httpd



[Docker部署SpringBoot](http://www.ityouknow.com/springboot/2018/03/19/spring-boot-docker.html)

mvn package docker:build

docker run -p 8080:8080 -t springboot/spring-boot-docker



以守护进程的方式启动

docker run -d -p 8080:8080 -t springboot/spring-boot-docker



docker进入mysql容器

docker exec -it mysql bash



docker运行rabbitmq

```shell
docker run -d -p 5672:5672 -p 15672:15672 --name rabbitmq rabbitmq:management
```

docker创建redis

```shell
docker run -p 6379:6379 -d --env HOST_IP=127.0.0.1 --name redis redis:5
```



