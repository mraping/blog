文档地址：[https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98](https://github.com/alibaba/druid/wiki/常见问题)

springboot配置示例：

```
server:
  port: 9071
spring:
  data:
    mongodb:
      host: 
      port: 
      username: 
      password: 
      database: 
      authentication-database: 
      connections-per-host: 100
      min-connections-per-host: 20
  datasource:
    druid:
      min-idle: 5
      web-stat-filter:
        # session统计功能
        session-stat-enable: true
        url-pattern: /*
        # 缺省sessionStatMaxCount是1000个
        session-stat-max-count: 10
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
        enabled: true
        # 配置profileEnable能够监控单个url调用的sql列表
        profile-enable: true
        # 配置principalSessionName，使得druid能够知道当前的session的用户是谁
        principal-session-name: user
      validation-query-timeout: 30000
      max-active: 30
      test-while-idle: true
      validation-query: SELECT 1
      # Spring和Jdbc的关联监控
      aop-patterns: com.ksxing.service.*
      use-global-data-source-stat: true
      filters: stat,wall,log4j
      max-wait: 60000
      master:
        name: master
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: 
        port: 3306
        username: 
        password: 
      slave1:
        name: Slave1
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: 
        port: 3306
        username: 
        password: 
      slave2:
        name: Slave2
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: 
        port: 3306
        username: 
        password: 
      filter:
        # 监控
        stat:
          # SQL合并配置
          merge-sql: true
          db-type: mysql
          # 配置SQL慢的标准，执行时间超过slowSqlMillis的就是慢。slowSqlMillis的缺省值为3000，也就是3秒
          slow-sql-millis: 1000
          log-slow-sql: true
        slf4j:
          enabled: true
        # 基于SQL语义分析来实现防御SQL注入攻击
        wall:
          config:
            # 是否允许修改表
            drop-table-allow: false
            # 是否允许执行DELETE语句
            delete-allow: true
          enabled: true
      test-on-borrow: false
      stat-view-servlet:
        url-pattern: /druid/*
        # 允许清空统计数据
        reset-enable: true
        login-username: 
        login-password: 
        enabled: true
        # StatViewSerlvet展示出来的监控信息比较敏感，是系统运行的内部情况，如果你需要做访问控制，可以配置allow和deny这两个参数
        # allow: 
      pool-prepared-statements: false
      initial-size: 5
      test-on-return: false
    type: com.alibaba.druid.pool.DruidDataSource
  redis:
    host: 
    port: 
    password: 
    jedis:
      pool:
        max-idle: 3
        max-active: 10
    session:
      #session存储类型
      store-type: redis

```

