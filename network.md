#### TPC

##### 四元组

Client （IP，PORT），Serve（IP，PROT）

三次握手中的【1】和【2】属于一个四元组

##### 三次握手

1. 服务端
   服务端在创建端口监听时
   1. 建立端口的监听
   2. 在当前线程new一个文件描述符【0】
   3. 三次握手完毕后，new一个文件描述符【1】，但是没有拥有者
2. 客户端
   客户端在创建连接后
   1. 建立端口的传输
   2. 会通过【0】进行通信（三次握手）
   3. 建立一个文件描述符【2】对应服务端的【1】

##### 程序使用

1. 服务端开始监听数据后（accept），会将文件描述符【1】的拥有者变为程序进程id



#### 存疑

C是Client S是Serve

C:P1|S:P ->3

C:P2|S:P ->4 

C上的描述符: 3 4

如果 S上的P 端口断了

两个文件描述符是在什么时候从内核中消失的（心跳？）

#### 网络IO

BIO：阻塞IO，建立连接阻塞，读取数据阻塞。	单线程建立连接，异步多线程读取数据处理。

<font color="orange">accpet阻塞，用户态内核态切换。浪费线程资源，开辟线程资源需要用户态内核态切换。且多个线程之间的切换浪费系统资源</font>

NIO：非阻塞IO。轮循调用每一个fd判断。

<font color="orange">无效的accpet浪费，用户态内核态切换。</font>

多路复用器：SELECT（fds收到FD_SETSIZE的限制），POLL（SELECT的无限制版），EPOLL（事件）

<font color="orange">SELECT/POLL 内核中遍历的浪费</font><font color="orange">EPOLL事件模型，是SELECT/POLL的拓展</font>

##### api使用

ServerSocketChannel 

Selector

|API操作 | SELECT、POLL | EPOLL |
| :- | :-| :-|
| ServerSocketChannel.open() | 创建一个socket | 创建一个socket |
| chanel.bind(new InetSocketAddress(port)) | socket绑定port | socket绑定port |
| chanel.configureBlocking(false); | 给socket设置NIO | 给socket设置NIO |
| chanel.bind(new InetSocketAddress(port))  | socket.create ->fd4 |socket.create -> fd4|
| Selector.open() | jvm实例化一个list |epoll_create->fd5|
| chanel.register(selector,SelectionKey.OP_ACCEPT); | fd4 放入 list |epoll_ctl(fd5,ADD,fd4)懒加载|
|  |  ||



##### 四次分手

| 命令途径 | 命令内容 | 先发送命令方的状态|后发送命令方的状态|
| :-:|:-: | :-| :-|
|先->后|FIN|FIN_WAIT1|ESTABLISHED|
|后->先|FIN_ACK|FIN_WAIT2|CLOSE_WAIT|
|先->后|FIN|CLOSING|CLOSING|
|后->先|ACK|TIME_WAIT<br />报文活动时间的两倍|CLOSE<br />这是一个顺时状态|

