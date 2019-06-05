## ZooKeeper的安装
zookeeper是一种分布式协调服务，选用了它，用于维护和监控存储的数据的状态变化。通过监控这些数据状态的变化，从而可以达到基于数据的集群管理。
下载zookeeper安装包 [zookeeper-3.4.10](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.10/)下载，然后进入到conf目录下，新建一个zoo.cfg
```
#tickTime: zookeeper中使用的基本时间单位，毫秒值。
#dataDir: 数据目录，即自己下载的zookeeper的data目录
#dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和#dataDir相同的设置
#clientPort: 监听client连接的端口号。

tickTime=2000
dataDir= /usr/local/zookeeper-3.4.10/data 
dataLogDir=/usr/local/zookeeper-3.4.10/logs
clientPort=2181
```
添加以上配置并保存后，进入zookeeper-3.4.10/bin目录下 执行./zkServer.sh start ，zookeeper启动成功

## dobbo admin安装

 1. [下载](https://blog.csdn.net/tian_wai_lai_ke/article/details/79332346),或者到[GitHub](https://github.com/apache/incubator-dubbo)进行下载
 2. tar -xzvf dubbox-2.8.4
 3. 修改dubbo-admin下的dubbo.properties,将dubbo.registry.address改为zookeeper地址
 4. 打war包
```
cd dubbox-2.8.4/dubbo-admin
mvn install -Dmaven.test.skip=true
```
 5. 部署dubbo-admin到Tomcat下
 6. Tomcat 启动，访问：http://localhost:8080/dubbo-admin-2.8.4/，默认用户名和密码均为root
### 安装遇到的问题及解决
#### 问题
```
Could not resolve dependencies for project com.alibaba:dubbo-admin:war:2.8.4-SNAPSHOT
```
#### 分析
原因是maven库里没有该dubbo的jar包，修改把dubbo-admin项目的pom.xml的dubbo-admin，可以在maven中心仓库查询，修改后重新build即可

