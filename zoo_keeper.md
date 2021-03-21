#### leader

主节点

#### follower

追随者(从节点)

#### client

客户端



#### 状态

1. 可用状态
2. 不可用状态（Leader挂掉）

不可用状态恢复到可用状态极快



#### watch 监听数据变化

构造函数：session级别的，和path、node没有关系。

只包括：当前session建立连接

其它的监听，调用需要使用 ZooKeeper.addWatch(path,mode)