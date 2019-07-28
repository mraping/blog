很多开发人员都会遇见”MySQL: ERROR 1040: Too many connections”的异常情况，造成这种情况的**一种原因是访问量过高**，MySQL服务器抗不住，这个时候就要考虑增加从服务器分散读压力；另**一种原因就是MySQL配置文件中max_connections值过小**。

首先，我们来查看mysql的最大连接数：

```mysql
mysql> show variables like "%max_connections%";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 4000  |
+-----------------+-------+
1 row in set (0.00 sec)
```

其次，查看服务器响应的最大连接数:

```mysql
mysql> show global status like "%max_used_connections%";
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| Max_used_connections | 601   |
+----------------------+-------+
1 row in set (0.00 sec)
```

对于mysql服务器最大连接数值的设置范围比较理想的是：服务器响应的最大连接数值占服务器上限连接数值的比例值在10%以上，如果在10%以下，说明mysql服务器最大连接上限值设置过高。

### 2、如何设置这个最大连接数值

方式一：

```mysql
mysql> set global max_connections  = 256;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like "%max_connections%";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 256   |
+-----------------+-------+
1 row in set (0.00 sec)
```

方式二：修改mysql配置文件my.cnf，在[mysqld]段中添加或修改max_connections值：max_connections=128
重启mysql服务即可。

```
[root@ksxing-tsinghua ~]# cat /etc/my.cnf
[client]
default-character-set=utf8

[mysql]
auto-rehash

[mysqld]
character-set-server=utf8
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

symbolic-links=0

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
max_connections=4000
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```



**区别：**

1.通过修改配置文件，需要重启服务；而用命令修改，即时生效。

2.采用修改配置文件的方式，更稳定可靠。因为如果配置文件中有`max_connections=100`，再去用命令修改的话，一旦重启mysql服务后，会重新以配置文件中指定的连接数为准。

**总结：**

在修改最大连接数的时候会有这样一个疑问—这个值是不是越大越好，或者设置为多大才合适？这个参数的大小要综合很多因素来考虑，比如使用的平台所支持的线程库数量（windows只能支持到2048）、服务器的配置（特别是内存大小）、每个连接占用资源（内存和负载）的多少、系统需要的响应时间等。可以在global或session范围内修改这个参数。连接数的增加会带来很多连锁反应，需要在实际中避免由此引发的负面影响。





# [技术经理：求求你，别再乱改数据库连接池的大小了！](https://segmentfault.com/a/1190000019177990)