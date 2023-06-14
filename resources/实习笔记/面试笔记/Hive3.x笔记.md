# Hive入门

## hive概述

将结构化的数据文件映射为一张表，并提供类SQL查询功能。就是Hadoop客户端，将HQL转化为MR程序。

Hive中每张表的数据存储在HDFS
Hive分析数据底层的实现是MapReduce（也可配置为Spark或者Tez）
执行程序运行在Yarn上

hive是将SQL转为MR的数仓工具，用了HQL查询语言，和SQL非常接近，所以很容易理解为数据库，实际上除了查询语言类似之外，没有其他相似之处。

优点：学习成本低，海量数据，统一的元数据管理（Hive中的表在Hadoop中是目录；Hive中的数据在Hadoop中是文件）
缺点：效率不高，自动生成的MR作业不够智能；调优困难。

## 架构

1）用户接口：CLI（command-line interface）、JDBC/ODBC
2）metastore 3）hiveserver2 4）hadoop

![image-20230422112450529](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230422112450529.png)

Hive组件两个服务：**metastore**：提供元数据访问接口（数据库，表信息，字段信息），不负责存储元数据，具体元数据存储在mysql中。
**HiveServer2**：提供jdbc/odbc访问接口（客户端并未直接访问Hadoop集群，而是由Hivesever2代理访问）；用户认证功能（hadoop集群的启动用户/HiveServer2启动用户/客户端登录用户）

用户身份取决于是否开启**用户模拟**功能：若启用，则Hiveserver2会模拟成客户端的登录用户去访问Hadoop集群的数据（ClientA ClientB ClientC），如果不启用，则Hivesever2会统一用Hadoop启动用户的身份（atguigu）访问。**默认启用，一定要启用**，完成用户权限隔离，否则全是一个用户了。



![image-20230424153538275](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230424153538275.png)

## 操作

事先打开hadoop集群，开启hiveservice：myhadoop.sh start 	$HIVE_HOME/bin/hive --service hiveserver2
关闭：hiveservices.sh stop myhadoop.sh stop
关机：shutdown -h now

default数据库：hdfs://hadoop102:8020/user/hive/warehouse
Hive3.1.3，hadoop3.1.3,MYSQL 5.7.28

```shell
// 启动HIVE服务，xshell关闭连接页面依然存在
bin/hive --service hiveserver2 // 启动hiveServer2，但这个xshell窗口会阻塞在这里，可以后台运行
nohup bin/hiveserver2 1>/dev/null 2>/dev/null & // 0指标准输入，1标准输出，2标准错误，将标准输出1和标准标准错误2的信息都写到/dev/null
// 连接beeline或者Datagrip图形化界面
bin/beeline -u jdbc:hive2://hadoop102:10000 -n atguigu -- beeline
```

## Mysql VS HiveSQL

|          | MySQL                                  | Hive                                                         |
| -------- | -------------------------------------- | ------------------------------------------------------------ |
| 查询语句 | SQL结构化查询语句，面向数据的          | 类SQL HQL，是面向对象的                                      |
| 存储位置 | mysql本地或设备，存储数据              | hive本身不存储数据，交给hadoop                               |
| 排序     | 只有order by                           | 四种                                                         |
| 数据模型 | 表，表-分区                            | 表，内部和外部，分区，分桶                                   |
| 数据更新 | 经常CRUD                               | 一般都是插入检索，很少更新删除                               |
| 语法     | 大小写不敏感                           | 更严格，大小写敏感                                           |
| 索引     | 支持，毫秒级响应                       | 不支持索引，每次扫描所有数据，底层是MR/spark/Tez，并行计算，适用于大数据量 |
| 引擎     | innoDB                                 | MR/spark/Tez                                                 |
| 数据类型 | 没有string，array，map，struct这些类型 |                                                              |
|          |                                        |                                                              |

|                                | SQL                 | HQL                                 |
| ------------------------------ | ------------------- | ----------------------------------- |
| 插入数据语句，建表语句都很不同 | insert into student | insert into/overwrite table student |
| 日期函数用法很不同             |                     |                                     |
| 开窗函数                       | 只有rows between    | rows range between 默认是range      |
| 子查询                         | 支持完整            | 不支持if case里面还有select子查询   |

[日期使用区别](#日期函数)

# DDL & DML

## 数据库操作

类似于MySQL

| 用途           | 关键字                                   | 备注                                                         |
| -------------- | ---------------------------------------- | ------------------------------------------------------------ |
| 创建数据库     | create location dbproperties             | location是hdfs的路径，默认是/user/hive/warehouse<br />dbproperties是键值对的数据库属性 |
| 查看数据库信息 | discribe desc                            | 查看基本信息，加上extended关键字后，可在parameters一列查看到上面的dbproperties |
| 修改数据库     | alter dbproperties、location、owner user | 修改数据库location，不会改变当前已有表的路径信息，而只是改变后续创建的新表的默认的父目录。 |
| 删除数据库     | drop restrict cascade                    | 默认restrict，非空数据库不删除；<br />cascade：有数据也全删掉。 |
| 切换数据库     | use                                      |                                                              |
|                |                                          |                                                              |

```mysql
-- 1.创建数据库，location dbproperties 可选字段
create database 
if not exsits database_hive3 
location hdfs_path
with dbproperties ("create_data" = "2023-4-24", "create_user" = "sxq");

-- 2.查看数据库信息 discribe desc
describe database database_hive3; -- 基本信息
describe database EXTENDED database_hive3; -- 详细信息，可见dbproperties
 -- 3.修改dbproperties，这个set类似Neo4j的，有属性的话修改，没有的话增加
 alter database database_hive3 set dbproperties ("create_data1" = "2023-5-1");
-- 4.删除数据库
drop database db_hive3 cascade; 
```

## 数据表操作

| 用途                                   | 语法                                                         | 备注                                                         |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1.普通建表，查询建表，复刻表结构create | TEMPORARY EXTERNAL<br />row format stored as                 |                                                              |
| 2.查看表show desc                      | show in like  展示表名<br />desc extended formatted 展示表结构，列，数据类型等 | 只能修改元数据（desc看到的），不能修改hdfs文件上的（select看到的） |
| 3.修改表alter                          | 修改表名，列的增加、更新、替换                               |                                                              |
| 4.删除表drop                           | drop table if exists table_name;                             |                                                              |
| 5.清空表truncate                       | truncate table table_name;                                   | truncate只能清空管理表，不能删除**外部表**中数据             |

### 查看表

```mysql
show tables in default like '*t*'; -- * 表示任意个字符，*t*可以匹配teacher,student
show create table  teacher2; -- 展示建表语句
desc teacher; -- 查看表的列，数据类型等
desc formatted teacher; -- 展示详细信息
desc extended teacher; -- 格式化详细信息
```

### 修改表

重命名表 ALTER TABLE table_name **rename to** new_table_name

增加列：该语句允许用户增加新的列，新增列的位置位于**末尾**。
ALTER TABLE table_name ADD COLUMNS (col_name data_type [COMMENT col_comment], ...)

更新列：该语句允许用户修改指定列的**列名、数据类型、注释信息**以及在表中的位置。
ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment] [FIRST|AFTER column_name]

替换列：语句允许用户用新的**列集**替换表中原有的全部列。最为灵活，**可以实现前两个**
ALTER TABLE table_name REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...)

### 建表3种:star:

普通建表（类似sql，最常用）
Create Table As Select（CTAS）：利用select返回值直接建表，表的结构和查询语句的结构保持一致，包含返回内容。
Create Table Like：允许用户复刻表结构，只复制结构，不包含数据。



create [**temporary**] [**external**] table [if not exists] [db_name.]table_name  -- **内部表外部表**
[(col_name data_type [comment col_comment], ...)]
[comment table_comment] -- 上面这几个每次都用
[**partitioned** by (col_name data_type [comment col_comment], ...)] -- **分区表**
[**clustered** by (col_name, col_name, ...)  [sorted by (col_name [asc|desc], ...)] into num_buckets buckets]--**分桶表**
[**row format** row_format] -- textfile用到,指定一行的serde，orc不用
[**stored** as file_format] -- **文件格式**，textfile orc parquet
[location hdfs_path] -- 表的**存储路径**，默认是数据库路径/表名（是目录）
[tblproperties (property_name=property_value, ...)]

#### 内部表和外部表

|        | 关键字             | 描述                                                         |
| ------ | ------------------ | ------------------------------------------------------------ |
| 内部表 | ==默认==都是内部表 | hive管理该表的元数据、HDFS数据。删除时元数据和HDFS上全部删除。hdfs上的目录都没有了。 |
| 外部表 | EXTERNAL           | hive只管理元数据，不管理HDFS的，认为HDFS数据是别人的。删除时只删除元数据，hdfs不变。 |
| 临时表 | TEMPORARY          | 当前会话可见，关闭会话就自动删除了，测试用                   |

#### 数据类型

| Hive 基本数据类型 | 说明                                                    | 备注                                       |
| ----------------- | ------------------------------------------------------- | ------------------------------------------ |
| tinyint           | 1byte有符号整数                                         |                                            |
| smallint          | 2byte有符号整数                                         |                                            |
| **int**           | 4byte有符号整数                                         |                                            |
| bigint            | 8byte有符号整数                                         |                                            |
| **boolean**       | 布尔类型，true或者false                                 | ==mysql没有boolean==                       |
| float             | 单精度浮点数                                            |                                            |
| double            | 双精度浮点数                                            |                                            |
| **decimal**(16,2) | 精准数字，没有精度丢失，金融相关                        | decimal(16,2) ，小数+整数最多16位，小数2位 |
| **varchar**(20)   | 字符序列，**需**指定最大长度，最大长度的范围是[1,65535] | varchar(32)                                |
| **string**        | 字符串，**无需**指定最大长度                            | ==mysql没有string==                        |
| **timestamp**     | 时间类型                                                |                                            |
| binary            | 二进制数据                                              |                                            |

| Hive复杂类型 | 说明                       | 定义                         | 取值       |
| ------------ | -------------------------- | ---------------------------- | ---------- |
| array        | 数组，用法类似java         | array<string>                | arr[0]     |
| map          | key-value对 ，用法类似java | map<string, int>             | map['key'] |
| struct       | 结构体，C的结构体          | struct<id:int,  name:string> | struct.id  |

#### 数据类型转换

1.隐式自动转换：

自动数据转换时，会找到两者都能转换成为的一个类型去统一。类似最小公倍数。(string + int :arrow_right:double)

a. tinyint:arrow_right:smallint:arrow_right:int:arrow_right:bigint。
b. 所有int、float和string类型都可以 :arrow_right: double。
c. tinyint、smallint、int:arrow_right:float。
d. boolean类型不可以转换为任何其它的类型。

2.显示手动转换cast (expr as <type>)需要保证能转成，否则是null

```
select cast('12' as int) as res; -- integer 12
select cast('1qw2' as int) as res; -- null
```

#### row format:star:

serde是Serializer and Deserializer的简写。Hive使用SERDE序列化和反序列化每行数据。
从表中读数据：HDFS files --> **Input**FileFormat --> <key, value> --> Deserializer（反序列化去对象） --> Row object
往表中写数据到HDFS：Row object --> Serializer（序列化存储） --> <key, value> --> **Output**FileFormat --> HDFS files

row format指定一行的serde

语法1：Delimiter自定义。对文件中的每个字段按照用户指定的分割符进行分割，使用默认的SERDE。

```mysql
row foramt delimited 
[fields terminated by char] -- 列分隔符
[collection items terminated by char] -- map，struct，aarray元素分隔符
[map keys terminated by char] -- map的key value分隔符。struct存储时候不存储key属性名的，只存储属性值，属性名定义在mysql中了
[lines terminated by char] -- 行分隔符
[null defined as char] -- 空值存储，默认\N
```

```mysql
create table if not exists student (
    id int,
    name string
)
row format delimited fields terminated by '\t';
```

语法2：指定其他serde比如JSON_SERDE处理json。

```mysql
create table if not exists teacher (
    name string,
    friends array<string>,
    students map<string, int>,
    address struct<street:string,city:string, postal_code:int>
) row format serde 'org.apache.hadoop.hive.serde2.JsonSerDe'
location '/user/hive/warehouse/teacher';
show create table teacher;
select friends[0],students['xiaohaihai'],address.city from teacher; -- address.city在dataGrip报错，但是hive命令行没问题
```

#### stored as

指定存储的文件格式：textfile（默认值，会将inputformat转为textInputformat，outputFormat转为textOutputFormat），sequence file，orc file（列式存储）、parquet file（列式存储）等等。会根据指定的文件格式，后台自动转换位对应的input/outputFormat。

#### CTAS 查询建表

只能建内部管理表、临时表，不能是external

#### Create table like

允许外部表、内部表、临时表，多了一个字段 **like** exsit_table_name指定复刻哪一张表结构

```mysql
create table teacher1 as select name from teacher; -- 将teacher的name字段建表teahcher1
create table teacher2 like teacher; -- 复刻表teacher为teacher2
```

## DML

一般只做insert，很少做update和delete。hive作为海量数据的分析计算引擎，很少随机修改和删除一条语句，并且效率很低，所以极少用到。

### Load

**load data [local] inpath** 'filepath' [**overwrite**] into table tablename [**partition** (partcol1=val1, partcol2=val2 ...)];

local：默认是hdfs上的filepath，有local表示本地的filepath，不是jdbc本机，而是hiveserver2本机。

overwrite：默认into表示追加，有overwrite into表示覆盖已有数据（**高危操作**）。

partition：分区表需指定分区上传数据。

```mysql
hadoop fs -put teacher.txt /user/hive/warehouse/teacher -- 命令行直接上传，和load的效果类似
load data local inpath '/opt/module/datas/student.txt' overwrite into table student;
```

### insert

将查询结果加入某个已存在的表：
**insert** (**into** | **overwrite**) **table** tablename [**partition** (partcol1=val1, partcol2=val2 ...)] select_statement; 追加和覆盖

将给定values插入表中：语法类似前面的，可选into / overwrite

将查询结果写入目标路径：只能用overwrite覆盖。这个路径一定要慎重，如果少写一级，**这一级的全部内容都没有啦**
**insert** **overwrite** [**local**] **directory** directory  [**row format** row_format] [**stored as** file_format] select_statement;

```mysql
insert into table student1
select
    id,
    name
from student;

insert into table student1 values(1,'wangwu'),(2,'zhaoliu');

insert overwrite local directory '/opt/module/datas/student' row format serde 'org.apache.hadoop.hive.serde2.JsonSerDe' select id,name from student


```

### export import

用于两个Hive实例之间的数据迁移，导入导出要配合使用。

导出：可将表的数据和元数据信息一并导出到HDFS路径
**export table** tablename **to** 'export_target_path'

导入：可将Export导出的内容导入Hive，表的数据和元数据信息都会恢复
**import** [**external**] **table** new_or_original_tablename **from** 'source_path' [**location** 'import_target_path']

datagrip很多时候执行会有问题，比如这个import table去掉分号才能执行，但是hive客户端和beeline都不会有问题

```mysql
export table default.student to '/user/hive/warehouse/export/student';
import table student2 from '/user/hive/warehouse/export/student';
```

# 查询语句:star:

## 基础语法

顺序不可变

**select** [**all** | **distinct**] name,id,..., --默认是all，不去重
**from** table_reference  [**where** where_condition]  -- 过滤，对每行的数据过滤
[**group by** col_list]    -- 分组查询
[**having** col_list]     -- 分组后过滤，对每组进行过滤
[**order by** col_list]    -- 排序
[**cluster by** col_list  | [**distribute by** col_list] [**sort by** col_list]
[**limit** number] 

limit 2,3代表从2行开始，抓取三行；limit 5代表从1开始，抓取5行

where和having后面常用的操作符：

| 操作符                 | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| is null is not null    | （判空，A is null）                                          |
| in("研发岗","销售岗")  |                                                              |
| A between B and C      | （两个闭区间）                                               |
| A (not) like B模糊匹配 | B可以包括%代表任意个任意字符，_表示1个任意字符<br />show里面的like * 代表任意个任意字符 |
| A rlike B, A regexp B  | B正则表达式                                                  |
| and or not             |                                                              |

```mysql
select *
from emp 
where ename like '张%' or ename like '赵%'; -- 这个地方要写全，ename like or ename like
```

## 聚合函数

输入多行数据，输出多行数据汇总在一起的一个值

count(*)所有行数，包含null；count(number)某列行数，不包含null；
max min sum avg 不包含null

```mysql
set mapreduce.framework.name = local; -- 不让在yarn上，选择本地，速度快一些
tail -500 /tmp/atguigu/hive.log -- 查看日志

```

![image-20230426103946826](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230426103946826.png)

## group by 和having

将数据按照deptno分为若干组，把**聚合函数**（max,min,count）应用在每一组里面，select里面可以加两种字段：聚合函数，或者是group by后面的deptno，但**不能加别的字段**ename之类的。每组只返回一行值（每组就一个deptno，一个max，一个count）

| Having                                                       | where                       |
| ------------------------------------------------------------ | --------------------------- |
| having后面可以使用分组聚合函数                               | where后面不能写分组聚合函数 |
| having只用于group by分组统计语句                             |                             |
| 用having过滤group by和where过滤子查询效率一样，只是前者更简洁。 |                             |

```mysql
-- 用having过滤group by的结果
select 
    t.empno, 
    avg(t.sal) avg_sal,
    count(*)
from emp t 
group by t.empno
having avg_sal > 2000;

-- 用where过滤子查询的结果
select 
 t1.empno,t1.avg_sal
from (
     select
    t.empno,
    avg(t.sal) avg_sal,
    count(*)
from emp t
group by t.empno) t1
where t1.avg_sal > 2000;
```

## join union

行很多是高表，列很多是宽表。**join是按行拼接为宽表，union是上下拼接为高表**

Hive支持通常的sql join语句，但是只支持**等值连接**（a.deptno = b.deptno），2.x版本不支持**非等值连接**（a.deptno > b.deptno）。但是不等值连接基本不用。

| 连接                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| join                 | 内连接，两边都有的才返回，行数是两边都关联的行               |
| left join right join | 左外连接，行数是左表全部行，右边有就添加上列，没有就后面的列为null |
| full join            | 满外连接，行数：左表+右表的所有行，左右没有的列都为null      |
| 多表连接             | 不需要放个子查询，直接join on   join on实现多表连接          |
| 笛卡尔积             | 最终得到m*n行，在以下三种情况生效：不写连接条件；连接条件无效；所有表中的所有行互相连接<br />不到万不得已不要用，会数据膨胀。 |
| **union**            | 要求上下两个表的**字段个数**和**字段类型**完全相同，**不要求字段名**相同，返回第一个select的字段名<br />连接的是select查询语句，必须是多个sql的union，不能union表<br />union会对**上下select结果中完全一样**的数据去重<br />可以多次union |
| **union all**        | union all不去重                                              |

```mysql
-- 满外连接
select 
    e.empno, 
    e.ename, 
    d.deptno 
from emp e 
full join dept d 
on e.deptno = d.deptno;

-- 多表关联，实现3张表的关联
select 
    e.ename, 
    d.dname, 
    l.loc_name
from emp e 
join dept d
on d.deptno = e.deptno 
join location l
on d.loc = l.loc;

-- 笛卡尔积
select 
    empno, 
    dname 
from emp join dept on true;

-- union 连接select语句
select * from emp where deptno=30
union
select * from emp where deptno=40
union
select * from emp where deptno=50;
```

## 4个by

| 排序语法                    | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| order by asc/desc           | **全局排序**，只有一个reduce，因此大规模的数据集order by的效率非常低，会数据倾斜<br />因此一般需要**limit**：加了limit也只会有一个reduce，但是map端优化，只返回100行<br />select * from emp orfer by sal limit 100; |
| 下面三个by很少用，mysql没有 | 每个Reduce内部排序                                           |
| sort by                     | 多reduce，指定MR的**排序**字段，保证map到reduce即**每个reduce的字段是有序**的，但是多个reduce之间不保证有序 |
| distribute by               | 多reduce，指定MR的**分区**字段进行hash分区<br />distribute by经常和sort by配合使用 |
| cluster by                  | 多reduce，如果分区字段和排序字段相同，可以合并sort by与distribute by。<br />排序只能降序 |
|                             |                                                              |
| distribute by VS cluster by | 可联合Transform语句，使用map和reduce 脚本去实现MR程序，简化MR。但很少用 |
|                             |                                                              |

```mysql
select *
from emp 
distribute by deptno 
sort by sal desc; -- deptno相同的放到一个分区文件中，每个文件根据sal排序

select *
from emp 
cluster  by sal; -- 将sal相同的放到一个分区文件，每个分区按照sal排序
```

# 函数

总共有290个函数。

```mysql
show functions like "date*"; -- 查看date相关函数
desc function substring; -- 查看具体函数的用法
desc function extended substring ; -- 打印更多信息，使用示例，源码路径 
```

## 单行函数

一行进，一行出

### 日期函数

| 函数                                  | 描述                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| unix_timestamp()                      | 把时间字符串转为零时区的unix时间戳，<br />1970.1.1 0:0:0开始的零时区的时间，全世界都一样，没有时区区分 |
| from_unixtime()                       | 时间戳转为零时区的时间字符串                                 |
| from_utc_timestamp                    | 时间戳转为当前时区的时间字符串                               |
| current_timestamp                     | 考虑到所在地时区的日期+时间                                  |
| year month day hour minute second     | 获取日期字符串的信息                                         |
| datediff(date1, date2)                | 两个日期字符串的天数差，day1-day2                            |
| date_add date_sub                     | 日期加天数/减天数，其实date_add中用负数也可以表示减去天数    |
| date_format                           | 格式记住**yyyy-MM-dd HH:mm:ss**大小写不要变                  |
| ==求年龄month_between(date1, date2)== | 得到两个date月份差，就可以得到年龄                           |
| ==**sumif的使用，见练习题**==         | sum是计算某列不为null的值之和<br />sum(if(score < 60,1,0))   |
| ==countif的使用==                     | count是计算某列不为null的行数                                |

[聚合函数练习题](#聚合函数练习题)

```mysql
-- 1.指定格式，将时间字符串转为零时区的时间戳
select unix_timestamp('2022-08-08 08:08:08','yyyy-MM-dd HH:mm:ss'); -- 从1970年开始经历了多少s,10位，以s为单位，如果是13位，单位是ms
-- 2.时间戳转为零时区的字符串
select from_unixtime(1667801234, 'yyyy-MM-dd HH:mm:ss'); -- 将时间戳默认转为零时区,2022-11-07 06:07:14
-- 3.时间戳转为东八区中国所在的区的字符串
select from_utc_timestamp(cast(1667801234 as bigint) * 1000,'GMT+8'); -- 东八区，前面默认是ms，所以需要*1000。 2022-11-07 14:07:14
-- 4.考虑到所在地时区的日期+时间
select `current_timestamp`(); -- 考虑到时区，2023-04-27 10:55:27.566000000
-- 5.
select year('2022-09-08 12:31:21') as year,  month('2022-09-08 12:31:21') as month, hour('2022-09-08 12:31:21') as hour;
-- 6.天数差
select datediff('2020-06-16', '2020-06-18'); -- 第一个 sub 第二个
-- 7.date_add 负数
select date_add('2020-1-1',-1); -- 2019-12-31
-- 8.date_format
select date_format('2022-08-08','yyyy年-MM月-dd日');

```

mysql和hivesql的日期函数用法很是不同

|                                | MySQL                                                        | HiveSQL                                                      |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 获取当前日期，当前格式化时间   | 都是current_date()，current_timestamp()                      |                                                              |
| 格式化                         | date_format(login_ts,'**%Y-%m-%d**')<br />select date_format('2022-08-08','%Y年-%m月-%d日'); | date_format(login_ts,'**yyyy-MM-dd**')<br />select date_format('2022-08-08','yyyy年-MM月-dd日'); |
| 时间戳转为字符串               | 都是from_unixtime                                            |                                                              |
| 字符串转为时间戳               | 没有，只能获取当前的unix时间戳unix_timestamp()               | unix_timestamp(一串数字,format)                              |
| 从格式化时间中得到年月日时分秒 | 都是year month day hour这些可以得到数字，如果想要得到April这种，还是date_format |                                                              |
| 日期加一天date_add             | date_add('2022-08-08',interval  1 day) -- 可以是day year month，value也是可正可负 | date_add('2022-08-08',1)                                     |
| 日期减12天date_sub             | SELECT date_sub('2022-08-08',interval  12 day)               | date_sub('2022-08-08',12)                                    |
| 日期相减                       | timestampdiff(day,begin,end)<br />-- 可以是month，year月份相减<br />也可以用datediff(end,begin)前减去后 | 也有timestampdiff<br />datediff(end,begin)                   |

### 字符串函数

| 函数                                                 | 描述                                                         | 实例                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| length                                               | 字节数                                                       | select length("abc");                                        |
| concat_ws(x, s1,s2...sn)                             | 第一个位置加上个分隔符，合并字符串<br />可以接收**任意个**参数，给多少都行 | SELECT CONCAT_WS("-", "SQL", "Tutorial","is", "fun!")as res; |
| ltrim(s),trim(s),rtrim(s)                            | 去掉字符串 s 左边、前后、右边的空格                          | SELECT trim(' s dds   ');                                    |
| substring(str, pos[, len])                           | pos表示位置，**可正可负**；**len可选**，表示长度，不选表示到末尾 | substring("Facebook", -5); --ebook<br />SELECT substring("Facebook", 5); --book<br />SELECT substring("Facebook", 5,1); --b |
| replace(string A, string B, string C)                | 将字符串A中的子字符串B替换为C，**全局替换**                  | replace('atguigu', 'gu', '12') -- at12i12                    |
| ==**regexp_replace(string A, string B, string C)**== | 正则替换，相当重要                                           | select regexp_replace("abcd-123-23qwe","[0-9]{1,}","*"); -- abcd- * - *qwe |
| regexp                                               | 正则匹配，返回true/false                                     | select "string" regexp ".* str.*"; --任意字符，重复任意次    |
| repeat(string A,int time)                            | 将A重复time次                                                | select repeat("str",3); --strstrstr                          |
| split(string A, string split)                        | 类似java，保存是一个数组，split是正则表达式，不是字符串，因此直接用.代表的任意字符，需要转义 | select split("192.168.10.2","\ \ ."); -- java中反斜杠转义，想用.切割 |
| ==nvl(a,b)==                                         | **赋默认值**，a不为空返回a，为空返回b                        | select nvl(null,0); -- 0                                     |
| ==get_json_object==                                  | 解析json字符串，用 $ 指代该json字符串，用数组下标.属性       |                                                              |

json结构：对象，数组，单值

```
select get_json_object('[{"name":"大海海","sex":"男","age":"25"},{"name":"小宋宋","sex":"男","age":"47"}]',
    '$[1].age'); -- 47
```

### 流程控制函数

case when 和if

```mysql
-- 1.一般case语句，先有case end,里面写when then when then else
select stu_id,
		case 
            when score >=90 then 'A'
            when score >=80 then 'B'
            when score >=60 then 'C'
            else '不及格'
		end
	as level form stu_info;
-- 2.等值判断简写
select stu_id,
		case payType
            when 1 then '微信支付'
            when 2 then '支付宝支付'
            when 30 then '银行卡支付'
            else '其他'
		end
	as method form table1;
```

### 集合函数

针对复杂类型array map struct的函数

| 复杂类型       | 函数                                 |
| -------------- | ------------------------------------ |
| array size     | 创建数组，求数组长度                 |
| array_contains | 判断是否有某个值存在，返回true false |
| sort_array     | 只能正序                             |
| map_keys       | 得到key的数组，不会有重复的          |
| map_values     | 得到value数组，可能有重复            |
| struct         | 创建的struct是默认的列名             |
| named_struct   | 可以指定列名                         |



```mysql
-- array
select size(`array`(10,2,3,4));
select array_contains(`array`(1,2,3,4,5),5);
select sort_array(`array`(10,2,3,4));

-- map
select `map`('name',18,'age',18); -- {"name":18,"age":18}
select map_keys(`map`('name',18,'age',18)); --["name","age"]
select map_values(`map`('name','bobo','age',18)); -- ["18","18"]

-- struct
select struct('val1','val2','val3'); -- {"col1":"val1","col2":"val2","col3":"val3"}
select named_struct('name',18,'age',18); -- {"name":18,"age":18}
```

## 全部聚合函数

多行进，一行出

有普通聚合（count sum avg max min）[](#聚合函数)

高级聚合就两种：collect_list将一列聚合为一个array，不去重；collect_set去重

```mysql
select
    collect_list(job), -- ["销售","行政","研发","研发","销售","行政","前台","前台"]
    collect_set(job) -- ["销售","行政","研发","前台"]
from employee;
```

## 炸裂函数（mysql没有）

UDTF：Table generating functions，制表函数，**一行进，多行出**
UDTF是一类函数，最常用的是explode函数。

LateralView侧视图通常与UDTF配合使用，LV可以将UDTF应用到源表的每行数据，将每行数据转换为一行或多行，并将原表中每行的输出结果与该行连接起来，形成一个虚拟表。

| UDTF函数                    | 描述                                  |
| --------------------------- | ------------------------------------- |
| explode(array或者map)       | 一行变一列，map变两列                 |
| pos_explode(array)不能是map | 返回索引行号和一列数据                |
| inine(结构体数组)           |                                       |
| lateral view                | 后面，explode的字段必须是表中有的字段 |

```mysql
select explode(`array`(1,2,3)) as item; -- 返回一列，字段名item
select explode(map(1,'a',2,'b',3,'c')); -- 两列，默认key value
select posexplode(`array`(1,2,3)) as (pp,vv); -- 返回两列，pp表示行号，0，1，2；vv表示item，1，2，3
```

![image-20230427143605364](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230427143605364.png)

```mysql
select
cate,count(*)
from (
     select
        movie,split(category,',') category -- 必须套一个select，因为lateral view后面explode的字段必须是表中有的字段
     from movie_info
    )t1 lateral view explode(category) tmp as cate -- t1和tmp这两个表别名必须起
group by cate;
```



## 窗口函数

n行进，n行出

窗口函数是在一个窗口内，计算聚合，排名，上下拉行，首尾值，**连接在该行后面**

| 开窗函数      | 包含                                                    | 备注                                                         |
| ------------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| 聚合函数5     | max min sum avg count 1个参数                           |                                                              |
| 跨行取值函数4 | lead lag  2-3个参数<br />first_value last_value 2个参数 | **lag和lead函数不支持自定义窗口**<br />只有基于行的，没有基于值的 |
| 排名函数3     | row_number rank dense_rank 无参数                       | **不支持自定义窗口**                                         |

### 开窗聚合函数

1.基于行的语法：
请注意：如果只有rows 窗口范围，在真正计算时是不明确的。因为表可能会切片，可能会shuffle，我们看到的数据不是MR看到的数据，而真正进行开窗聚合的是MR的数据。
因此**窗口范围的定义必须有order by字段**，这样就明确了。

每个分区独立划分，独立排序，

```mysql
select *,
avg/sum/max/min/count计数(salary) over (
partition by dname -- 分区，划分表的时候不是整张表划分，而是根据分区，每个分区独立划分
order by eid asc/desc -- 如果不排序，则没有累加效果，表示从之前所有行的累加和/均值/最大值/最小值
rows between ... and ... -- 起点一定要小于终点的，起点可以随便写，终点不行
range between ... and ... -- 基于值的语法，一定注意，这里的current row的范围不一定只表示这一行
)
as rn1 from employee;
-- between and可以填写
between unbounded preceding and current row -- 有order by的默认，从之前的到当前行
between unbounded preceding and unbounded following -- 没有order by的默认
between 3 preceding and 1 following -- 表示对当前行前的三行，后的一行（最多情况下共5行）
between current row and unbounded following -- 从当前行到最后一行
between 1 following and unbounded following -- 运行窗口内不包含当前行
```

2.基于值的语法：比如说1，1，2，2，3，4这一列，找值处于[currentValue-1,currentValue]的行有哪些，对于第二行，是找到所有值处于[1,2]的也就是[1,4]行。
把上面的rows换成range
order by对于基于值的窗口函数，仅代表根据哪个值去取range。
between 3 preceding and 1 following 代表当前值 到 3到当前值+1，如果使用这种定义窗口，需要order by这个字段必须是int，不能是string和小数。
如果窗口定义用的是unbounded preceding current row这种，不涉及到加减操作，order by的字段不一定需要是int类型。

3.省略：
partition by不写，表示不分区；
order by不写，表示不排序（很少很少）。如果是基于值，说明不知道根据哪个字段划分窗口了，后面的range都失效。
rows between and不写：有order by，则默认为**range between unbounded proceding and current row**；没有order by默认该分区的全部数据range between unbounded proceeding and unbounded following

### 跨行取值函数

1.lead和lag:arrow_lower_right:，有2-3个参数，字段名，偏移量，默认值。
这里只能是基于行的，不能是基于值的。
不支持自定义窗口，这个函数有自己的窗口逻辑

```mysql
select *,
lag(hiredate,1,'2000-01-01') over (PARTITION by dname order by hiredate) as time1,
-- 对于每一组的hiredate列，将上1行的值放过来，如果没有上1行，则有默认值
lag(hiredate,2) over (PARTITION by dname order by hiredate) as time2
-- 对于每一组，将上2行的值放过来，如果没有上2行（第1行和第2行都没有上2行），则null
from employee;
```

2.first_value和last_value，2个参数，对哪一列取first_value，是否跳过null值（默认false，不跳过）
允许自定义rows 窗口，有order by默认是**range** between unbounded proceding and current row，==**基于值**==

```mysql
select *,
first_value(salary,true) over(PARTITION by dname order by hiredate) as rn1,
-- 本组内，通过hiredate排序的到目前为止的第一个salary值
-- 如果是false，第一行的salary是null则返回null。如果true，第一行null就去找第二行
last_value(salary) over(PARTITION by dname order by salary) as rn2
-- 本组内，通过salary排序的最后一个salary值，也就是本行值
from employee;
```

### 排名函数

```mysql
select 
dname,
ename,
salary,
row_number() over(partition by dname order by salary desc) as rn1, -- 最普通排名 1 2 3
rank() over(partition by dname order by salary desc) as rn2, -- 并列稀疏排名， 1 1 3
dense_rank() over(partition by dname order by salary desc) as rn3 -- 密集排名，1，1，2
from employee;
```

## 自定义函数

自定义函数用的不多，用的话也是单行函数。
用IDEA maven项目区创建，继承抽象类GenericUDF，实现抽象方法。
initialize：初始化，入参类型长度校验，约定返回的数据类型；ObjectInspector保存数据元信息，接收上一个Operator的源信息
evaluate：核心处理逻辑，每处理一行数据，就调用一次evaluate，hive会自己循环调用，所以不用自己写for循环。
getDisplayString：获取在explain中展示的字符串，一般不管，返回""

## 练习题

别名需要起，但可以不用，多表join最好都带上t1.id这一种

in一个子查询，这个子查询**不要起别名**

having的字段和select 的字段要一样范围，是group by后面的字段，或聚合函数的字段，字段别名

case when then else不能有逗号啊

别名最好不要是函数名字，lag lead这种，自己想别的

join的时候左表最好小，用join或者left join

where后面的字段**不能是**select中起的别名，having order by**可以**是select中的别名

group by之后的结果想要join，最好嵌套一个子查询

窗口函数默认：有order by是到current row，没有order by是unbounded following

### 聚合函数练习题

```mysql
-- 2）查询每个人的年龄（年 + 月）
-- 先得到月份差，分离出年、月，需要转为整数，拼接为指定格式
-- 多个select嵌套，反而比在一个里面疯狂处理要快。-- replace比round(x,y)要快
select
st.name,concat(st.year,'年', replace(st.month, '.0','月'))
from (
     select
        t.name,`floor`(t.newMonth / 12) as year, round(t.newMonth % 12) as month
     from (
         select
         name,
         months_between(`current_date`(),replace(birthday,'/','-')) as newMonth
         from employee
      ) t
) st;

-- 3）按照薪资，奖金的和进行倒序排序，如果奖金为null，置位0
-- nvl函数的运用，order by
select
   name, 
   salary + nvl(bonus,0) as sal
from employee
order by sal desc;

-- 4）查询每个人有多少个朋友，孩子的姓名
select
    name,
    size(friends) as cnt,
    map_keys(children) as ch_name
from employee;

-- 6）查询每个岗位男女各多少人
-- 思路：可以先筛选男性groupbyjob然后筛选女性，groupby job，然后full join，记得nvl的使用
-- sumif的使用，重要，也可以用countif，但要注意
    select
    job,
    sum(if(sex = '男',1,0)) as male,
    sum(if(sex='女',1,0)) as female,
    count(if(sex = '男','abc',null)) as male, -- 为男的时候，返回什么都行，反正是统计行数，不为男返回是null，不统计这一行
from employee
group by job;

-- 7）每个月的入职人数以及姓名
select
    tt.newDate month,
    count(*) cn,
    collect_list(tt.name) name_list
from (
    select
        name,
        substring(replace(hiredate,'/','-'), 1,7) as newDate
    from employee
	) as tt
group by tt.newDate;

-- 8）显示学生的语文数学英语成绩，没有成绩的输出0，按照有效平均成绩降序显示
select
	stu_id,
	sum(if(course_id = '01',1,0)) as 'yw',
	sum(if(course_id = '02',1,0)) as 'sx',
	sum(if(course_id = '03',1,0)) as 'yy',
	count(*),
	avg(score)
from score_info
group by stu_id; -- group by前面select出来的可以是聚合函数，所有的聚合函数都ok

-- 9）有两门以上课程 <60的学生学号和姓名，平均成绩
select
	t1.stu_id,
	t2.stu_name,
from (
	select
		stu_id,
		sum(if (score < 60,1,0)) as cnt,
    	avg(score)
	from score_info
	group by stu_id
	having cnt >=2
) t1 join student_info t2 on t1.stu_id = t2.stu_id;

-- 10）查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号
-- 类似，都用in过滤，分组聚合count计数
SELECT
stu_id,count(*) as cnt -- 计数，看有几个
from score_info
where course_id in ('01','02') -- 过滤，只留下01 02课程信息
group by stu_id having cnt = 2 -- 这里直接写就行

-- 11）学过“李体音”老师所教的所有课的学生的学号
SELECT
stu_id, count(*) as cnt -- 求出每个学生选的李体音的课的个数
from score_info 
where course_id in -- 筛选过滤
(
    SELECT -- 李体音教的所有课程
    course_id
    from course_info 
    WHERE tea_id in (SELECT tea_id from teacher_info where tea_name = '李体音')
)
group by stu_id having cnt = (SELECT count(*)from course_info -- 李体音教了多少门课需要计算
WHERE tea_id in (SELECT tea_id from teacher_info where tea_name = '李体音'))

-- 12）学过“李体音”老师所教的任意一门课的学生的学号
SELECT
stu_id, count(*) as cnt -- 求出每个学生选的李体音的课的个数
from score_info 
where course_id in
(
    SELECT -- 李体音教的所有课程
    course_id
    from course_info 
    WHERE tea_id in (SELECT tea_id from teacher_info where tea_name = '李体音')
)
group by stu_id having cnt >=1 -- 区别只在于这里，是>=1

-- 13）查询没学过"李体音"老师讲授的任一门课程的学生姓名，不能用上面的方法了，需要让查出来的not in
SELECT
stu_id, stu_name
from
student_info
where stu_id not in
(
	SELECT stu_id from score_info
	where course_id in
	(
		SELECT -- 李体音教的所有课程
		course_id
		from course_info 
		WHERE tea_id in (SELECT tea_id from teacher_info where tea_name = '李体音')
	)
	group by stu_id
);
```

### 窗口函数练习题

1）不能默认不写，不写是range between ，我们要的是rows between，有重复值（一天下两单）就会有问题
null值与别的值做任何运算得到的也是null
2）先做分组聚合，再对分组聚合的结果做开窗

```mysql
-- 1）统计每个用户截至每次下单的当月累积下单总额
-- 截止每次下单，说明是用rows between；每个用户当月，partition by 两个东西；为了防止不同年同月，substring更合适
select
order_id,user_id,user_name,order_date,order_amount,
sum (order_amount) over (
    partition by user_id,substring(order_date,1,7)
    order by order_date
    rows between unbounded preceding and current row
    ) as sum_so_far
from order_info;

-- 2）统计每个用户每次下单距离上次下单相隔的天数（首次下单按0天算），子查询嵌套，先lag，
select
t.order_id,t.user_id, t.user_name, t.order_date, t.order_amount,
    datediff(order_date, pref) as diff
from (
     select
    order_id,user_id,user_name,order_date,order_amount,
    lag(order_date,1,order_date) over ( -- 默认值是自身，拿不到就是自己
    partition by user_id
    order by order_date
    ) as pref
    from order_info
    ) t;
    
-- 3）查询所有下单记录以及每个用户的每个下单记录所在月份的首/末次下单日期
select
order_id,user_id,user_name,order_date,order_amount,
first_value(order_date,false) over (
    partition by user_id,substring(order_date,1,7)
    order by order_date
    rows between unbounded preceding and unbounded following -- 这个可以不加，默认是负无穷到当前值
    ) as first_date,
last_value(order_date,false) over (
    partition by user_id,substring(order_date,1,7)
    order by order_date
    rows between current row and unbounded following -- 必须加窗口，起点无所谓，终点必须正无穷
    ) as last_date
from order_info;
```



中等题目

```mysql
-- 1） 查询累积销量排名第二的商品
select
sku_id
from (
    select -- 这个select是主要代码
    sku_id
    from (
        select
        sku_id,
        dense_rank() over (
            partition by sku_id
            order by sum desc
        ) as rank
        from (
            select
            sku_id,
            sum(sku_num) as sum
            from order_detail_info
            group by sku_id
        )
    ) t where t.rank = 2
) t1 -- 这一层以及下面为了保证没有第二名的时候返回null
right join (
    select  1
    ) t2
on 1=1;
```

### 去重方案

```mysql
-- 1.distinct去重，user_id与create_date同时相等的进行去重
select
distinct user_id, create_date
from order_info;

-- 2.group by去重
select
user_id, create_date
from order_info
group by user_id, create_date;

-- 3.row_number去重，不同的row_number过滤掉
select
user_id,create_date
from (
    select
    user_id,create_date,
       row_number() over (partition by user_id, create_date) as rn
    from order_info
    ) where rn = 1;
```

### 连续三天

```mysql
-- 思路1：查询至少连续三天下单的用户：一天下多单，先去重；diff = date - rank()，diff相等的就是连续的。已经去过重了，同一个user没有相同的下单日期了，所以用哪个序号函数都一样。

select
distinct t.user_id -- 这个地方也要去重，因为一个user可能有多组连续3天下单，就会出现多个user1
from
(
    select
        user_id,create_date,
        date_sub(create_date, row_number() over (partition by user_id order by create_date)) as diff,
    DATE_SUB(create_date,INTERVAL row_number() over (PARTITION by user_id ORDER BY create_date asc) DAY)as diff -- 这个是mysql求日期相减的
    -- 这个地方求create_date和row_number的差值，省了一层子查询，底层执行逻辑不变，直接把row_number作为一个参数
    from
    (
        select
        distinct user_id, create_date
        from order_info
    )t1
)t
group by t.user_id, t.diff
having count(*) >=3;

-- 思路2：先去重，只需要比较当前日期与下两行是否相差2
select
distinct user_id
from
(
    select
    user_id, create_date,
    datediff(create_date, lag(create_date, 2, create_date) over (partition by user_id order by create_date)) as sub
    from
    (
            select
            distinct user_id, create_date
            from order_info
    )
) t where t.sub = 2;

-- 思路3：先去重，用count开窗，range between 1day proceeding and 1day following，但是这里面必须是数字，不能是字符串，因此转时间戳
select
distinct user_id --step4: where过滤，distinct user_id
from
(
    select -- step3：时间戳确定前一天后一天
    user_id, ts,
    count(*) over (partition by user_id order by ts range between 86400 preceding and 86400 following) as cnt
    from
    (
        select -- step2:转时间戳
        user_id,
        unix_timestamp(create_date,"yyyy-MM-dd") as ts
        from
        (
           select -- step1：去重
           distinct user_id, create_date
           from order_info
        )
    )
) t where t.cnt = 2;
```

### 查询各品类销售商品的种类数及销量最高的商品

分组topn问题，rank() over (partition by order by ) as rk select子查询rk <=n; 

```mysql
select -- where即可
category_id,category_name,sku_id,`name`,sku_sum as order_num, order_cnt
from
(
	SELECT  -- step3:销量topn用rank加where过滤，统计每品类的商品数，count(*)
	category_id,category_name,sku_id,`name`,sku_sum,
	ROW_NUMBER() over(partition by category_id ORDER BY sku_sum desc) as rn,
	COUNT(*) over(partition by category_id) as order_cnt
	from
	(
		SELECT  -- step2 三表join，没啥好说的
		t0.sku_id, sku_sum ,sku_info.`name`,sku_info.category_id,category_info.category_name
		from
		(
			SELECT -- step1:先sum再join数据量会小一些
			sku_id,sum(sku_num) as sku_sum
			from order_detail
			GROUP BY sku_id
		)t0
		left join sku_info on t0.sku_id = sku_info.sku_id
		left join category_info on sku_info.category_id = category_info.category_id
	)t1
)t2
where t2.rn <=1;
```

### 查询VIP等级case when

```mysql
select
user_id,create_date,sum_so_far,
case
	when  sum_so_far < 10000 then '普通会员' -- 不能有逗号
	when  sum_so_far < 30000 then '青铜会员'
	when  sum_so_far < 50000 then '白银会员'
	when  sum_so_far < 80000 then '黄金会员'
	when  sum_so_far < 100000 then '白金会员'
	else '钻石会员'
end as vip_level
from
(
	select
	user_id,create_date,
	sum(total_amount) over(PARTITION by user_id ORDER BY create_date asc
	rows between unbounded preceding and current row
	) as sum_so_far
	from
	order_info
)t1
```

### 查询首次下单后第二天连续下单的用户比率

```mysql
select-- 一个结果的计算和格式化
concat(substring(res * 100,1,4),'%') as percentage
from
(
	select -- 一个结果的计算和格式化
	count(*) / cnt as res
	from
	(
		SELECT -- 计算总用户数量
		user_id,diff,
		count(*) over() as cnt
		from
		(
			SELECT -- 得到前两次下单的时间差，diff=1需要关注
			user_id,
			TIMESTAMPDIFF(day,min(create_date),max(create_date)) as diff
			from
			(
				select -- topn问题，得到不同用户不同天数的前两次下单
				user_id,create_date
				from
				(
					SELECT
					user_id,create_date,
					ROW_NUMBER() over (partition by user_id ORDER BY create_date) as rn
					from
					(
						SELECT -- step1去重，得到不同天数的前两次下单
						DISTINCT user_id,create_date
						from
						order_info
					)t1
				)t2
				where rn <=2
			)t3
			GROUP BY user_id
		)t4
	) t5
	where diff = 1
)t6

```

### 向用户推荐收藏的商品

```mysql
--  向用户推荐朋友收藏的商品，向所有用户推荐其朋友收藏但是用户自己未收藏的商品
-- 得到用户的所有朋友union；得到朋友收藏的所有商品left join；得到自己收藏的所有商品collect_set
select
    t2.user1_id,
       t2.sku_id
from
(
    select
    distinct t1.user1_id,favor_info.sku_id
    from
    (
        select
        user1_id,user2_id
        from friendship_info
        union
        select
        user2_id,user1_id
        from friendship_info -- 1.union，每个用户的所有朋友，user1_id的所有朋友都在user2_id
    ) t1
    left join favor_info on t1.user2_id = favor_info.user_id -- 2.得到每个用户的所有朋友推荐的商品
) t2 left join
(
    select
    user_id,
    collect_set(sku_id) as set
    from favor_info -- 3.每个用户自己收藏的商品列表
    group by user_id
) t3 on t2.user1_id = t3.user_id
where !array_contains(t3.set,t2.sku_id); -- 4.1 朋友收藏 left join自己收藏，!array_contains()
-- 4.2 step2和step3的表直接join，左表中（朋友收藏）过滤掉右表中有的（自己收藏） where t4.user_id is null

```

### 同时在线最多的人数

先找到所有人的登入时间，所有人的登出时间，两表union all不去重；

```mysql
select
    max(total) -- step3:取最大值
from
(
    select
        user_id,event_ts,
        sum(acc) over (order by event_ts rows between unbounded preceding and current row ) as total
    -- step2:按照时间顺序，从负无穷到当前行累加，不分区
    from
    (
        select
        user_id,login_ts as event_ts,1 as acc -- 表示有人登入,acc= 1
        from
        user_login_detail
        union all
        select
        user_id,logout_ts,-1 -- 表示有人登出，acc= -1
        from
        user_login_detail  -- step1:unoin
    )t1
)t2

```

### 会话划分问题

规定若同一用户的相邻两次访问记录时间间隔小于60s，则认为两次浏览记录属于同一会话。现有如下需求，为属于同一会话的访问记录增加一个相同的会话id字段。

```mysql
-- 会话记录，首先需要找到会话起点，会话起点为1，其余为0，sum求和
select
user_id,page_id,view_timestamp, -- 3.sum得到的就是1，1，1，2，2，2这种
concat(user_id,'-',sum(newid) over (partition by user_id order by view_timestamp rows between unbounded preceding and current row )) as session_id
from
(
    select
    user_id,page_id,view_timestamp,
    if (view_timestamp - last_view > 60,1,0) newid -- 2.找会话起点
    from
    (
        select
        user_id,page_id,view_timestamp,
        lag(view_timestamp,1,0) over (partition by user_id order by view_timestamp) as last_view -- 1.每一行lag上一行
        from page_view_events
    )t1
);
```

### 真正连续

统计各用户最长的连续登录天数，真正连续

```mysql
select
user_id,max(cnt) -- 最后求个最大值，记得group by
from
(
    select
    user_id,
    diff,
    count(*) as cnt -- 对diff分组求行数，这个就是连续登录天数
    from
    (
        select
            user_id,create_date,
            date_sub(create_date, row_number() over (partition by user_id order by create_date)) as diff
        -- 这个地方求create_date和row_number的差值，省了一层子查询，底层执行逻辑不变，直接把row_number作为一个参数
        from
        (
            select
            distinct user_id, create_date
            from order_info
        )t0
    )t
    group by user_id, diff
)t1
group by user_id;
```

### 间断连续

统计各用户最长的连续登录天数，间断一天也算连续。参考会话的思路。
把上一次登录的日期lag下来，如果间隔1天以内是0，否则是1。（找起点）
对这个进行sum（负无穷到当前行）连续登录在一组。（起点到终点这个sum值相同）
分组max-min+1

**==不能直接对分组聚合的结果做运算，但是如果开窗的结果是支持的，开窗可以省去一重select==**

```mysql
select
user_id, max(diff) -- 7.终于可以取最大值了，这个只需要groupby user_id
from
(
				select -- 5.找到每个用户每组连续登录的日期最大值、最小值 group by user_id, newPar
        		user_id,newPar,
				timestampdiff(day,min(date), max(date)) + 1 as diff -- mysql写法，这个是对的
    			datediff(max(date),min(date)) + 1 as diff
        from
        (
            select -- 4. 连续登录的newPar字段相同
            user_id,date,start_point,
            sum(start_point) over (partition by user_id order by date rows between unbounded preceding and current row ) as newPar
            from
            (
                select -- 3.找起点
                user_id,date,last_day,
								if (TIMESTAMPDIFF(day,last_day,date) > 2,1,0) as start_point
                from
                (
                    select -- 2.拉取上一行的date
                    user_id,date,
                    lag(date,1,"1970-01-01") over (partition by user_id order by date) as last_day
                    from
                    (
                        select -- 1.去重，date格式转换
                            distinct
                            user_id,date_format(login_datetime,"%Y-%m-%d") as date
                        from
                        login_events
                    )t1
                )t2
            )t3
        )t4 group by user_id, newPar
)t6 group by user_id;
```

### 日期交叉问题

现有各品牌优惠周期表（promotion_info），记录了每个品牌的每个优惠活动的周期，同一品牌的不同优惠活动的周期可能会有交叉。要求统计每个品牌的优惠总天数，去掉交叉日期。

思路：改变start_date，到没有交叉的状态。



```mysql
select
brand, -- 记得datediff
sum(TIMESTAMPDIFF(day,new_start_date,end_date) + 1) as promotion_day_count
from
(
    select -- 考虑三种情况，取到新的start_date
    brand,start_date,end_date,
    case
        when max_prior_end_date > end_date then DATE_ADD(end_date,INTERVAL 1 day)
        when max_prior_end_date > start_date then DATE_ADD(max_prior_end_date,INTERVAL 1 day)
        else start_date
    end as new_start_date
    from
    (
           select
                brand,
                start_date,
                end_date,
                max(end_date) over -- 拿到start_date升序的，之前的所有行最大的结束日期
                (partition by brand order by start_date rows between unbounded preceding and 1 preceding) max_prior_end_date
            from promotion_info
    )t1
)t2 group by brand
```

### 本周过生日 下周过生日

```mysql
-- 本周过生日，直接用week函数
SELECT
student.* , week(Sage), week(now())
FROM student WHERE week(Sage) = week(now())

-- 下周过生日，直接用week函数
SELECT
student.* , week(Sage), week(now())
FROM student WHERE week(Sage) = week(now()) + 1
```



# 存储角度优化

## 分区表和分桶表

### 分区表

非常重要，99%的表都是分区表。这个分区和MR的分区没有任何关系。把一张大表的数据按照业务需要分散的存储到多个目录，每个目录就称为该表的一个分区。
在查询时通过where子句的分区字段，可以避免全局扫描，查询效率高。
可以将分区字段看作表的**伪列**，可以进行**查询过滤**。

```mysql
-- 1.建表语句
create table if not exists student (
    deptno int,
    dname string,
    loc string
)
partitioned by (day string), -- 这个day就是分区字段，所以这个表一共有4个字段
row format delimited fields terminated by '\t';
-- 2.load写入，多了一个partition，这个day会作为表的一个字段，可以查询过滤
load data local inpath '/opt/module/hive/datas/dept_20220401.log' into table dept_partition 
partition(day='20220401');
-- 3.insert写入，表名后面一定要跟着一个partition (day = "   ")
insert overwrite table dept_partition partition (day = '20220402')
select deptno, dname, loc
from dept_partition
where day = '2020-04-01';
-- 4.查看表的所有分区
show partitions dept_partition;
-- 5.增加分区
alter table dept_partition add partition(day='20220403'); -- 但是这个分区是没有数据的
-- 6.删除分区，与内部表（全删）外部表（不删hdfs）一样特性
alter table dept_partition drop partition (day='20220403');

```

1.分区表底层存储：
路径名：<分区字段=分区值> day=20220401

2.修复分区
只有元数据和HDFS分区路径一致时，才能正常读取数据。比如手动在hdfs上创建删除路径，hive都是感知不到的（元数据不增加）。可以进行修复（add partition; drop partition; 让hive元数据信息向HDFS路径看齐）
msck（make store check，通用的方式）

```mysql
msck repair table table_name [add/drop/sync partitions];
-- add partitions：该命令会增加HDFS路径存在但元数据缺失的分区信息。类似add partition命令，但不用手动指定
-- drop partitions：该命令会删除HDFS路径已经删除但元数据仍然存在的分区信息。类似drop partition
-- sync partitions：该命令会同步HDFS路径和元数据分区信息，相当于同时执行上述的两个命令。
```

3.绝大部分数据一般都是按照**日期**分区，一天一批数据。

4.二级分区：如果觉得一天一批太慢，想要按小时分区，可以定义二级分区，一天1目录，其下1小时1目录。也是可以当作普通字段去查询。

```mysql
-- 1.建表语句
create table if not exists student (
    deptno int,
    dname string,
    loc string
)
partition by (day string, hour string), -- 这个day是一级分区，hour是二级分区
row format delimited fields terminated by '\t';

-- 2.插入
insert overwrite table dept_partition partition (day = '20220402',hour = '12');
```

5.动态分区
动态分区是指向分区表**insert数据**时，被写往的分区不由用户指定，而是**由每行数据的最后一个字段的值来动态的决定**。使用动态分区，可**只用一个insert语句将数据写入多个分区**

严格模式：多级分区下，至少指定一个分区，其他分区是动态的。
非严格模式：不需要指定任何分区。**更多用非严格模式，但默认是严格模式**。

```mysql
-- 开启动态分区
set hive.exec.dynamic.partition=true
-- 严格模式和非严格模式

```

### 分桶表

分桶表与分区没有必然联系。将一张表的数据分散存储到不同文件。首先为每行数据计算一个hash值，hash % 指定分桶数，将取模结果相同的行写入一个文件，该文件是一个分桶。对一张表的数据进行hash分区。

一个分桶中的数据会按照分桶字段有规律的。

分桶排序表：

```mysql
-- 1.普通分桶表建表
create table stu_buck(
    id int, 
    name string
)
clustered by(id) -- 指定表中存在的字段，为分桶字段
into 4 buckets -- 分桶数，4个桶，有4个文件
row format delimited fields terminated by '\t';
-- 2.导入，普通数据的导入

-- 3.分桶排序表建表
create table stu_buck(
    id int, 
    name string
)
clustered by(id) sorted by (id) -- 分桶字段和排序字段不要求完全一致，也可以有多个
into 4 buckets -- 分桶数，4个桶，有4个文件
row format delimited fields terminated by '\t';

```



### 分桶与分区

|      | 分区                                                         | 分桶                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义 | 一张大表的数据按照业务需要分散的存储到多个分区目录（day='20220101'），分区字段是一个可查询过滤的伪列。<br />支持多级分区、静态动态分区 | 根据每行数据指定字段的hash值，将取模结果相同的写入同一个分桶 |
|      | 一张表可以既是分区又是分桶。对于分区表的分桶：会对每个分区的数据进行分桶。<br />**分区字段和分桶字段不可一样**：因为没有意义，同一个分区内分区字段相同，分桶还是一样的。另外分区字段是虚拟的，不能用于分桶。 |                                                              |
| 用处 | 99%的表都是分区表，用于查询过滤，一天分析一批数据            | 优化手段：join优化，bucket map join                          |
|      | 分区字段不能是表中的字段<br />一个分区是一目录               | 分桶字段必须是表中字段<br />分桶数为4，会load出来4个文件     |

## 文件格式



|          | textfile                                                     | orc                                                          | parquet                                                      |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义     | 文本文件，默认的文件格式<br />文本文件中的一行内容，就对应Hive表中的一行记录（行式存储） | **列式存储**，一列一列的存储                                 | 列式存储                                                     |
| 文件格式 |                                                              | head <br />body(stripe [index data row data stripe footer编码])<br />tail(file footer) | 首尾之间有若干个row group(column trunk-> page)和一个file footer(每个行组和列块都有一个footer元数据信息)<br />其实和orc类似，只是有些东西移到一起了。 |
|          |                                                              |                                                              |                                                              |

行式存储 VS 列式存储：由使用场景决定
想要获取满足条件的一整行数据，适合行式存储。想要获取一整列或者几列的数据，适合列式存储。
**where过滤的是行，select字段都是列。**所以更多情况下是列式查询更多。
**orc和parquet用的更多**，列式存储。

### ORC

![image-20230428112505323](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428112505323.png)

想要读取a列的数据：倒着读，先读取postscript的长度:arrow_right: 拿到postscript:arrow_right:，拿到filefooter，得到各个stripe的信息，比如stripe的起始位置，索引的长度:arrow_right:需要得到stripe footer，得知每一列是怎么编码的:arrow_right:去stripe里面得到对应的列，再根据编码区解析列数据。
hive在读列数据的时候会比行式存储快，不用一行一行扫描，有很多索引可以精确找到数据。
写orc文件比写文本文件要慢，但是读取会很快。

每个Orc文件由Header、Body和Tail三部分组成。
1.Header内容为ORC，用于表示文件类型。
2.Body由1个或多个stripe组成，每个stripe一般为HDFS的块大小，每一个stripe包含多条记录，这些记录按照列进行独立存储，每个stripe里有三部分组成，分别是Index Data，Row Data，Stripe Footer。
Index Data：一个轻量级的index，默认是为各列每隔**1W行做一个索引**。每个索引会记录第n万行的位置，和最近一万行的**最大值和最小值**等信息。
Row Data：按列存储具体数据，并对每个列进行编码，分成多个Stream来存储。
Stripe Footer：存放的是各个Stream的位置以及各column的编码信息。
3.Tail由File Footer和PostScript组成。File Footer中保存了各Stripe的**起始位置、索引长度、数据长度**等信息，各Column的统计信息等；PostScript记录了整个文件的压缩类型以及File Footer的长度信息等。

用的是orcSerde

| ORC **参数**           | **默认值**                     | **说明**                                                     |
| ---------------------- | ------------------------------ | ------------------------------------------------------------ |
| orc.compress  压缩算法 | ZLIB （NONE、ZLIB,、SNAPPY  ） | orc压缩文件不支持切片，但orc是分块压缩，默认256KB是不支持切片的。 |
| orc.compress.size      | 256KB                          | 每个压缩块的大小（ORC文件是分块压缩的）                      |
| orc.stripe.size        | 64MB                           | 每个stripe的大小 ，hadoop block大小，**应该调整为128M**      |
| orc.row.index.stride   | 1w                             | 索引步长（每隔多少行数据建一条索引）                         |
|                        |                                |                                                              |
| parquet参数            |                                |                                                              |
| parquet.block.size     | 128M                           | 行组大小，通常与hadoop block大小一致                         |
| **parquet.page.size**  | 1M                             | 页大小                                                       |

### parquet

![image-20230428135522108](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428135522108.png)

读取流程：倒着读得到footer，里面有所有元数据信息，可以直接获取到

## 压缩

hive支持的压缩算法于Hadoop完全保持一致。

支持切片：LZO，bzip2（输入）；压缩解压缩速度快：snappy，LZO（mapper出）；压缩文件小：gzip bzip2（reducer出）。权衡较好的：gzip

单个MR的中间结果进行压缩，单条SQL语句的中间结果进行压缩，都建议使用snappy

不同文件类型的表，声明压缩的方式不同。

|        | TextFile                                                     | ORC                                     | parquet                                         |
| ------ | ------------------------------------------------------------ | --------------------------------------- | ----------------------------------------------- |
| 建表   | 无需在建表语句做出声明                                       | tblproperties ("orc.compress"="snappy") | tblproperties ("parquet.compression"="snappy"); |
| insert | 用户需设置compress.output=true，来保证写入表中的数据是被压缩的 | 自动压缩                                | 自动压缩                                        |
| 查询   | Hive在查询表中数据时，可自动识别其压缩格式，进行解压         | 自动解压缩                              | 自动解压缩                                      |

# SQL调优

## 执行计划explain

explain [formatted（格式化信息） | extended（临时目录） | dependency（读取的表和分区）] query-sql

Explain呈现的执行计划，由一系列Stage组成，这一系列Stage具有依赖关系，每个Stage对应一个MapReduce Job / 文件系统操作。
若某个Stage对应的一个MapReduce Job，其Map端和Reduce端的计算逻辑分别由Map Operator Tree和Reduce Operator Tree，Operator Tree由一系列的Operator组成（一个单一的逻辑操作）。

| Operator               | 描述                        |      |
| ---------------------- | --------------------------- | ---- |
| TableScan              | 表扫描，通常map端第一个操作 |      |
| Select Operator        | 选取                        |      |
| Group By Operator      | 分组聚合                    |      |
| Reduce Output Operator | 输出到 reduce               |      |
| Filter Operator        | 过滤，having where          |      |
| File Output Operator   | 文件输出                    |      |
| Fetch Operator         | 客户端获取数据              |      |

列裁剪 map端预聚合（在map operator tree里面group by）

![image-20230428145746744](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428145746744.png)

## 分组聚合优化

Hive对分组聚合的优化主要围绕着减少Shuffle数据量进行，具体做法是**map-side聚合**。
不是combiner，是在map端维护一个hash table，利用其完成部分的聚合，然后将部分聚合的结果。（上面那张图的可视化）

![image-20230428150609486](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428150609486.png)

## join优化

Hash Join和Sort Merge Join均为关系型数据库中常见的Join实现算法。Hash Join对参与join的一张表构建hash table，扫描另外一张表进行**逐行匹配**。**Sort Merge Join**取左边的一个key到!key就算取完（有序），右表也是key到!key，进行join。

| join算法                        | 概述                                                         | 触发           |
| ------------------------------- | ------------------------------------------------------------ | -------------- |
| Common Join                     | **默认**，最稳定，通过MR join完成。<br />按照**关联字段**分区，通过Shuffle，将其发送到Reduce端，相同key的数据在Reduce端join（数据倾斜）<br />一个sql语句中的**相邻**的且**关联字段相同**的多个join操作可以合并为一个Common Join task，如果join的关联字段不同，由两个MR job去做。 |                |
| Map Join                        | 两个只有map阶段的Job完成一个join，**没有reduce，没有shuffle**。**必须大表join小表**，缓存放得下<br />第一个job读小表，存在HDFS缓存；第二个job先缓存小表，再扫描大表，map端关联，不需要相同的key发给reducejoin。 | hint，自动     |
| Bucket Map Join                 | 由于mapjoin使用局限性（用到缓存），Bucket Map Join可以大表join大表。<br />参与join的是分桶表  &join字段=分桶字段  & 一张表的分桶数量（4个桶）是另一张表（2个桶）的整数倍。<br />满足以上条件，利用分桶关联关系，缓存一个桶（有2个桶的表的桶），进行**hash join**算法。<br />使用bucketInputFormat，有4个桶启动4个mapper | MR引擎只能hint |
| Sort Merge Bucket Map Join(SMB) | 基于BucketMapJoin。<br />参与join的是**排序**分桶表  & 关联字段=分桶字段**=排序字段**  & 一张表的分桶数量（4个桶）是另一张表（2个桶）的整数倍。<br />利用分桶关联关系，进行**sort merge join 算法**。<br />比bucket map 内存要求更低，**不需要缓存桶了** | hint，自动     |

![image-20230428163101363](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428163101363.png)

### map join触发方式

hint提示触发（过时），hive优化器自动触发

起初所有的join操作均采用Common Join算法实现，但hive会**根据表大小**（表大小<map内存阈值）判断common join是否可以转为map join。
但有些Common Join所需的表大小**未知**（子查询join，不知道谁是小表），Hive会在编译阶段生成一个条件任务（Conditional Task），其下会包含一个计划列表，包含转换后的2个Map Join以及原有的Common Join（后备任务，万一两个都是大表）。只有运行阶段才能确定走那个condition task能跑完，先让map join A小表跑，不然则让map jion B小表跑，不然则只能跑commonJoin。

启动Map Join自动转换，开启无条件转Map Join，设置最大缓存（不超过Map端总内存的1/2 - 2/3）,如果能通过我们设置的mapJoin判断就走mapJoin计划，同时判断需不需要join合并，不能通过就走commonJoin。

![image-20230428171642314](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230428171642314.png)

CBO优化：我们写的多表join顺序未必是hive执行的多表join顺序。

### bucket map join触发方式

Bucket Map Join不支持自动转换，必须通过用户在SQL语句中提供如下Hint提示。因为Hive已经放弃MR引擎了。

根据map端的内存，考虑分几个桶。

### SMB

只需要考虑能不能走SMB，分几个桶无所谓，几乎不占用缓存

## 数据倾斜

Hive中的数据倾斜常出现在分组聚合和join操作。

### 分组聚合数据倾斜

开启Map-Side聚合后，数据会现在Map端完成部分聚合工作。

**Skew-GroupBy**启动两个MR任务，第一个MR按照**随机数**分区，将数据分散发送到Reduce，完成部分聚合；第二个MR按照**分组字段**分区，完成最终聚合。

### join数据倾斜

map join：join操作仅在map端就能完成，没有shuffle操作，没有reduce阶段。
**skew join**：为倾斜的大key单独启动一个map join任务进行计算，其余key进行正常的common join
调整SQL语句：加随机数打散

```mysql
-- 原始
select
    *
from A
join B
on A.id=B.id;

-- 随机数打散
select
    *
from(
    select --打散操作
        concat(id,'_',cast(rand()*2 as int)) id,
        value
    from A
)ta
join(
    select --扩容操作
        concat(id,'_',0) id,
        value
    from B
    union all
    select
        concat(id,'_',1) id,
        value
    from B
)tb
on ta.id=tb.id;
```

### 任务并行度

map端并行度（map个数）：默认由切片数决定，一个切片256M，但在于以下情况：
	大量小文件（用combineInputFormat调整map个数变小）
	map端有复杂的查询逻辑（正则替换，json解析），增大map

reduce端并行度（reduce个数）：用户指定，或者Hive根据文件大小估算。如果用户指定，用指定值；如果未指定（-1），那么Hive用文件大小 / 单个reduce的计算量得到reduce个数，当然会有一个上限maxReducer。

```
--指定Reduce端并行度，默认值为-1，表示用户未指定
set mapreduce.job.reduces;
--Reduce端并行度最大值
set hive.exec.reducers.max;
--单个Reduce Task计算的数据量，用于估算Reduce并行度
set hive.exec.reducers.bytes.per.reducer;

```

## 小文件问题

map输入合并：ConbineInputFormat
reduce输出合并：根据计算任务输出文件的平均大小进行判断，若符合条件，则单独启动一个额外的任务进行合并

## 其他优化

### CBO优化

cost based optimizer，基于计算成本的优化。
CBO在hive的MR引擎下主要用于**join顺序的优化**，考虑：数据的行数、CPU、本地IO、HDFS IO、网络IO等。选出最优的执行顺序

### 谓词下推

**将where过滤操作前移**，以减少后续计算步骤的数据量。

CBO优化也会完成一部分的谓词下推优化工作，因为在执行计划中，谓词越靠前，整个计划的计算成本就会越低。

### 矢量化查询优化

依赖于CPU的矢量化计算（**将一列数据当成一个向量，批量计算出来，减少对CPU指令的调用**），可以极大的提高一些典型查询场景（例如scans, filters, aggregates, and joins）下的CPU使用效率。

若执行计划中，出现“Execution mode: vectorized”字样，即表明使用了矢量化计算。

不是所有情况下都可以使用矢量化查询，对于复杂数据类型（array map struct）不支持。

### 本地模式

如果Hive的输入数据量很小，可以通过本地模式在单台机器上处理所有的任务。对于小数据集，执行时间可以明显被缩短。

### 并行执行优化

指的是没有依赖关系的stage（多个MR）的并行执行。map和reduce本来就是并行执行的，这说的说多个MR过程是并行执行的

### 严格模式

防止高危操作。

| 高危操作                  | 禁止                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 分区表不使用分区过滤      | 除非where语句中含有分区字段过滤条件来限制范围，否则不允许执行 |
| 使用order by没有limit过滤 | 使用了order by语句的查询，要求必须使用limit语句。            |
| 笛卡尔积                  | 限制笛卡尔积的查询                                           |

