# mysql优化专题
目录
- [数据库设计要合理（3范式）](#数据库设计要合理)
- [索引](#索引)
- [分表分库](#分表分库)
- [读写分离](#读写分离)
- [存储过程](#存储过程)
- [mysql服务器最大连接数](#最大连接数)
- [sql语句调优](#sql语句调优)
### 数据库设计要合理
三范式
```java
1.每一列不可再分割，说的是每一列的原子性
2.表中的每列都和主键相关 确保数据的唯一性，比如订单表中不应该有产品信息
3.不要有冗余数据
```
五约束
```java
1.PRIMARY KEY:设置主键约束；
2.UNIQUE：设置唯一性约束，不能有重复值；
3.DEFAULT 默认值约束，height DOUBLE(3,2)DEFAULT 1.2 height不输入是默认为1,2
4.NOT NULL：设置非空约束，该字段不能为空；
5.FOREIGN key :设置外键约束 **只有INNODB存储引擎支持外键**
```
特别说明
```java
3范式，只是一个规范，但具体的设计要以业务情况为准
```
### 索引
索引类型
- 普通索引
- 主键索引
- 唯一索引
- 全文索引
### 分表分库
分表分库分区
- 分区
```java
就是把一张表的数据分成N个区块，在逻辑上看最终只是一张表，但底层是由N个物理区块组成的

分区解决的问题: 提升查询效率
mysql5 开始支持分区功能
实现demo：
CREATE TABLE sales (
    id INT AUTO_INCREMENT,
    amount DOUBLE NOT NULL,
    order_day DATETIME NOT NULL,
    PRIMARY KEY(id, order_day)
) ENGINE=Innodb 
PARTITION BY RANGE(YEAR(order_day)) (
    PARTITION p_2010 VALUES LESS THAN (2010),
    PARTITION p_2011 VALUES LESS THAN (2011),
    PARTITION p_2012 VALUES LESS THAN (2012),
PARTITION p_catchall VALUES LESS THAN MAXVALUE);
```
- 分库：垂直分割
```java
数据库单表的记录行数可能达到千万级甚至是亿级，采用Master-Slave复制模式的MySQL架构
写入操作还是集中在Master，读取操作集中在Slave，但是单个Master挂载的Slave也不可能无限制多，并且写入能力有限
这时候要考虑分库
或者分布式系统，各个微服务单独的库，降低耦合度

优点：提高数据库写入能力和并发访问能力，海量数据存储
缺点：
    需增加硬件机器支持
    跨数据库的事务（分布式事务、事务补偿也就是事务的最终一致性）
    join查询（查询2次，程序中组合数据）
分库策略：取模
```
- 分表：水平分割
```java
表数据达到一定级别，查询效率不高，需要分表
分表策略：
    按范围
    按年
    取模
```
- 取模分库分表
```java
什么是取模？
    id % mod
    比如id：1 2 3 4 5 6
    要分3个表，这时候模mod=3,
    1、4分到--->1表，2、5分到--->2表，3、6分到--->0表
```
### 读写分离
```java
让master数据库处理写操作，slave数据库处理读操作。
master将写操作的变更同步到各个slave节点。
MySQLProxy（mysql官方提供的数据库代理服务，可实现读写分离）。对多接点slave，还可以实现负载。
```
### 存储过程
### 最大连接数
```java
在my.ini文件中配置
```
### sql语句调优
..

