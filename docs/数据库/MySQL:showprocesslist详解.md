show processlist 是显示用户正在运行的线程，需要注意的是，除了 root 用户能看到所有正在运行的线程外，其他用户都只能看到自己正在运行的线程，看不到其它用户正在运行的线程。除非单独个这个用户赋予了PROCESS 权限。

root用户，可以看到全部线程运行情况

普通的activiti用户只能看到自己的

单独给activiti用户授PROCESS权限，（授权后需要退出重新登录）

show processlist 显示的信息都是来自MySQL系统库 information_schema 中的 processlist 表。所以使用下面的查询语句可以获得相同的结果：

```mysql
select * from information_schema.processlist
```

了解这些基本信息后，下面我们看看查询出来的结果都是什么意思。

- Id: 就是这个线程的唯一标识，当我们发现这个线程有问题的时候，可以通过 kill 命令，加上这个Id值将这个线程杀掉。前面我们说了show processlist 显示的信息时来自information_schema.processlist 表，所以这个Id就是这个表的主键。
- User: 就是指启动这个线程的用户。
- Host: 记录了发送请求的客户端的 IP 和 端口号。通过这些信息在排查问题的时候，我们可以定位到是哪个客户端的哪个进程发送的请求。
- DB: 当前执行的命令是在哪一个数据库上。如果没有指定数据库，则该值为 NULL 。
- Command: 是指此刻该线程正在执行的命令。这个很复杂，下面单独解释
- Time: 表示该线程处于当前状态的时间。
- State: 线程的状态，和 Command 对应，下面单独解释。
- Info: 一般记录的是线程执行的语句。默认只显示前100个字符，也就是你看到的语句可能是截断了的，要看全部信息，需要使用 show full processlist。

下面我们单独看一下 Command 的值：

- Binlog Dump: 主节点正在将二进制日志 ，同步到从节点
- Change User: 正在执行一个 change-user 的操作
- Close Stmt: 正在关闭一个Prepared Statement 对象
- Connect: 一个从节点连上了主节点
- Connect Out: 一个从节点正在连主节点
- Create DB: 正在执行一个create-database 的操作
- Daemon: 服务器内部线程，而不是来自客户端的链接
- Debug: 线程正在生成调试信息
- Delayed Insert: 该线程是一个延迟插入的处理程序
- Drop DB: 正在执行一个 drop-database 的操作
- Execute: 正在执行一个 Prepared Statement
- Fetch: 正在从Prepared Statement 中获取执行结果
- Field List: 正在获取表的列信息
- Init DB: 该线程正在选取一个默认的数据库
- Kill : 正在执行 kill 语句，杀死指定线程
- Long Data: 正在从Prepared Statement 中检索 long data
- Ping: 正在处理 server-ping 的请求
- Prepare: 该线程正在准备一个 Prepared Statement
- ProcessList: 该线程正在生成服务器线程相关信息
- Query: 该线程正在执行一个语句
- Quit: 该线程正在退出
- Refresh：该线程正在刷表，日志或缓存；或者在重置状态变量，或者在复制服务器信息
- Register Slave： 正在注册从节点
- Reset Stmt: 正在重置 prepared statement
- Set Option: 正在设置或重置客户端的 statement-execution 选项
- Shutdown: 正在关闭服务器
- Sleep: 正在等待客户端向它发送执行语句
- Statistics: 该线程正在生成 server-status 信息
- Table Dump: 正在发送表的内容到从服务器
- Time: Unused

如果有歧义可以查看官方文档： [8.14.1 Thread Command Values](https://link.zhihu.com/?target=https%3A//dev.mysql.com/doc/refman/5.6/en/thread-commands.html)

下面我们看看 State 的值有哪些：

发现有大神已经总结的超好了：[MySQL SHOW PROCESSLIST协助故障诊断](https://link.zhihu.com/?target=http%3A//www.ywnds.com/%3Fp%3D9337)

官方文档关于 State 的解释： [8.14.2 General Thread States](https://link.zhihu.com/?target=https%3A//dev.mysql.com/doc/refman/5.6/en/general-thread-states.html)

下面是常用到的SQL，记录下来便于使用。

按客户端 IP 分组，看哪个客户端的链接数最多

```mysql
select client_ip,count(client_ip) as client_num from (select substring_index(host,':' ,1) as client_ip from processlist ) as connect_info group by client_ip order by client_num desc;
```

查看正在执行的线程，并按 Time 倒排序，看看有没有执行时间特别长的线程

```mysql
select * from information_schema.processlist where Command != 'Sleep' order by Time desc;
```

找出所有执行时间超过 5 分钟的线程，拼凑出 kill 语句，方便后面查杀

```mysql
select concat('kill ', id, ';') from information_schema.processlist where Command != 'Sleep' and Time > 300 order by Time desc;
```