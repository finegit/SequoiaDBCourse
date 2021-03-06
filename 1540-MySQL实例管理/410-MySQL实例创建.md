---
show: step
version: 15.0
enable_checker: true
---



# MySQL 实例创建

## 课程介绍

本课程将带领您在已经部署 SequoiaDB 巨杉数据库引擎及安装了 SequoiaSQL-MySQL 程序的环境中，创建 MySQL 实例及数据库和数据表，并向数据表中写入数据进行测试。

#### 请点击右侧选择使用的实验环境

#### 环境架构：

本课程中 SequoiaDB 巨杉数据库的集群拓扑结构为三分区单副本，其中包括：1 个 SequoiaSQL-MySQL 数据库实例节点， 1 个引擎协调节点， 1 个编目节点与 3 个数据节点。

![图片描述](https://doc.shiyanlou.com/courses/1469/1207281/8d88e6faed223a26fcdc66fa2ef8d3c5)

如若详细了解 SequoiaDB 巨杉数据库系统架构请点击以下链接：
* [SequoiaDB 系统架构](http://doc.sequoiadb.com/cn/sequoiadb-cat_id-1519649201-edition_id-0)

#### 实验环境

课程使用的实验环境为 Ubuntu Linux 16.04 64 位版本。SequoiaDB 巨杉数据库引擎以及 SequoiaSQL-MySQL 实例均为 3.4 版本。


## 切换用户及查看数据库版本

切换至部署 SequoiaDB 巨杉数据库和 SequoiaSQL-MySQL 实例的操作系统用户并查看数据库引擎版本。

#### 切换到 sdbadmin 用户

```shell
su - sdbadmin
```
>Note:
>
>用户 sdbadmin 的密码为 `sdbadmin`

#### 查看巨杉数据库版本

查看 SequoiaDB 巨杉数据库引擎版本：

```shell
sequoiadb --version
```
操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1469/1207281/b4082b0d6d6bdf89d229aa713a53759d)


## 查看节点启动列表

查看 SequoiaDB 巨杉数据库引擎节点列表：

```shell
sdblist 
```

操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1469/1207281/02fcaa58ac27e91688ead137fa748d6e)

>Note:
>
>如果显示的节点数量与预期不符，请稍等节点初始化完成并重试该步骤。

## 创建 MySQL 实例

此实验环境已经安装了 SequoiaSQL-MySQL 程序，我们直接添加 MySQL 实例。

1）切换到 SequoiaSQL-MySQL 安装目录；

```shell
cd /opt/sequoiasql/mysql
```

2）添加实例；

```shell
bin/sdb_sql_ctl addinst myinst -D database/3306/
```

>Note:
>
> 指定实例名为 myinst，该实例名映射相应的数据目录和日志路径，用户可以根据自己需要指定不同的实例名，实例默认端口号为 3306 。

3）查看实例，可以看到实例名为 myinst 的数据和日志目录信息；

```shell
bin/sdb_sql_ctl listinst
```
操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/d7dba8ae7ad2523dfff2a4543bcaf53d-0)

4）查看实例状态；

```shell
bin/sdb_sql_ctl status
```

操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/f6502e68b949510b8ddf02aa13284f61-0)

## 创建数据库及数据表

进入 MySQL shell ，连接 SequoiaSQL-MySQL 实例并创建 company 数据库实例，验证实例的创建及使用是否成功。

#### 创建数据库

1）登录 MySQL Shell；

```shell
/opt/sequoiasql/mysql/bin/mysql -h 127.0.0.1 -P 3306 -u root
```

2）在 MySQL 实例中创建新数据库 company，并切换到 company；

```sql
CREATE DATABASE company ;
USE company ;
```

3）查看 MySQL 实例中的数据库；

```sql
SHOW DATABASES ;
```

#### 创建分区表

在 SequoiaSQL-MySQL 实例中创建的表将会默认使用 SequoiaDB 数据库存储引擎，包含主键或唯一键的表将会默认以唯一键作为分区键，进行自动分区。

1）在 MySQL 实例的 company 数据库中创建数据表 employee；

```sql
CREATE TABLE employee (
	empno INT,
	ename VARCHAR(128),
	age INT,
	PRIMARY KEY (empno)
) ENGINE = sequoiadb COMMENT = "雇员表, sequoiadb:{ table_options : { ShardingKey : { 'empno' : 1 } , ShardingType : 'hash' , 'Compressed' : true , 'CompressionType' : 'lzw' , 'AutoSplit' : true , 'EnsureShardingIndex' : false } }" ;
```

2）查看数据库引擎；

```sql
SHOW ENGINES ;
```

操作截图:  

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/d2caef2b1c019578ea0f2d211678ea01)

3）查看 company 数据库中创建分区表 employee；

```sql
SHOW CREATE TABLE employee ;
```

操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/9234e051cb07f00ff82c8ad8245610cf-0)


## 数据表中写入数据并查询

在 SequoiaSQL-MySQL 实例中创建的表完全兼容MySQL语法和协议，用户可以使用SQL语句访问 SequoiaDB 数据库，完成对数据的增、删、查、改操作以及其他MySQL语法操作。

1）在分区表 employee 中插入数据；
```sql
INSERT INTO employee VALUES (10001, 'Georgi', 48) ;
INSERT INTO employee VALUES (10002, 'Bezalel', 21) ;
```

操作截图：

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/33d20f44d4f70ad15132164cdb2ca6f9-0)

2）查询分区表 employee 中的数据；
```sql
SELECT * FROM employee ;
```

操作截图:

![图片描述](https://doc.shiyanlou.com/courses/1540/1207281/03af12a43819e244a320314f21bb391c-0)

## 总结
通过本课程，我们学习了在 SequoiaSQL-MySQL 实例上创建数据库和数据表，并在数据表上进行了写入和查询操作。
