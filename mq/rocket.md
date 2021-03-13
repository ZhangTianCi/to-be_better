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

```xml
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
log4j.rootLogger=INFO, console
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.conversionPattern=%5p [%t] (%F:%L) - %m%n
```



