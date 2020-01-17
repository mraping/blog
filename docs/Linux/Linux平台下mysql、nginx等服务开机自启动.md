我们对service和chkconfig两个命令都不陌生，systemctl 是管制服务的主要工具， 它整合了chkconfig 与 service功能于一体。

- systemctl is-enabled iptables.service
- systemctl is-enabled servicename.service #查询服务是否开机启动
- systemctl enable *.service #开机运行服务
- systemctl disable *.service #取消开机运行
- systemctl start *.service #启动服务
- systemctl stop *.service #停止服务
- systemctl restart *.service #重启服务
- systemctl reload *.service #重新加载服务配置文件
- systemctl status *.service #查询服务运行状态
- systemctl --failed #显示启动失败的服务

注：*代表某个服务的名字，如http的服务名为httpd



linux下systemctl enable （service名）时出现file exists的解决办法

有时候我们在Linux下（我主要是是在centos平台，想来不同的平台应该差异没有那么大）配置好一项服务的时候，会使用一个命令，那就是

systemctl enable （service名），

但是如果一次没有配置成功的话，经常需要重复的第二次配置，但是如果忘记remove时候，就会出现比较奇怪的错误，比如会提示报错说file exists，这时候就需要先systemctl disable （服务名）。

这是因为在systemctl enable （service名）时候，实际上是创建了一个链接，这里以我的vncserver@:1.service为例，我们可以看到实际上是创建了一个symlink，系统链接，从/etc/systemd/system/multi-user.target.wants/vncserver@:1.service 到 /etc/systemd/system/vncserver@:1.service

我们这时候在/etc/systemd/system/下面也是能够看得到这个vncserver@:1.service的链接



所以再次操作的时候，要记得使用 systemctl disable 命令清除掉



如果发现没有自启动的话：

getenforce命令      

使用getenforce命令可以显示当前SELinux的应用模式，是强制、执行还是停用。

命令语法：

getenforce

如果输出：Enforcing

则执行 setenforce 0

然后改个文件：vim /etc/selinux/config

修改：SELINUX=disabled



重启观察有没有自动开启