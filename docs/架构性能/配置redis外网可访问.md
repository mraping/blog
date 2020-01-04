在 Linux 中安装了redis 服务。在内网的一台虚拟机启动之后，另一台虚拟机的项目启动不了，查看日志是因为redis连接不上。

错误分析及解决办法

错误的原因很简单，就是没有连接上 redis 服务，由于 redis 采用的安全策略，默认会只准许本地访问。需要通过简单配置，完成允许外网访问。

修改 redis 的配置文件，将所有 bind 信息全部屏蔽。

```
# bind 192.168.1.100 10.0.0.1 
# bind 192.168.1.8 
# bind 127.0.0.1
```

修改完成后，需要重新启动 redis 服务。

修改 Linux 的防火墙(iptables)，开启你的 redis 服务端口，默认是 6379。

```
-A INPUT -m state –state NEW -m tcp -p tcp –dport 6379 -j ACCEPT 
…… 
-A INPUT -j REJECT –reject-with icmp-host-prohibited
```

请注意，一定要将 redis 的防火墙配置放在 REJECT 的前面。然后执行 **service iptables restart**。

至此，访问刚刚上面的代码，就能够链接到 redis 服务，并且能够正确显示了。

### 关于bind

翻看网上的文章，此处多翻译为"指定 redis 只接收来自于该 IP 地址的请求，如果不进行设置，那么将处理所有请求，在生产环境中最好设置该项"。这种解释会 totally 搞糊涂初学者，甚至是错误的。该处的英文原文为:

```
# If you want you can bind a single interface, if the bind option is not 
# specified all the interfaces will listen for incoming connections. 
# bind 127.0.0.1
```

该处说明 bind 的是interface，也就是说是网络接口。服务器可以有一个网络接口(通俗的说网卡)，或者多个。打个比方说机器上有两个网卡，分别为 192.168.205.5 和 192.168.205.6，如果 bind 192.168.205.5，那么只有该网卡地址接受外部请求，如果不绑定，则两个网卡口都接受请求。

OK，不知道讲清楚没有，在举一个例子。在我上面的实验过程中，我是将bind项注释掉了，实际上我还有一种解决方案。由于我redis服务器的地址是 192.168.1.4 。如果我不注释bind项，还有什么办法呢？我可以做如下配置:

```
# bind 192.168.1.4
```

这里很多人会误以为绑定的ip应该是请求来源的ip。其实不然，这里应该绑定的是你r**edis服务器本身接受请求的ip**。