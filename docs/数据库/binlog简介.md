### 1、什么是binlog

​        binlog是一个二进制格式的文件，用于记录用户对数据库**更新的****SQL语句****信息**，例如更改数据库表和更改内容的SQL语句都会记录到binlog里，但是对库表等内容的**查询不会记录**。

   默认情况下，binlog日志是二进制格式的，不能使用查看文本工具的命令（比如，cat，vi等）查看，而使用mysqlbinlog解析查看。

### 2.binlog的作用

​      当有数据写入到数据库时，还会同时把更新的SQL语句写入到对应的binlog文件里，这个文件就是上文说的binlog文件。使用mysqldump备份时，只是对一段时间的数据进行全备，但是如果备份后突然发现数据库服务器故障，这个时候就要用到binlog的日志了。

**主要作用是用于数据库的主从复制及数据的增量恢复。**

### 3、如何开启binlog日志功能

#### 3.1 查看是否开启

```
mysql> show variables like "log_bin";
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | OFF   |
+---------------+-------+
1 row in set (0.00 sec)
```

#### 配置为开启

在mysql的配置文件my.cnf中，增加log_bin参数即可开启binlog日志，也可以通过赋值来指定binlog日志的文件名，实例如下：

```
[mysqld]
# bin log 配置
server-id=1
log-bin=/var/lib/mysql/mysql-bin
```

### 4、查看bin log

```sheel
[root@tsinghua-bak mysql]# pwd
/var/lib/mysql
[root@tsinghua-bak mysql]# ll
总用量 176248
-rw-rw---- 1 mysql mysql       56 11月 25 2018 auto.cnf
drwx------ 2 mysql mysql     4096 2月  20 14:17 datong
-rw-rw---- 1 mysql mysql 79691776 8月   9 16:31 ibdata1
-rw-rw---- 1 mysql mysql 50331648 8月   9 16:31 ib_logfile0
-rw-rw---- 1 mysql mysql 50331648 8月   9 16:31 ib_logfile1
drwx------ 2 mysql mysql     4096 2月  21 10:37 jforum
drwx------ 2 mysql mysql    20480 7月  22 17:29 ksxing
drwx------ 2 mysql mysql     4096 11月 27 2018 ksxing_pay
drwx------ 2 mysql mysql     4096 11月 25 2018 mysql
-rw-rw---- 1 mysql mysql      793 8月   9 16:30 mysql-bin.000001
-rw-rw---- 1 mysql mysql      143 8月   9 16:31 mysql-bin.000002
-rw-rw---- 1 mysql mysql      120 8月   9 16:31 mysql-bin.000003
-rw-rw---- 1 mysql mysql       96 8月   9 16:31 mysql-bin.index
srwxrwxrwx 1 mysql mysql        0 8月   9 16:31 mysql.sock
drwx------ 2 mysql mysql     4096 11月 25 2018 performance_schema
drwx------ 2 mysql mysql    20480 3月   6 16:02 pukou_ksx
drwx------ 2 mysql mysql     4096 2月  19 14:11 qhtest
drwx------ 2 mysql mysql    20480 7月  18 10:20 qinghua
drwx------ 2 mysql mysql    12288 3月  19 13:40 yuantong
[root@tsinghua-bak mysql]# mysqlbinlog --no-defaults --base64-output=DECODE-ROWS -v mysql-bin.000001
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#190809 16:17:03 server id 1  end_log_pos 120 CRC32 0x162e71d8 	Start: binlog v 4, server v 5.6.42-log created 190809 16:17:03 at startup
ROLLBACK/*!*/;
# at 120
#190809 16:26:47 server id 1  end_log_pos 203 CRC32 0x782e59e6 	Query	thread_id=604	exec_time=0	error_code=0
SET TIMESTAMP=1565339207/*!*/;
SET @@session.pseudo_thread_id=604/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1075838976/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=45,@@session.collation_connection=45,@@session.collation_server=33/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
BEGIN
/*!*/;
# at 203
#190809 16:26:47 server id 1  end_log_pos 461 CRC32 0xaf38378d 	Query	thread_id=604	exec_time=0	error_code=0
use `ksxing`/*!*/;
SET TIMESTAMP=1565339207/*!*/;
INSERT INTO `ksxing`.`qh_score`(`id`, `surname`, `user_name`, `work_grade`, `attendance_grade`, `create_time`) VALUES (12, '苏雄伟', 'suxiognwei', 20, 50, '2019-08-23 16:26:45')
/*!*/;
# at 461
#190809 16:26:47 server id 1  end_log_pos 492 CRC32 0x83a15930 	Xid = 3798
COMMIT/*!*/;
# at 492
#190809 16:27:51 server id 1  end_log_pos 583 CRC32 0x826fe19f 	Query	thread_id=604	exec_time=0	error_code=0
SET TIMESTAMP=1565339271/*!*/;
SET @@session.time_zone='SYSTEM'/*!*/;
BEGIN
/*!*/;
# at 583
#190809 16:27:51 server id 1  end_log_pos 739 CRC32 0xc0919609 	Query	thread_id=604	exec_time=0	error_code=0
SET TIMESTAMP=1565339271/*!*/;
UPDATE `ksxing`.`qh_score` SET `attendance_grade` = 60 WHERE `id` = 12
/*!*/;
# at 739
#190809 16:27:51 server id 1  end_log_pos 770 CRC32 0xc27d30e2 	Xid = 3801
COMMIT/*!*/;
# at 770
#190809 16:30:05 server id 1  end_log_pos 793 CRC32 0xe28831ce 	Stop
DELIMITER ;
# End of log file
ROLLBACK /* added by mysqlbinlog */;
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
[root@tsinghua-bak mysql]#
```



## 参考文章

- [binlog介绍](https://www.cnblogs.com/dadonggg/p/8617826.html)

- [binlog之五：mysqlbinlog解析binlog乱码问题解密](https://www.cnblogs.com/duanxz/p/6651024.html)

- [mysqlbinlog 查看binlog时报错unknown variable 'default-character-set=utf8'](https://www.cnblogs.com/cobbliu/p/4311926.html)















