## 工具/原料
- 两台Linux系统机器
- ssh
- ssh-keygen/ssh-copy-id

## 方法/步骤

1. 本地系统执行 ssh-keygen -t rsa 命令，生成密钥文件

2. 在相应的目录下查看生成的密钥文件，其中：id_rsa为私钥文件，id_rsa.pub为公钥文件

3. 本地机器执行命令如：ssh-copy-id -i ~/.ssh/id_rsa.pub dss@10.164.194.42， 将公钥文件传输的远程机器，并生效

4. 再次使用已经做免密处理的用户登录远程机器，已经不需要密码了，免密登录处理完成。

5. 查看远程机器的authorized_keys文件，可以看到对应的变化：本地机器的公钥已经增加到远程机器的配置文件中了

END
## 注意事项
- 免密码登录的处理是用户对用户的，切换其他用户后，仍然需要输入密码
- 公钥传到远程机器并生效的操作，可用其他方式实现，如scp后修改authorized_keys
- 远程机器的.ssh目录需要700权限，authorized_keys文件需要600权限

## 参考资源

[linux实现ssh免密码登录的正确方法](https://jingyan.baidu.com/article/c275f6ba08267ae33c756758.html)

