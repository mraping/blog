命令 | 用途
---|---
df -hl | 查看磁盘空间占用
npm config set registry http://registry.npm.taobao.org | 修改npm的资源镜像链 
locate | [查找文件或目录](https://www.cnblogs.com/xqzt/p/5426666.html) 
 ip addr | 显示网卡及配置的地址信息,参考[Linux ip命令详解](https://www.jellythink.com/archives/469) 
 grep -v、-e、-E | 在Linux的grep命令中如何使用or，and，not操作符 
 netstat -ntpl | 查看占用端口的应用 
 vim临时显示行号 | ：set number 
 mac vim 默认显示行号 | [mac vim 默认显示行号](https://blog.csdn.net/i491066272/article/details/78588748) 
 修改iterm2中的用户名 | [修改iterm2中的用户名](https://segmentfault.com/q/1010000011533134) 
Mac下Vim编辑快捷键小结| https://www.jianshu.com/p/6f13474d36ac 
[jstat](https://www.cnblogs.com/alipayhutu/archive/2012/08/20/2647353.html)| -class：统计class loader行为信息，示例：jstat -class 17970 1000 10 (每隔1秒监控一次，一共做10次) 
| -gc：统计jdk gc时heap信息 
| -compile：统计编译行为信息 
查看服务器端口开放| curl 39.107.59.19:9999 
[jcmd](https://www.jianshu.com/p/388e35d8a09b)| jcmd 11295 VM.system_properties 查看 JVM 的属性信息 
| jcmd 11295 VM.version 查看目标jvm进程的版本信息 
| jcmd pid PerfCounter.print 查看指定进程的性能统计信息 
jmap| jmap -heap 11295 
jstack| jstack 11295 > jstack_20190613.log 
iostat| 查看磁盘的读写情况 
Source| 到数据库 
service mongo start/mongod| 启动mongo 
rpm| 执行安装包 
| －ivh 安装显示安装进度--install--verbose--hash 
| －Uvh 升级软件包--Update 
netstat -lntp \| grep 6379| 检测6379端口是否在监听 
ps -ef grep redis| 检查后台进程是否正在运行 
option + f/b| 命令跳 
redis配置全局软连接| sudo ln -s /Users/suxiongwei/program/redis-5.0.2/src/redis-server /usr/local/bin/redis-server 
netstat -anp tcp \|grep 6379| mac 下查看redis端口 
/usr/local/mongodb/bin|                        
.mongod| 启动mongo 
.mongo| 进入mongoDB后台 
select version();| 显示mysql版本 
查看kafka topic列表，使用--list参数| bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list 
查看kafka特定topic的详情，使用--topic与--describe参数| bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic exportAllResultsOnebox --describe 
| bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group ksx_exam 
unrar x 需解压的文件目录| 解压rar 
合并两个独立启动仓库的历史| git pull origin master --allow-unrelated-histories 
打印集群的信息| cluster info 
列出集群当前已知的所有节点(node)，以及这些节点的相关信息| cluster nodes 
对当前目录下```所有的目录和文件```的大小进行汇总,-s表示汇总,-h表示以KB, MB, GB, TB格式进行人性化显示| du -sh . 
对当前目录下每一个目录和文件的大小分别进行汇总| du -sh * 
查使用内存最多的10个进程| ps -aux \| sort -k4nr \| head -n 10 
跳到第n行"ngg" 或 "nG" 或 ":n"|                        
删除一个单词"dw"，删除一个单词进入插入模式"cw"|                        
粘贴"p"，交换两个字符"xp"|                        
复制"y"，复制一行"yy"|                        
删除到行尾可以使用"D"或"C"|                        
修改item2用户名称| vim agnoster.zsh-theme；https://segmentfault.com/q/1010000011533134 
批量添加注释| v <br/>选中<br/>control + v<br/>i<br/>esc * 2 
Item2清除当前行| control + u 
到行首| control + a 
到行尾| control + e 
搜索命令历史| control + r 
netstat| https://www.cnblogs.com/EasonJim/p/8098532.html 
| https://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316661.html 
统计80端口连接数| netstat -nat \| grep -i "80" \| wc -l 
统计已连接上的，状态为“established| netstat -anp \| grep ESTABLISHED \| wc -l 
Mac top| 先输入 ***o\*** 键 ,再输入 ***cpu\*** 则按cpu使用量排序，输入 ***rsize\*** 按内存使用量排序 
vim /etc/motd| 设置登录欢迎语 
tail -300f shopbase.log| \倒数300行并进入实时监听文件写入模式 
git branch \| xargs git branch -d| 删除当前分支外的所有分支 
git branch \| grep ‘dev*’  \| xargs git branch -d| 删除分支名包含指定字符的分支 
Idea 驼峰| shift + option + u 
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        
|                        

