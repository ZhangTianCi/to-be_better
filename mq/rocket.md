### NameServer

```shell
# 测试环境修改JVM配置
vim home_path/bin/runserver.sh
sh home_path/bin/mqnamesrv
```

### Broker

```shell
# 测试环境修改JVM配置
vim home_path/bin/runbroker.sh
sh home_path/bin/mqbroker -n name_server_host:port
```



### 控制台

[ GitHub](https://github.com/apache/rocketmq-externals)

mvn编译

mvn clean package -Dmaven.test.skip=true

参数 --rocketmq.config.namesrvAddr=host:port,host:port

#### 规范

在Consumer集群下.

一个集群内,所有的Consumer的设置应当一致.

包括Group、Topic、submit

## 控制台输出消除

pom包添加slf4j的实现。然后添加相应的配置。

```java
// 设置环境变量 - 消除RocketMQ的运行时警告
System.setProperty("rocketmq.client.logUseSlf4j","true");
```


```xml
<!-- pom.xml -->
<dependency>
	<groupId>org.slf4j</groupId>
	<artifactId>slf4j-log4j12</artifactId>
	<version>2.0.0-alpha1</version>
</dependency>
<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
</dependency>
```

```properties
# log4j.properties
log4j.rootLogger=INFO, console
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.conversionPattern=%5p [%t] (%F:%L) - %m%n
```



### 消息的顺序性

1. 同一个Topic
2. 同一个Queue
3. 发送方保证顺序（单线程），并发送至一个Queue。异步发送无法保证顺序。
4. 接收方用一个线程，消费一个Queue里的线程

