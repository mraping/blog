```
# 终止监听80端口的程序
lsof -i:80 |awk '{print $2}' | sed -n '2p' | xargs kill -9
java -jar security-3.3.0-SNAPSHOT.jar --server.port=80 > log.file 2>&1 &
```

1. lsof -i:80 为找到监听80的程序及对应程序的pid
2. awk是行处理器
3. sed -n 是指定第几行
4. “xargs kill -9” 中的xargs命令是用来把前面命令的输出结果（这里是PID）作为“kill -9”命令的参数，并执行该命令

详细的启动脚本
```
RESOURCE_NAME=security-3.3.0-SNAPSHOT.jar

tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Stop Process...'
kill -15 $tpid
fi
sleep 5
tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Kill Process!'
kill -9 $tpid
else
echo 'Stop Success!'
fi

tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
    echo 'App is running.'
else
    echo 'App is NOT running.'
fi

rm -f tpid
nohup java -jar $RESOURCE_NAME --server.port=80 > log.file 2>&1 &
echo $! > tpid
echo Start Success!
```

/Users/suxiongwei/.bash_profile







nohup /home/work/software/jdk1.8.0_191/bin/java -Dserver.port=9071 -Dspring.profiles.active=dev -jar ksxing-ow-0.0.1-SNAPSHOT.war > home.log 2>&1 &