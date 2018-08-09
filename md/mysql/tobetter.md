# mysql优化专题
目录
- [数据库设计要合理（3范式）](#数据库设计要合理)
- [索引类型](#索引类型)
- [分表分库](#分表分库)
- [读写分离](#读写分离)
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
- 分库
    - 垂直分割 
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
    - 水平分割 根据地市，拆分数据库

- 分表
    - 水平分割
    ```java
    表数据达到一定级别，查询效率不高，需要分表
    分表策略：
        按范围 如id为1~10000的放表1，10001~20000的放表2
        按年
        取模
    ```
    - 垂直分割
    ```java
     把不经常查询的字段，或者数据量大的字段，单独放入另一张表。
     如：
      用户基本信息 id,name,头像,性别
      用户详细信息 地址，爱好，个人介绍
    ```
- 取模分库分表
```java
什么是取模？
    id % mod 的余数，为表的序号
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
### sql语句调优

- 定位慢查询：在my.ini中配置，然后有慢查询上查询mysql_slow.log文件，找到对应的sql语句
    ```sql
    [mysqld]
    log="C:/temp/mysql.log" #日志文件
    log_slow_queries="C:/temp/mysql_slow.log" #慢查询的日志文件
    long_query_time=1 #慢查询时间（默认10秒） 这里超过1秒就算慢查询
    ```
- 创建索引

    - 为什么用索引？
    ```java
    避免全表扫描
    ```
    - 索引的优缺点？
    ```java
    优点：提升查询效率
    缺点：创建和更新数据，索引也需要跟着维护，消耗时间；索引文件占空间
    ```
    - 建立索引的原则
    ```java
    更新少查询多的字段，建立索引，反之避免过多索引
    数据量小的无需建立索引
    值少的列，无需建立索引，如性别 只有男女或者0 1
    ```
    - MyISAM和InnoDB存储引擎中所有的区别
    ```java
    MyISAM支持hash索引和b+tree索引
    InnoDB默认并且只能为b+tree索引
    ```
    - [索引类型](#索引类型)
- sql语句
    ```java
    不要使用 select *
    使用limit 如limit1查一条； limit10, 20查10条
    使用join代替子查询
    避免随机取记录 ORDER BY RAND() 
    批量insert，使用一条语句 insert into t values(1,'name1'),(2,'name2'),(3,name3)...
    ```
### 索引类型
索引类型
- 普通索引
```java
基本索引类型，，允许在定义索引的列中插入重复值和空值，为了查询效率
如 手机号、姓名
ALTER TABLE 表名 ADD INDEX 索引名(`索引字段`);
```
- 主键索引
```java
为表的主键设置的索引，也是唯一索引，不允许null
如 id
```
- 唯一索引
```java
索引列中的值必须是唯一的，但是允许为空值，一张表中可以有多个唯一索引
如 订单号
```
- 组合索引
```java
将多个列建立索引
如index(name_phone_age)， 查询的时候where name=xx and phone=xx and age=xx 使用索引
但是如果不是按最左列查询，则索引无效，如按 where phone=xxx and age=xxx 会全表扫描
如果where name=xxx and phone=xxx and age > 10，这样只有name和phone索引生效，age失效
所以使用组合索引，查询顺序很重要
```
- 全文索引（MyISAM）
```java
全文索引适用于 Match Against 操作，而不是普通的where条件操作
```
- 空间索引（MyISAM）
```java
适合地理位置搜索
```

