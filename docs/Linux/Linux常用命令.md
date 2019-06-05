1. 安装zip:yum install zip
2. 安装unzip:yum install unzip
3. 解压zip文件:unzip file.zip
4. 更改目录权限命令:chmod -R 755 目录名称
> -R : 对目前目录下的所有档案与子目录进行相同的权限变更(即以递回的方式逐个变更) 
5. 分屏显示：more
6. 清屏：clear
7. 删除文件：rm
8. 查看或者合并文件内容：cat,用于查看文件内容（适合内容较少的，**较多的用more**）、创建文件、文件合并、追加文件内容等。
9. 文本搜索：grep 比如: grep 'http' elasticsearch.yml 
10. 查看命令位置：which 比如: which java
11. 查看当前日历：cal
12. 终止进程：kill
> kill命令指定进程号的进程，需要配合 ps 使用。
使用格式：kill [-signal] pid
信号值从0到15，其中**9为绝对终止**，可以处理一般信号无法终止的进程。
13. 检测磁盘空间：df  -h 以人性化方式显示
14. jps -l: 输出应用程序主类完整package名称或jar完整名称.<br/>&emsp;&emsp;-v: 列出jvm参数, -Xms20m -Xmx50m是启动程序指定的jvm参数
15. service firewalld stop：关闭防火墙
16. find /home -name "*.txt":在/home目录下查找以.txt结尾的文件名
17. find . \( -name "*.txt" -o -name "*.pdf" \)或find . -name "*.txt" -o -name "*.pdf":当前目录及子目录下查找所有以.txt和.pdf结尾的文件
18. ps -ef/ps aux：这两个命令都是查看当前系统正在运行进程，两者的区别是展示格式不同。如果想要查看特定的进程可以使用这样的格式：ps aux|grep redis （查看包括redis字符串的进程）
19. linux less从后向前查看日志信息
- less sigma.log
- shift+g 移动到最后一行
- ctrl + F - 向前移动一屏 or f?
- ctrl + B - 向后移动一屏 or b?
20. sz下载文件夹
- 先压缩：tar zcvf filaname.tar.gz filename
- 再下载  


## Linux性能检测基本命令
1. uptime
    > 该命令可以大致的看出计算机的整体负载情况，load average后的数字分别表示计算机在1min、5min、15min内的平均负载。
2. ls -alh
    > 会使用G、M等来显示大文件
## 查看 CPU、内存、磁盘、网络CPU、内存、磁盘、网络
https://mp.weixin.qq.com/s/75zCoSdVTnFhDBU48dQTLA
### 查看内存信息:
- free(-g/-m)
### 查看磁盘的信息一般有三个命令：
- df 查看容量使用情况
    - df -lh (生成便于阅读的本地文件系统列表,大小以MB和GB为单位.)
- vmstat 查看读写信息
- iostat 查看读写速度
## zookeeper开机自动启动
1. 新建一个脚本zookeeper
2. 为新建的脚本zookeeper添加可执行权限，命令是:chmod +x zookeeper
3. 把zookeeper这个脚本添加到开机启动项里面，命令是：chkconfig --add zookeeper
4. 如果想看看是否添加成功，命令是：chkconfig --list


