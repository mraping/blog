## 官网地址:http://hbase.apache.org/
## HBase的应用场景及特点
### HBase能做什么？
- 海量数据存储
- 准实时的查询

### 实际业务场景中的应用
- 交通(GPS信息)
- 金融
- 电商
- 移动

### 特点
- 容量大：HBase单表可以有百亿行、百万列，数据矩阵横向和纵向两个维度所支持的数据量级都非常具有弹性
- 面向列：HBase是面向列的存储和权限控制，并支持独立检索。列式存储，其数据在表中是按照某列存储的，这样在查询只需要少数几个字段的时候，能大大减少读取的数据量。
- 多版本：HBase每一个列的数据存储有多个Version
- 稀疏性：为空的列并不占用存储空间，表可以设计的非常稀疏
- 扩展性：底层依赖于HDFS
- 高可靠性：WAL机制保证了数据写入时不会因为集群异常而导致写入数据丢失：Replication机制保证了在集群出现严重的问题时，数据不会发生丢失或损坏。而且HBase底层使用HDFS HDFS本身也有备份。
- 高性能：底层的LSM数据结构和Rowkey有序排列等架构上的独特设计，使得HBase有着很高的写入性能。region切分、主键索引和缓存机制使得HBase在海量数据下具备一定的随机读取性能，该性能针对Rowkey的查询能够达到毫秒级别。

## HBase的概念
### 概念
Apache HBase:tm: is the Hadoop database, a distributed, scalable, big data store.</br>

Use Apache HBase:tm: when you need random, realtime read/write access to your Big Data. This project's goal is the hosting of very large tables -- billions of rows X millions of columns -- atop clusters of commodity hardware. Apache HBase is an open-source, distributed, versioned, non-relational database modeled after Google's Bigtable: A Distributed Storage System for Structured Data by Chang et al. Just as Bigtable leverages the distributed data storage provided by the Google File System, Apache HBase provides Bigtable-like capabilities on top of Hadoop and HDFS.

### 如何选择合适HBase的版本
- 官网版本：http://archive.apache.org/dist/hbase/
- CDH版本(稳定)：http://archive.cloudera.com/cdh5/

## HBase架构体系与设计模型
### 列簇
- 一张表的列簇不会超过5个
- 每个列簇中的列数没有限制
- 列只有插入数据后存在
- 列在列簇中是有序的

### HBase表与关系型数据库表结构的对比
- 列动态增加
- 数据自动切分
- 高并发读写
- 不支持条件查询

## HBase的安装部署
需提前安装好HDFS服务和Zookeeper
## HBase shell 的使用


## 相关命令
-  sbin/hadoop-daemon.sh start datanode
-  sbin/hadoop-daemon.sh start namenode
-  bin/hdfs namenode -format
