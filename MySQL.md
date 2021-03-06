# MySQL



存储引擎代表了数据结构的组织形式

## InnoDB



#### MyISAM



## 执行计划 explain

### id 

查询的序列号。

1. 序列号越大，越先执行
2. 序列号相同的项可以视为一组，组内按照输出顺序执行

### select_type

查询的类型

1. `SIMPLE`：查询中不包含子查询或者`UNION`
2. 查询中若包含任何复杂的子部分，最外层查询则被标记为：`PRIMARY`
3. 在`SELECT`或`WHERE`列表中包含了子查询，该子查询被标记为：`SUBQUERY`

### table

用到的表

### partitions

表示查询时可能使用的索引

如果是空的，没有相关的索引。

### type 

效率依次从高到低：system > const > eq_ref > ref > range > index ≈ all

**system**：表中只有一行记录。
**const**：使用主键或唯一索引进行比较。
**ref**：查询用到了非唯一性索引，或者关联操作只使用了索引的最左前缀。
**range**：使用索引范围扫描。
**index**：全索引扫描。一般出现在索引条件区分度不高的场景下，未必比全表扫描快
**all**：全表扫描

### possible_keys

可能会用到的索引

### key

实际被用到的索引

### key_len

索引字段的长度

### ref

那个字段、常量被索引使用

### rows

预估的行数

### filtered

存储引擎返回的数据在 server 层过滤后，剩下多少满足查询的记录数量的比例，它是一个百分比。

例如全表扫描后，只有一条记录满足查询条件，那么 filtered 的值就会比较低。

### Extra

Using index：查询使用了覆盖索引，不需要回表。

Using where：两种情况：

1. 查询使用了 where 过滤，但没有用到索引；
2. 有索引的过滤，且不需要回表。

Using index condition：查询用到索引且需要回表。

Using filesort：不能使用索引来排序，用到了文件排序。

Using temporary：用到了临时表（内存或磁盘）。

参考链接：https://blog.csdn.net/u011212394/article/details/103877385

## 索引

主键索引、唯一索引、普通索引、组合索引、全文索引（用于文字）

### 聚簇

聚簇索引：文件和数据在一起（数据是否跟索引的根节点、磁盘数据紧邻在一起）

非聚簇索引：文件和数据不在一起

### 优点和缺点

优点：相关数据在一起、数据访问更快、支持覆盖索引

缺点：提高了IO密集型应用的性能（如果数据都在内存中，则没有优势）、插入速度依赖于插入顺序（数据的维护：索引的维护）、提高了更新的代价（数据块大了会发生移动）、**删除同理**、磁盘页分裂以及页合并的问题。

### 前缀索引

基于string类型。（VARCHAR、TEXT、BLOB）

可以截取前n位的字符串作为索引

```sql
ALTER TABLE `table_name` ADD KEY( column_name(n) );
```

### 技术名词

回表：根据索引找到的主键，去表中查找全部数据

覆盖索引：在非主键索引中找到值，并且所查询的列是id的情况下，叫覆盖索引

最左匹配：在组合索引时（A+B），如果条件是A、A+B，会去索引找，B、B+A则不会

索引下推：如果使用
```sql
where A like 'X%' and B = 'X'	// 没有使用索引
where A = 'X' and B = 'X'		// 使用了A、B
where A = 'X' and B like 'X%'  	// 使用了A、B
where A like '%X | > | < 等范围比较' and B ='X'	// 使用了A、没有使用B
```
在满足 like 条件后，会判断是否满足B（5.6版本划分）__使用范围查询会停止下推__

索引合并：如果触发多个索引，会去每个索引找，并取交集

匹配列左前缀：

```sql
like XX%
like %XX
```

匹配范围值：

```sql
> 'XX'
```



# 索引的作用

1. 覆盖索引
2. 排序ORDER BY（索引列）默认是asc，desc用不到索引，

### 幻读

在当前事务中,执行了Update语句后,前后的查询语句不符。数据从快照读变成了当前读.

#### 解决方法

查询语句添加for update（零件锁）。

此时，其它的事务无法对锁定范围（where条件内的数据）内的数据进行插入。

例如

```sql
-- 事务1
select * from table where columnX = x for update;
-- 事务2
inser into table (column0,column1,column2,columnX)values(0,1,2,x);
-- 如果事务2的cloumnx在事务1的范围内，则插入失败
```

