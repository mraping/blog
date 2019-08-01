### 开发使用记录

1. mysql 设置了默认值了为什么还是会报column cannot be null --> 去掉非空约束
2. SELECT_COLUMNS  用途，修改了数据库字段后避免 SELECT * 与model对应不到的问题

###隔离性等级

- 未提交读( READ UNCOMMITED)**脏读**,两个事务之间互相可见；
- 已提交读(READ COMMITED)符合隔离性的基本概念,一个事务进行时，其它已提交的事物对于该事务是可见的，即可以获取其它事务提交的数据。
- 可重复读( REPEATABLE READ)**InnoDB的默认隔离等级**。事务进行时，其它所有事务对其不可见，即多次执行读，得到的结果是一样的！
- 可串行化（SERIALIZABLE） 在读取的每一行数据上都加锁，会造成大量的锁超时和锁征用，严格数据一致性且没有并发是可使用。



SHOW VARIABLES LIKE 'autocommit';

SET AUTOCOMMIT = 1

