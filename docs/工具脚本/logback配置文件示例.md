```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
   <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
   <property resource="application.yml"/>
   <springProperty scope="context" name="LOG_HOME" source="loghome"/>

   <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
      <encoder>
         <pattern>%date{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
      </encoder>
   </appender>

   <appender name="INFO-LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <file>${LOG_HOME}/ksx-ow.log</file>
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
         <fileNamePattern>${LOG_HOME}/ksx-ow-%d{yyyy-MM-dd}.log</fileNamePattern>
         <!--日志文件保留天数-->
         <maxHistory>15</maxHistory>
      </rollingPolicy>
      <encoder>
         <pattern>%date{yyyy-MM-dd HH:mm:ss}    %msg%n</pattern>
      </encoder>
      <filter class="ch.qos.logback.classic.filter.LevelFilter"><!-- 只打印错误日志 -->
         <level>INFO</level>
         <onMatch>ACCEPT</onMatch>
         <onMismatch>DENY</onMismatch>
      </filter>
   </appender>

   <appender name="ERROR-LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <file>${LOG_HOME}/ksx-ow-err.log</file>
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
         <fileNamePattern>${LOG_HOME}/ksx-ow-err-%d{yyyy-MM-dd}.log</fileNamePattern>
         <!--日志文件保留天数-->
         <maxHistory>15</maxHistory>
      </rollingPolicy>
      <encoder>
         <pattern>%date{yyyy-MM-dd HH:mm:ss}    %msg%n</pattern>
      </encoder>
      <filter class="ch.qos.logback.classic.filter.LevelFilter"><!-- 只打印错误日志 -->
         <level>ERROR</level>
         <onMatch>ACCEPT</onMatch>
         <onMismatch>DENY</onMismatch>
      </filter>
   </appender>

   <!-- 异步输出 -->
   <appender name="ASYNC-INFO" class="ch.qos.logback.classic.AsyncAppender">
      <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
      <discardingThreshold>0</discardingThreshold>
      <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
      <queueSize>256</queueSize>
      <!-- 添加附加的appender,最多只能添加一个 -->
      <appender-ref ref="INFO-LOG"/>
   </appender>

   <appender name="ASYNC-ERROR" class="ch.qos.logback.classic.AsyncAppender">
      <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
      <discardingThreshold>0</discardingThreshold>
      <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
      <queueSize>256</queueSize>
      <!-- 添加附加的appender,最多只能添加一个 -->
      <appender-ref ref="ERROR-LOG"/>
   </appender>


   <!--开发环境:打印控制台-->
   <springProfile name = "dev">
      <root level="INFO">
<!--         <appender-ref ref="CONSOLE" />-->
         <appender-ref ref="ASYNC-INFO"/>
         <appender-ref ref="ASYNC-ERROR"/>
      </root>
   </springProfile>

   <!--生产环境:输出到文件-->
   <springProfile name = "product">
      <root level="INFO">
         <appender-ref ref="ASYNC-INFO"/>
         <appender-ref ref="ASYNC-ERROR"/>
      </root>
   </springProfile>
</configuration>
```

