## 起步

[官网地址](http://activemq.apache.org/)

[下载地址](http://activemq.apache.org/components/classic/download/)

**依赖于Java环境运行**

### 特点

消息确认机制、组件丰富、支持多种协议

### 事务

在创建Session的时候声明。

### 确认机制

消费端收到消息后，可以给Broke发送ack请求。Broke在收到ack后，会在队列中删除当前消息。（Queue）

生产端发出消息后，可以通过Listener或者同步的方式，来确认消息是否被消费。



### 配置

1. 配置Web端账号密码

```properties
vim activemq_home/conf/jetty-realm.properties
# user_name : password [,role_name]
```

2. 配置Broke账号密码(需要添加依赖)

   2.1简单的权限验证

   ```xml
   <!-- activemq_home/conf/activemq.xml broke节点 -->
   <plugins>
   <simpleAuthenticationPlugin>
   	<users>
   		<authenticationUser username="admin" password="admin" groups="admins,publishers,consumers"/>
   		<authenticationUser username="publisher" password="publisher" groups="publishers,consumers"/>
   		<authenticationUser username="consumer" password="consumer" groups="consumers"/>
   		<authenticationUser username="guest" password="guest"  groups="guests"/>
   	</users>
   </simpleAuthenticationPlugin>
   </plugins> 
   ```

#### 数据存储（持久化）

##### KahaDB

默认的持久化策略。

带索引，带锁。

```xml
<persistenceAdapter>
	<kahaDB directory="文件存放位置" journalMaxFileLength="保存消息的文件大小"/>
</persistenceAdapter>
```

##### AMQ方式

**只适用于5.3版本之前**

##### JDBC存储

数据库配置

```sql
-- 需要三张表
CREATE TABLE `activemq_msgs` COMMENT='Queue和Topic的消息都存在这个表中';
CREATE TABLE `activemq_acks` COMMENT='存储持久订阅的消息和最后一个持久订阅的消息';
CREATE TABLE `activemq_lock` COMMENT='和KahaDB的lock文件类似,确保数据库在某一时刻只有一个Broker在访问';
```

配置文件

```xml
<!-- activemq_home/conf/activemq.xml beans节点 -->
<bean id="mysql-ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
   <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
   <property name="url" value="jdbc:mysql://localhost/activemq"/>
   <property name="username" value="activemq"/>
   <property name="password" value="activemq"/>
   <property name="poolPreparedStatements" value="true"/>
 </bean>
<!-- 修改节点信息 persistenceAdapter -->
<persistenceAdapter>
	<jdbcPersistenceAdapter dataSource="#mysql-ds"/>
</persistenceAdapter>
<!-- 将依赖的jar文件,放在“activemq_home/lib/optional”目录下 -->
```


##### LevelDB存储

官方不建议使用

##### Memory存储

内存存储

##### JDBC Message store with ActiveMQ Journal

集群环境下使用

ActiveMQ Journal，使用延迟存到数据库的方案，当消息来到时先缓存到文件中，延迟后才写到数据库中。**流转批**

### 集群

1. 共享文件
   提供可访问，不能保证数据安全（分摊带宽）
   多Broke去抢文件锁、谁抢到了谁作为master

2. 共享数据库
   提供可访问，不能保证数据安全（需要DB去支持数据安全）
   多Broke去数据库抢锁，谁抢到了谁作为master

3. Zoo Keeper

   需要搭配 level DB

4. Client需要搭配broke-url：failover(uris)使用

### 负载均衡

将两个Broke通过network连接到一个集群组。

自动转发：如果没有消费者，则自动推送到集群

动态订阅：转发者不负责持久化

静态订阅：转发者会持久化

（在开启了自动转发的情况下）