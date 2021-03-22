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

<font color="orange">浪费线程资源，开辟线程资源需要用户态内核态切换。且多个线程之间的切换浪费系统资源</font>

NIO：非阻塞IO。轮循调用每一个fd判断。

<font color="orange">accpet浪费，需要用户态内核态切换。</font>

多路复用器：SELECT（fds收到FD_SETSIZE的限制），POLL（SELECT的无限制版），EPOLL（事件）

<font color="orange">SELECT/POLL 内核中遍历的浪费</font><font color="orange">EPOLL事件模型，是SELECT/POLL的拓展</font>



