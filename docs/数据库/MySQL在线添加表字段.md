本文以一次在项目上线中出现的故障进行展开

### 线上故障记录及分析

#### 故障描述

直接在线上添加字段，执行耗时较长，在主库添加字段后同步到从库时也较慢，同时导致线上部分服务不可用

#### 前提说明

- 线上表的数据量：4581993
- 数据库版本：MySQL 5.6
- 表内数据不时有新增和更新

#### 故障分析

MySQL在5.6版本之前，线上大表新增字段是需要停机的，因为这时候mysql会锁表，任何写操作都会被阻塞。

不过如果能保证数据库只读就没问题。

直接先在主上操作，那么会导致主从延迟很大(在量比较大的情况下)。因为主从复制，从库的SQL线程是单线程，它接收到主库的BINLOG，要一条一条执行，一条SQL卡住了，后面的SQL就会排队等待，这时同步就延迟了

MySQL在线DDL(加字段、加索引等修改表结构之类的操作）过程如下：

1. 对表加锁(表此时只读)
2. **复制原表物理结构**
3. 修改表的物理结构
4. **把原表数据导入中间表中**，数据同步完后，**锁定中间表，并删除原表**
5. rename中间表为原表
6. 刷新数据字典，并释放锁

在这个过程中会锁表。造成当前操作的表无法写入数据，影响用户使用。由于需要复制原表的数据到中间表，所以表的数据量越大，等待的时候越长，卡死在那里(用户被拒绝执行update和insert操作,表现就是延迟了一直在等待)。

其实就是对表加了个排它锁，这个时候其他用户只能读表的数据,不能写。

平时进行修改表的结构，更改字段，新增字段，更改字段名称一般都是通过ALTER TABLE  TABLENAE 语法进行修改的。对于测试库，在线小表或者并发访问不是很大的情况是OK。但是如果是在线大表。那就很麻烦。由于表数据量大，复制表需要比较长的时间，在这个时间段里面，表是被加了锁的(写锁)，加写锁时其他用户只能select表不能update、insert表。表数据量越大，耗时越长。

所以，对于数据量大的表，数量很大。在线修改表结构一直是一个头痛的问题，因为互联网应用的一大特点不能影响用户正常使用，否则用户会慢慢流失掉。

#### 解决方案

一般线上新增字段的两个通用处理方式：

- 方式一：流量低谷时段进行alter table操作：如凌晨3点

- 方式二：创建临时表、加字段、拷贝数据、增量拷贝、rename表名

  ```sql
  -- 1. 表结构复制，耗时<10ms
  create table table_new like table_name;
  -- 2. 临时表加字段，耗时<10s
  ALTER TABLE `table_new`
  ADD COLUMN `new_column`  tinyint UNSIGNED NULL DEFAULT 0 COMMENT '字段注释|张晓武|2017-7-17';
  
  -- 3. 数据拷贝临时表，345w数据耗时86s
  INSERT INTO table_new(id,os,sv,uuid,reg,account,user_id,package_id,created_at,updated_at) (SELECT id,os,sv,uuid,reg,account,user_id,package_id,created_at,updated_at from table_name);
  
  -- 4. 增量数据再次拷贝，看增量数据量，耗时<100ms
  INSERT INTO table_new(id,os,sv,uuid,reg,account,user_id,package_id,created_at,updated_at) (SELECT id,os,sv,uuid,reg,account,user_id,package_id,created_at,updated_at from table_name
  WHERE `id` > (SELECT MAX(`id`) from `table_new`));
  
  -- 5. 表名切换，耗时50ms
  RENAME TABLE
  table_name TO table_name_20170717,
  table_new TO table_name;
  ```

- 方式三：工具 -> online-schema-change
是percona推出的一个针对mysql在线ddl的工具