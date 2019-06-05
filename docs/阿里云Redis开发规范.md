#### [阿里云Redis开发规范](https://yq.aliyun.com/articles/531067)
1. 【强制】：拒绝bigkey(防止网卡流量、慢查询)

string类型控制在10KB以内，hash、list、set、zset元素个数不要超过5000。

反例：一个包含200万个元素的list。

非字符串的bigkey，不要使用del删除，使用hscan、sscan、zscan方式渐进式删除，同时要注意防止bigkey过期时间自动删除问题(例如一个200万的zset设置1小时过期，会触发del操作，造成阻塞，而且该操作不会不出现在慢查询中(latency可查))，查找方法和删除方法

2. 禁用命令

禁止线上使用keys、flushall、flushdb等，通过redis的rename机制禁掉命令，或者使用scan的方式渐进式处理。

3. [阿里云redis大key搜索工具](https://yq.aliyun.com/articles/117042?spm=a2c4e.11153940.blogcont531067.15.fdaf744alyNEWx)
4. [热点key寻找(内部实现使用monitor，所以建议短时间使用)
](https://github.com/facebookarchive/redis-faina?spm=a2c4e.11153940.blogcont531067.16.fdaf744aSgYT6Q)
5. 对于必须要存储的大文本数据一定要压缩后存储

对于大文本【超过500字节】写入到Redis时，一定要压缩后存储！大文本数据存入Redis，除了带来极大的内存占用外，在访问量高时，很容易就会将网卡流量占满，进而造成整个服务器上的所有服务不可用，并引发雪崩效应，造成各个系统瘫痪！