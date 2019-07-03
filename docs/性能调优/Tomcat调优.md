### 1.JVM 参数调优

-Xms表示 JVM 初始化堆的大小，-Xmx表示 JVM 堆的最大值。这两个值的大小一般根据需要进行设置。当应用程序需要的内存超出堆的最大值时虚拟机就会提示内存溢出，并且导致应用服务崩溃。因此一般建议堆的最大值设置为可用内存的最大值的**80%**。在 catalina.bat 中，设置 JAVA_OPTS='-Xms256m -Xmx512m'，表示初始化内存为 256MB，可以使用的最大内存为 512MB。 

### 2.禁用DNS查询

当web 应用程序向要记录客户端的信息时，它也会记录客户端的 IP 地址或者通过域名服务 器查找机器名转换为 IP 地址。DNS 查询需要占用网络，并且包括可能从很多很远的服务器或 者不起作用的服务器上去获取对应的 IP 的过程，这样会消耗一定的时间。为了消除 DNS 查询 对性能的影响我们可以关闭 DNS 查询，方式是修改 server.xml 文件中的 enableLookups 参数值。

enableLookups:是否反查域名，取值为:true 或 false。为了提高处理能力，应设置为 false 

### 3.调整线程数 

通过应用程序的连接器(Connector)进行性能控制的的参数是创建的处理请求的线程数。 Tomcat 使用线程池加速响应速度来处理请求。在 Java 中线程是程序运行时的路径，是在一个 程序中与其它控制线程无关的、能够独立运行的代码段。它们共享相同的地址空间。多线程帮助程序员写出 CPU 最大利用率的高效程序，使空闲时间保持最低，从而接受更多的请求。 

web server 允许的最大连接数还受制于操作系统的内核参数设置，通常 Windows 是 2000 个左右，Linux 是 1000 个左右。  

在 tomcat 配置文件 server.xml 中的<Connector />配置中，和连接数相关的参数有: 

minProcessors:最小空闲连接线程数，用于提高系统处理性能，默认值为 10 

maxProcessors:最大连接线程数，即:并发处理的最大请求数，默认值为 75 

acceptCount:允许的最大连接数，应大于等于 maxProcessors，默认值为 100 

connectionTimeout:网络连接超时，单位:毫秒。设置为 0 表示永不超时，这样设置有隐患 的。通常可设置为 30000 毫秒。 

其中和最大连接数相关的参数为 maxProcessors 和 acceptCount。如果要加大并发连接数， 应同时加大这两个参数。 

最好的方式是多设置几次并且进行测试，观察响应时间和内存使用情况。在不同的机器、操作系统或虚拟机组合的情况下可能会不同，而且并不是所有人的 web 站点的流量都是一样的，因此没有一刀切的方案来确定线程数的值。

### 4.Tomcat内存

-  java.lang.OutOfMemoryError: Java heap space

  堆内存溢出，调整 Heap size

  ```properties
  修改 TOMCAT_HOME/bin/catalina.sh set JAVA_OPTS= -Xms32m -Xmx512m
  可以根据自己机器的内存进行更改。
  ```

- java.lang.OutOfMemoryError: PermGen space

  PermGen space 的全称是 Permanent Generation space,是指内存的**永久保存区域**,这块内存主要是被 JVM 存放 **Class 和 Meta** 信息的,Class 在被 Loader 时就会被放到 PermGen space 中, 它和存放类实例(Instance)的 Heap 区域不同,GC(Garbage Collection)**不会在主程序运行期对PermGen space 进行清理**，所以如果你的应用中有很多 CLASS 的话,就很可能出现 PermGen space 错误,这种错误常见在 web 服务器对 JSP 进行 pre compile 的时候。如果你的 WEB APP 下都用了大量的第三方 jar, 其大小超过了 jvm 默认的大小(4M)那么就会产生此错误信息了。
  
解决方法: 手动设置 MaxPermSize 大小 
  
修改 TOMCAT_HOME/bin/catalina.sh
  在“echo "Using CATALINA_BASE: $CATALINA_BASE"”上面加入以下行:
  
```properties
  JAVA_OPTS="-server -XX:PermSize=64M -XX:MaxPermSize=128m
  ```
  
建议:将相同的第三方 jar 文件移置到 tomcat/shared/lib 目录下，这样可以达到减少 jar 文档重复占用内 存的目的。 