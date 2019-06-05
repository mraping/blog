-  启动ZooKeeper服务器 ```bin/zkServer.sh start```
-  启动CLI ```bin/zkCli.sh```
-  启动ZooKeeper服务器 ```bin/zkServer.sh stop```
-  启动Kafka服务器 ```bin/kafka-server-start.sh config/server.properties```
-  后台启动Kafka服务器```nohup bin/kafka-server-start.sh config/server.properties >>kafka.log &``` 然后回车 exit
-  停止Kafka服务器 ```bin/kafka-server-stop.sh config/server.properties``` 
-  创建Kafka主题
```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic Hello-Kafka
```
- 获取Kafka服务器中的主题列表
```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```
- 启动生产者以发送消息
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic Hello-Kafka
```
- 启动消费者以接收消息
```
bin/kafka-console-consumer.sh --zookeeper localhost:2181 —topic Hello-Kafka --from-beginning
以上命令在高版本的已不再支持，使用下面的
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Hello-Kafka --from-beginning
```

### 线上kafka启动
```
/home/work/software/kafka_2.11-2.0.1/bin/kafka-server-start.sh -daemon /home/work/software/kafka_2.11-2.0.1/config/server.properties 1>/dev/null 2>&1 &
/home/work/software/kafka_2.11-2.0.1/bin/kafka-server-start.sh -daemon /home/work/software/kafka_2.11-2.0.1/config/server-1.properties 1>/dev/null 2>&1 &
/home/work/software/kafka_2.11-2.0.1/bin/kafka-server-start.sh -daemon /home/work/software/kafka_2.11-2.0.1/config/server-2.properties 1>/dev/null 2>&1 &
/home/work/software/kafka_2.11-2.0.1/bin/kafka-server-start.sh -daemon /home/work/software/kafka_2.11-2.0.1/config/server-3.properties 1>/dev/null 2>&1 &
```