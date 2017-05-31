## Hive Shell ##

`hive` 启动Hive Shell，  参数：
 - `-f [script.q]` 执行hive脚本
 - `-e 'HiveQL'` 执行单条hive脚本
 - `-s` 不显示运行时详细信息（如查询所花时间）默认是显示详细信息
 - `--config` 指定包含配置文件的目录来重设配置
 - `--hiveconfig` 为单个hive会话配置某个项
 - `--service [type]` 指明使用那种服务
  - `cli` 默认值，以shell方式提供交互服务
  - `hiveserver` 提供Thrift服务的服务器方式运行（接受Thrift，JDBC，ODBC客户端）
  - `hwi` Web接口，在http://host:9999/hwi中提供服务
  - `jar` 运行jar包中的程序
  - `metastore` 作为独立的metastore来运行
 - `-auxpath [path]` 指定需要附加的JAR文件路径，相当于ADD JAR path


## HiveQL ##

创建数据库

```
CREATE DATABASE [dbname] USE [dbname]
```

删除数据库

```
DROP DATABASE [dbname]
```

创建表

```
CREATE TABLE tablename(colname Type, ...) [PARTITION BY (pcol1 Type, pcol2 Type)] --PRATITION后面的列为分区列
[CLUSTERED BY (col) SORTED BY (col ASC) INTO 2 BUCKETS]  --划分桶的同时对数据按某列进行归并排序
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' --指定每行以`\t`结尾
LOAD DATA [LOCAL] INPATH `input/sample.txt` --LOCAL表示从本地文件系统加载数据，LOCAL文件将被移动，非LOCAL将被复制
PARTITION (col1='...', col2='...')
OVERWRITE INTO TABLE tablename  --将加载的数据写入表;
```

> 分区列数据不存在于文件中，形式表现为目录。所以，在SELECT语句中查找分区列会从目录中进行查找。

创建外部表
外部表中，Hive不负责文件的管理

```
CREATE EXTERNAL ... LOCATION '...' --指明表的存储位置
LOAD ...
```

使用现有模式创建一个空表：
`CREATE TABLE new_table LIKE existing_table`

调试信息

```
EXPLAIN 'HiveQL' --HiveQL的AST；执行各阶段的依赖图，每个阶段的信息
EXPLAIN EXTENDED "..." --显示更详细的信息，如`view_name`
```

插入数据

动态分区插入

> 动态分区插入需要`hive.exec.dynamic.partition`为true

```
INSERT OVERWRITE TABLE new_table PARTITION (dt) SELECT dt FROM table
```

插入数据的另一种写法

```
FROM src_table (INSERT OVERWRITE TABLE target_table SELECT col1, col2)
[(INSERT OVERWRITE TABLE target_table2 SELECT col1, col2)] --多表插入可以写多遍
```

插入变体 - 由查询创建表

```
CREATE TABLE target AS SELECT col1, col2 FROM src_table;
```

排序

```
ORDER BY [col] ASC / DESC --全局排序;
SORT BY [col] ASC / DESC --在每个reducer产生的文件内排序
SORT BY [col1] ASC / DESC DISTRIBUTE BY [col2] --控制一个行进入哪一个reducer
CLUSTER BY [col] 与上一条命令等价
```

连接查询

```
SELECT table1.*, table2.*
FROM table1 JOIN table2 (table1.id = table2.id [AND table1.id2 = table2.id2 ...])
[JOIN table3 ON ...]
```

> 原则上，数据多的表在后有利于性能

各种外连接

```
LEFT OUTER JOIN
RIGHT OUTER JOIN
FULL OUTER JOIN
```

半连接

```
SELECT * FROM table_left LEFT SEMI JOIN table_right
ON table_left.id=table_right.id
```

等价于

```
SELECT * FROM table_outer
WHERE table_outer.id IN (SELECT id FROM table_inner);
```

map连接

```
SELECT /*+ MAPJOIN (small_table) */ big_table.*, small_table.*
FROM big_table JOIN small_table ON (big_table.id=samll_table.id)
```

子查询

```
SELECT col1,col2
FROM (SELECT ...FROM ...) aliasname
```

视图

```
CREATE VIEW view_name AS
SELECT * / [col1,col2,...] FROM ...
```

取样

```
SELECT * FROM table TABLESAMPLE (BUCKET x OUT OF y [ON col]) --分桶表取样 ;
```

写入

```
STORED AS TEXTFILE / SEQUENCEFILE / RCFILE
ROW FORMAT SERDE --指定处理文件格式的SERDE
WITH SERDEPROPERTIES (key=val) -- 指定SERDE参数
```

serde列表 p473 表

> OUT OF后面的数字为桶数的约数即可

创建函数

```
CREATE TEMPORARY FUNCTION func_name AS 'package.className'
```

使用脚本过滤

```
SELECT TRANSFORM (col1, col2, col3) --TRANSFORM后面元祖作为脚本输入
USING 'script' AS col1, col2 --AS后面为脚本输出对应的列
```

使用脚本进行Map，Reduce

```
FROM(
    FROM table
    MAP col1, col2, col3 --Map脚本输入
    USING 'map_script'
    AS col1, col2   --Map脚本输出的列
) map_output   --Map脚本输出到的集合
REDUCE col1, col2   --Reduce脚本输入
USING 'reduce_script'
AS col1, col2 --Reduce脚本输出
```

修改表

```
ALTER TABLE old_table RENAME to new_table
ALTER TABLE table ADD COLUMES (col3 Type);
```

> 修改表对于内部表，一般是修改目录名和元数据；对于外部表只修改元数据

删除表

```
DROP TABLE
```

> 对于内部表删除文件和元数据，对于外部表只删除元数据

> 上面命令同时删除模式与数据，如果想要清空数据且保留模式，可使用：
  `dfs -rm /user/hive/warehouse/tablename`

## 命令(用于Hive Shell) ##

`SET config-item` 查看配置项的值
`SET config-item:value` 修改配置项的值
`SHOW LOCKS` 查看当前的锁
`SHOW FUNCTION` 函数列表
`DESCRIBE FUNCTION [dbname]`
`SHOW PARTITIONS` 显示分区


## 设置 ##

### 常用设置项 ###

`hive.exec.dynamic.partition` 是否允许动态分区插入

### 设置优先级 ###

Hive设置优先级由高到低

 - Hive SET命令
 - `-hiveconfig`选项
 - hive-site.xml
 - hive-default.xml
 - hadoop(core/hdfs/mapred)-site.xml
 - hadoop(core/hdfs/mapred)-default.xml

## 日志 ##

一般错误日志 ，本地文件系统的`/tmp/USER/hive.log`中

## 类型 ##

p461类型

STRUCT<a:T1, b:T2, ...>
ARRAY<T>
MAP<K,V>

## 用户定义函数 ##

> 仅支持JAVA编写

**UDF**  -输入单行数据，输出单行数据（继承org.aphace.hadoop.hive.ql.exec.UDF）
**UDAF**  -输入多行数据，输出单行数据（继承org.aphace.hadoop.hive.ql.exec.UDAF）
**UDTF**  -输入单行数据，输出多行数据（继承org.aphace.hadoop.hive.ql.exec.UDTF）

## Hive体系结构 ##

```@viz
digraph G {
  compound=true;
  subgraph cluster_1 {
  label = "Hive Clients";
  "Hive Trift Clent"
  "Hive JDBC Clent"
  "Hive ODBC Clent"
  }

 "Hive Trift Clent"->"Hive Server"
  "Hive JDBC Clent"->"Hive Server"
  "Hive ODBC Clent"->"Hive Server"
"Hive Server"[lhead=cluster_1]
    subgraph cluster_2 {
        label = "Hive S
ervice";
        CLI->"Unknown"
        "Hive Server"->"Unknown"
        "Hive Web Interface" ->"Unknown"
"Unknown"
       "Metastore"
       "FileSystem"
       "JobClient"
"Unknown"->Metastore
"Unknown"->FileSystem
"Unknown"->JobClient
    }
Metastore->"Metastore Database"
FileSystem->"Hadoop Cluster"
JobClient->"Hadoop Cluster"
   "Metastore Database"[shape=octagon]
   "Hadoop Cluster"
}
```
