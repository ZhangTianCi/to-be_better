# redis

## Key

是一个对象

除了存`key`的字符串

还根据`value`类型的不同、存其它的东西

**全部：key中还存了encoding**

## Value

### String

#### 字节安全

中文转换成字节存储(xshell 的字节编码)

长度按照字节长度

**key中还存了length**

#### int

#### raw

#### bitmap

setbit bitpos bitcount bitop

### List

**key中还存了头尾指针**

l/r push添加、l/r pop弹出、lrange范围查看、LINDEX按索引获取、LSET按索引设置、LREM移除元素、LINSERT插入、  

栈：同向操作

队列：异向命令

数组：INDEX操作

阻塞、单播队列：B操作

### Hash

点赞、收藏、详情页、

### Set

去重、无序的

可以做交、并、差集

随机操作（抽奖）

### Sorted Set

**添加元素需要带分值**

对元素排序

物理内存左小右大

可以做交、并、差集（需要指定分值的处理方式）