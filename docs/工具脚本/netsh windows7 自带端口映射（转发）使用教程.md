1. 查看

  ```netsh interface portproxy show all```

2. 添加端口映射转发
  ```netsh interface portproxy add v4tov4 listenaddress=localaddress listenport=localport connectaddress=destaddress```

  - listenaddress -- 等待连接的本地ip地址

  - listenport -- 本地监听的TCP端口（待转发）

  - connectaddress -- 被转发端口的本地或者远程主机的ip地址

  - connectport -- 被转发的端口

  > 如待转发为虚拟机，被转发为宿主机

3. 删除端口映射转发

  ```
  netsh interface portproxy delete v4tov4 listenaddress=10.10.10.234 listenport=64822 
  ```
