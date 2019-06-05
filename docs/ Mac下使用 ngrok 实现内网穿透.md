## ngrok
ngrok 是一个反向代理，通过在公共的端点和本地运行的 Web 服务器之间建立一个安全的通道。ngrok 可捕获和分析所有通道上的流量，便于后期分析和重放

## 安装配置
1. 下载地址
https://ngrok.com/download
2. 下载完解压到独立目录
- ./ngrok help 查看帮助
- ./ngrok http 8080 映射8080端口出公网

当 Session Status 显示为 online 状态时即可使用外网访问