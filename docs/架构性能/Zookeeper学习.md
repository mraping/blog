分布式系统的指挥官

使用场景：

1. 数据发布订阅
2. 负载均衡
3. 命名服务
4. Master选举
5. 集群管理
6. 配置管理
7. 分布式队列
8. 分布式锁



配置文件：

1. clientPort：端口
2. dataDir：存储快照的目录
3. snapCount：



zk的特性：

- 会话
- 数据节点
- 版本
- Watcher
- ACL
- 集群角色



创建节点必须指定值：如 create /xxx xxx

节点类型：

- 持久节点：

- 持久顺序节点

- 临时节点

- 临时顺序节点

对于持久节点和临时节点，同一个znode下，名称是唯一的

持久：连接断开之后仍然存在的 