## Dubbo分布式服务调度
### 本文目标
如何基于Dubbo发布注册接口服务、如何订阅调用服务
## Dubbo简介
[官网地址](http://dubbo.apache.org/zh-cn/)</br>
Apache Dubbo (incubating) |ˈdʌbəʊ| 是一款高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

### Dubbo内部调度图
![Dubbo内部调度图](http://39.96.200.64/upload/2019/01/stccup77k8hcfp0efbogd1e93q.png)

### Dubbo支持的两大协议
- RPC协议/Dubbo协议

	适用于同构项目</br>
	Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。Dubbo缺省协议不适合传送大数据量的服务，比如传文件，传视频等，除非请求量很低。
-  HTTP协议

	适用于通用项目
	
## 搭建一个简单的[springboot+dubbo服务](https://github.com/suxiongwei/springboot-dubbo)[多模块项目]
分别创建</br>
1. 父模块 springboot-dubbo
2. provider 模块
3. consumer 模块

### provider配置
```
## 避免端口冲突
server.port=9200

## Dubbo 服务提供者配置
spring.dubbo.application.name=consumer
spring.dubbo.registry.address=zookeeper://127.0.0.1:2181
spring.dubbo.scan=com.sxw.dubbo.service.provider
spring.dubbo.server=true
spring.dubbo.protocol.name=dubbo
spring.dubbo.protocol.port=20880
```

#### 定义sayHello接口
```
public interface SayHello {
    String sayHello(String name);
}

```

#### sayHello接口实现
```
import com.alibaba.dubbo.config.annotation.Service;

@Service
public class SayHelloImpl implements SayHello {
    @Override
    public String sayHello(String name) {
        return "Hello " + name + "!";
    }
}
```

#### 编写启动类
```
@SpringBootApplication
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
```

### consumer配置
```
## 避免端口冲突
server.port=9201

## Dubbo 服务消费者配置
spring.dubbo.application.name=consumer
spring.dubbo.registry.address=zookeeper://127.0.0.1:2181
spring.dubbo.scan=com.sxw.dubbo.service
spring.dubbo.server=true
spring.dubbo.protocol.name=dubbo
spring.dubbo.protocol.port=20800
```

#### 创建SayService
```
@Component
public class SayService  {
    @Reference
    SayHello sayHello;

    public String say (String name) {

        return sayHello.sayHello(name);
    }
}

```

#### 编写启动类
```
@SpringBootApplication
@RestController
public class ConsumerApplication {
    @Autowired
    SayService sayService;

    @RequestMapping("/hello")
    public String say(@RequestParam("name") String name) {
        return sayService.say(name);
    }
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

## 搭建完成，测试
1. 启动zookeeper
进入zookeeper/bin目录下，启动zookeeper。
./zkServer.sh start
2. 启动dubbo
3. 访问http://localhost:9201/hello?name=测试dubbo
```
返回：
Hello 测试dubbo!
```
接口调用成功，说明服务消费方通过dubbo成功的调用了提供者的服务




