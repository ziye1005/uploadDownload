# 基础知识总结

## SQL基础

元组是行，属性（码）是列。候选码（主属性）：唯一标识一行的属性（index_id）。主码（主键），外码（外键）

ER图：实体联系图，提供了实体、属性、联系。

![学生与课程之间联系的E-R图](https://oss.javaguide.cn/github/javaguide/csdn/c745c87f6eda9a439e0eea52012c7f4a.png)

数据库范式：遵循的范式级别越高，数据冗余性就越低。共有五个范式

| 范式          | 定义                                                     | 其他                                                         |
| ------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 1NF(第一范式) | 属性不可再分                                             | 1NF 是所有关系型数据库的**最基本要求** ，关系型数据库中创建的表一定满足第一范式。 |
| 2NF           | 1NF 的基础之上，消除了非主属性对于码的**部分函数依赖**。 | 加上主键，非主属性都依赖于主键。分表                         |
| 3NF           | 2NF 的基础之上，消除了非主属性对于码的**传递函数依赖**   | 分表。**基本**上解决了数据冗余过大，插入异常，修改异常，删除异常的问题。 |

函数依赖：属性X确定了，Y随着确定，则Y依赖X，记为X-> Y
部分函数依赖：X1->Y且 X2->Y，Y依赖于X1也依赖于X2（姓名依赖学号，也依赖身份证号）
完全函数依赖：X1和X2无法单独确定Y，但是合在一起可以。(X1,X2) -> Y（班级号，学号-> 姓名）
传递函数依赖：X->Y，Y->Z，那么Z传递依赖于X（学号->系名，系名->系主任）

不推荐使用外键和级联，外键与级联更新适用于单机低并发，不适合分布式、高并发集群; 级联更新是强阻塞，存在数据库更新风暴的风 险; 外键影响数据库的插入速度。
禁止使用存储过程，难以调试和扩展，没有移植性。

数据库设计：
需求分析 : 分析用户的需求，包括数据、功能和性能需求。
概念结构设计 : E-R图。
逻辑结构设计 : 表结构设计。
物理结构设计 : 主要是为所设计的数据库选择合适的存储结构和存取路径。
数据库实施： 包括编程、测试和试运行
数据库的运行和维护 : 系统的运行与数据库的日常维护。



## 字符集

MySQL支持utf8，utf8mb4

| 常见字符集 | 范围                                                         | 字节                                                        |
| ---------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| ASCII      | 英语字符集，没有考虑中文                                     | 1                                                           |
| GB2312     | 6700汉字，不支持生僻字繁体字                                 | 英文字符1字节，非英2字节                                    |
| GBK        | 兼容2312，20000汉字                                          |                                                             |
| GB18030    | 兼容GB2312和GBK，70000汉字 + 日韩汉字<br />目前为止最全面的汉字字符集 |                                                             |
| BIG5       | 繁体字，13000                                                |                                                             |
| utf-8      | 包含了世界上几乎所有已知的字符<br />目前使用最广的一种字符编码 | 只支持1-3字节，一般1字节，中文3字节，<br />emoji和繁体4字节 |
| utf-8mb4   | UTF-8 的完整实现，正版                                       | 最多支持使用 4 个字节表示字符，可以用来存储 emoji 符号      |

## SQL语句执行过程

![img](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

简单来说 MySQL 主要分为 Server 层、存储引擎层：

- **Server 层**：主要包括连接器、查询缓存、分析器、优化器、执行器等，所有跨存储引擎的功能都在这一层实现，比如存储过程、触发器、视图，函数等，还有一个通用的日志模块 binlog 日志模块。
- **存储引擎**： 主要负责数据的存储和读取，采用可以替换的**插件式**架构，支持 InnoDB、MyISAM、Memory 等多个存储引擎，其中 InnoDB 引擎有自有的日志模块 redolog 模块。现在最常用的存储引擎是 InnoDB。

| Server层组件                   | 介绍                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| 连接器                         | 身份认证、权限管理，门卫                                     |
| 查询缓存(MySQL 8.0 版本后移除) | 缓存我们所执行的 SELECT 语句以及该语句的结果集               |
| 分析器                         | 分析sql语句是否合法，提取查询关键字                          |
| 优化器                         | 用MySQL认为的最优方案去执行                                  |
| 执行器                         | **判断权限**，如果有，调用存储引擎的接口，返回接口执行的结果 |

查询语句执行流程：权限校验（如果命中缓存）--->查询缓存--->分析器--->优化器--->权限校验--->执行器--->引擎--->返回结果

更新语句执行流程：前面差不多，引擎之后，引擎--->redo log(prepare 状态)--->binlog--->redo log(commit状态)

InnoDB 引擎把数据保存在内存中，同时记录 redo log（重做日志），此时 redo log 进入 prepare 状态，然后告诉执行器，执行完成了，随时可以提交。执行器收到通知后记录 binlog，然后调用引擎接口，提交 redo log 为提交状态。这是为了保证数据一致性，防止正在提交时数据库挂了。

## MySQL优化

### 总体优化

优点：
成熟稳定，功能完善
开源免费；
兼容性好；
事务支持；
支持分库分表、读写分离、高可用

存储IP地址：
可以将 IP 地址转换成整形数据存储，性能更好，占用空间也更小。
插入数据前，先用 `INET_ATON()` 把 ip 地址转为整型，显示数据时，使用 `INET_NTOA()` 把整型的 ip 地址转为地址显示即可。



| 优化方法                                                     | 原因                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **基本设计规范**                                             |                                                              |
| 所有表必须使用 InnoDB 存储引擎                               | 支持事务，支持行级锁                                         |
| 字符集统一使用 UTF8                                          |                                                              |
| 所有表和字段都需要添加注释                                   | 数据字典的维护                                               |
| 单表数据量< 500 万                                           | 历史数据归档，分库分表等控制数据量大小                       |
| 谨慎使用分区表                                               | 物理上多个文件逻辑上是一个表，跨分区查询效率更低<br />建议采用物理分表的方式管理大数据 |
| 禁止在表中建立预留字段                                       | 很难做到见名识义，也难以确定数据类型                         |
| 禁止在数据库中存储文件（比如图片）这类大的二进制数据         |                                                              |
| 不要被范式束缚                                               | 满足3NF需要分好多表                                          |
| **字段设计规范**                                             |                                                              |
| 避免使用 ENUM 类型                                           | ORDER BY 操作效率低，需要额外操作                            |
| 尽可能把所有列定义为 NOT NULL                                |                                                              |
| 使用 TIMESTAMP(4 个字节) 或 DATETIME 类型 (8 个字节) 存储时间 |                                                              |
| 同财务相关的金额类数据必须使用 decimal 类型                  |                                                              |
| **索引设计规范**                                             |                                                              |
| 限制每张表上的索引数量,建议单张表索引不超过 5 个             |                                                              |
| 禁止使用全文索引                                             |                                                              |
| 每个 InnoDB 表必须有个主键                                   |                                                              |
| **SQL语句规范**                                              |                                                              |
| 禁止使用 SELECT *                                            |                                                              |
| 禁止使用不含字段列表的 INSERT 语句                           |                                                              |
| 避免使用子查询，可以把子查询优化为 join 操作                 | 子查询的结果集无法使用索引                                   |
| 减少同数据库的交互次数                                       | insert多条                                                   |
| in 代替 or                                                   |                                                              |
| 禁止使用 order by rand() 进行随机排序                        |                                                              |
| WHERE 从句中禁止对列进行函数转换和计算                       | 无法使用索引                                                 |

### distinct优化

大量的排序操作影响系统性能，所以尽量减少排序操作。GROUP BY、ORDER BY、 ROLLUP、[DISTINCT](https://so.csdn.net/so/search?q=DISTINCT&spm=1001.2101.3001.7020)等都会产生排序。少用DISTINCT！

1.exists代替distinct：EXISTS 使查询更为迅速，因为RDBMS核心模块将在子查询的条件一旦满足后，立刻返回结果。

```mysql
select distinct userid,username from user,userinfo where user.userid=userinfo.userid -- 低效率
select userid,username from user where exists (select 'T' from userinfo where userinfo.userid=user.userid) --高效率
```



## 读写分离—处理并发

![读写分离示意图](https://oss.javaguide.cn/github/javaguide/high-performance/read-and-write-separation-and-library-subtable/read-and-write-separation.png)

将对数据库的读写操作分散到不同的数据库节点上。处理**并发**有效，但会有**主从同步延迟**。
解决方法：强制将读请求路由到主库处理；延迟读取。

实现方法：
1.部署多台数据库，选择其中的一台作为主数据库，其他的一台或者多台作为从数据库。
2.保证主数据库和从数据库之间的数据是实时同步的，这个过程也就是我们常说的**主从复制**。
3.系统将写请求交给主数据库处理，读请求交给从数据库处理。

具体方式：
1.代理方式（ MySQL Router（官方）、Atlas（基于 MySQL Proxy）、Maxscale、MyCat）：数据请求交给代理层处理，代理层负责分离读写请求，将它们**路由**到对应的数据库中。
2.组件方式（sharding-jdbc）：引入第三方组件来处理读写请求

MySQL 主从复制是依赖于 binlog 。另外，常见的一些同步 MySQL 数据到其他数据源的工具（比如 canal）的底层一般也是依赖 binlog

## 分库分表—数据表过大

分库分表标准：存储占用100G+；数据增量每天200w+；单表条数1亿条+

分库：将数据分散到不同的数据库，分为垂直分库：不同的业务使用不同的数据库（用户表、订单表、商品表）
																	水平分库：同一个表按一定规则拆分到不同的数据库中（一个订单表拆成两个）

分表：对单表的数据进行拆分，分为垂直分表：列拆分，将一些列单独拿出来
                                                             水平分表：行拆分，解决单表数据量大的问题

分库分表带来的问题：
1.**join 操作** ： 分库无法使用 join 操作。需要手动进行数据的封装，比如你在一个数据库中查询到一个数据之后，再根据这个数据去另外一个数据库中找对应的数据。
2.**事务问题** ：单个操作涉及到多个数据库，数据库自带的事务就无法满足。
3.**分布式 id** ：分库下，自增主键无法保证唯一。需要引入分布式 id 。

解决：ShardingSphere 。支持读写分离和分库分表，还提供分布式事务、数据库治理等功能；生态体系完善，社区活跃，文档完善，更新和发布比较频繁。

数据迁移：
1.停机迁移：半夜迁移一小时
2.双写方案：我们对老库的更新操作（增删改），同时也要写入新库（双写）。如果操作的数据不存在于新库的话，需要插入到新库中。 这样就能保证，咱们新库里的数据是最新的。
在迁移过程，双写只会让被更新操作过的老库中的数据同步到新库，我们还需要自己写脚本将老库中的数据和新库的数据做比对。如果新库中没有，那咱们就把数据插入到新库。如果新库有，旧库没有，就把新库对应的数据删除（冗余数据清理）。
重复上一步的操作，直到老库和新库的数据一致为止。

## 分区

分库分表和分区并不冲突，可以结合使用。

|      | 分区                                                         | 分库分表                               |
| ---- | ------------------------------------------------------------ | -------------------------------------- |
| 字段 | 如果表中存在主键或唯一索引时，分区列必须是唯一索引的一个组成部分。 | 查询字段，数值型                       |
| 位置 | 分区一般都是放在单机里的，用的比较多的是时间范围分区，方便归档 | 是为了承接超大规模的表，单机放不下那种 |
| 实现 | mysql内部实现（partition by）                                | 代码实现                               |

# SQL分类

## DDL 数据定义语言

<font color='red'>data Definition language(对数据库和表结构的操作，不涉及到数据)</font>

<font color='red'>create alter drop show desc use </font>

alter操作是要带table，drop带着table

| 功能                                   | **SQL**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| 查看所有的数据库                       | show databases；                                             |
| 创建数据库                             | create database if not exists mydb1                          |
| 切换 (选择要操作的) 数据库             | use mydb1；                                                  |
| 删除数据库                             | drop database ifexists mydb1；                               |
| 修改数据库编码                         | alter database mydb1 characterset utf8;                      |
|                                        |                                                              |
| 创建表                                 | create table if not EXISTS student();                        |
| 查看所有表                             | show tables;                                                 |
| 查看指定某个表的创建语句               | show create table 表名；                                     |
| 查看表结构                             | desc 表名                                                    |
| 删除表                                 | drop table 表名                                              |
| 创建视图                               | create or replace view view_emp<br/>as<br/>select ename, job from emp; |
| 修改视图                               | alter view view_emp<br/>as <br/>select a.deptno,a.dname,a.loc,b.ename,b.sal from dept a, emp b where a.deptno = b.deptno; |
| 删除视图                               | drop view if exists view_emp                                 |
| 重命名视图                             | rename table view_emp to new_view;                           |
| 创建索引                               | CREATE index index_gender on student(gender);<br />alter table student add index index_age(age); |
| **<font color='red'>修改alter</font>** |                                                              |
| 添加列                                 | ALTER TABLE testmysql.student add `dept` varchar(20);        |
| 修改列名或类型                         | alter table student CHANGE `dept` department VARCHAR(20);    |
| 删除列                                 | alter table student drop department;                         |
| 添加主键                               | alter table employee add PRIMARY key (id);                   |
| 添加外键                               | alter table slave add constraint dept_id_fk foreign key(dept_id) references master(deptno); |
| 删除主键                               | alter table employee drop primary key;                       |
| 删除外键                               | alter table slave drop foreign key dept_id_fk ;              |
| 指定自增长初始值                       | alter table employee  **auto_increment** = 100;              |
| 添加非空约束                           | alter table employee **modify** name varchar(20) not null;   |
| 添加唯一约束                           | alter table employee add [constraint unique_ph] unique(name); |

数据类型：

默认有符号，无符号：int unsigned

| 数值**类型**  | 字节 | **范围（有符号）**                                           | **范围（无符号）**很少用                                     | **用途**         |
| ------------- | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| TINYINT       | 1    | (-128，127)                                                  | (0，255)                                                     | 小整数值         |
| SMALLINT      | 2    | (-32 768，32767)                                             | (0，65 535)                                                  | 大整数值         |
| MEDIUMINT     | 3    | (-8 388 608，8388 607)                                       | (0，16 777 215)                                              | 大整数值         |
| **INT常用**   | 4    | (-2 147 483 648，2147 483 647)                               | (0，4 294 967 295)                                           | 大整数值         |
| BIGINT        | 8    | (-9,223,372,036,854,775,808，9223 372 036 854 775 807)       | (0，18 446 744 073 709 551 615)                              | 极大整数值       |
| FLOAT         | 4    | (-3.402 823 466 E+38，3.402823 466 351 E+38)                 | 0，(1.175 494 351 E-38，3.402823 466 E+38)                   | 单精度 浮点数值  |
| **DOUBLE**    | 8    | (-1.797 693 134 862 315 7 E+308，1.797693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797693 134 862 315 7 E+308) | 双精度 浮点数值  |
| DECIMAL       |      | 依赖于M和D的值                                               | 依赖于M和D的值                                               | 小数值           |
| 字符串类型    |      |                                                              |                                                              |                  |
| **varchar**   |      |                                                              | 0，65535                                                     | 变长字符串       |
| text          |      |                                                              | 0，65535                                                     | 长文本数据       |
| 日期类型      |      |                                                              |                                                              |                  |
| **Date**      | 3    | YYYY-MM-DD                                                   |                                                              | 日期值           |
| Time          | 3    | HH:MM:DD                                                     |                                                              | 时间值或持续时间 |
| **datetime**  | 8    | 合并了Date和Time                                             |                                                              | 混合日期和时间   |
| **TimeStamp** | 4    | 混合date和time，加上时区                                     |                                                              | 默认获取时区时间 |

## DML 数据操纵语言

<font color='red'>insert update delete truncate</font>

<font color='red'>Data Multipulation language 数据操纵语言，对表进行增删改，不是查询</font>

| 操作                 | 语句                                                         |
| -------------------- | ------------------------------------------------------------ |
| 插入insert           | insert into student values(1001,'男',18,'1996-12-23','北京',83.5),(...),(...); |
| 修改update set       | update student set address = '美国';<br />UPDATE employee set **salary = 4000, gender = '女'** WHERE `name` = '李四';--  一次改两个，逗号<br/>UPDATE employee set **salary = salary + 1000** WHERE `name` = '王五';--  原有基础上改变 |
| 删除 delete truncate | delete from student where name = 'zhangsan'; --  删除单条<br />delete from student;--  删除全部内容<br />truncate student; --  将整个表删除，然后再创建该表 |

delete数据之后自动增长从断点开始（上次是3，全删除了，依然从4开始）
truncate数据之后自动增长从默认起始值开始（重置了，从1开始）

## TCL事务控制语言

管理数据库中的事务，Commit和rollback

## DCL数据控制语言

对数据**访问权**进行控制的指令，它可以控制特定用户账户对数据表、查看表、预存程序、用户自定义函数等数据库对象的控制权。grant和revoke

# 约束

主键约束(primary key)  add
自增长约束(auto_increment)
非空约束(not null) 不能为空 modify
唯一性约束(unique) 不重复 add
默认约束(default) 有默认值 modify
零填充约束(zerofill)
外键约束(foreign key) 多表 add

唯一约束没有特殊的表示

1. 主键约束

一个列或者多个列的组合，唯一的标识表中的每一行，方便查找

**主键列唯一，分空**

每个表只能允许一个主键（一列或者多个列加在一起是联合主键，联合主键也是一个主键）

添加单列主键：定义时字段类型后面id int primary key，或者最后primary key(id)

添加联合主键：primary key(name, id) --  唯一：name与id不完全一样就可以；不空：name和id都不为空才可以

使用alter另外添加或者删除主键

2. 自增长约束

通过给字段添加 auto_increment 属性来实现**主键自增长**，一定是主键自增长

```mysql
CREATE TABLE if not EXISTS `employee`(
  `id` int PRIMARY key auto_increment comment '编号',--  主键自增长约束
  `name` varchar(20) not null, --  非空约束
  `numberUnique` varchar(20) unique, --  唯一约束
  `address` varchar(20) default '北京', --  默认约束
  `gender` varchar(10) comment '性别',  -- 加注释
  `salary` double DEFAULT NULL, 
   index index_name(name), -- 给name列创建普通索引
    unique index_numberUnique(numberUnique), -- 创建唯一索引
    CONSTRAINT emp_dept FOREIGN key(id) REFERENCES dept (deptno) -- 外键约束
    --  这个最后的逗号不能要
);

INSERT into testmysql.employee VALUES(NULL,'张三', '男', 4000); --  第一列是自增长的，所以写null就好，不写不行，指定值也可以
```

默认初始值是 1，每新增一条记录，字段值自动加 1。
一个表只能有一个字段使用 auto_increment约束，且一定是主键或者主键一部分。
auto_increment约束的字段只能是整数类型（TINYINT、SMALLINT、INT、BIGINT 等。
auto_increment约束字段的最大值受该字段的数据类型约束，如果达到上限，auto_increment就会失效。

3. 非空约束

限制不为空 name varchar(20) not null

或者 alter table employee modify name varchar(20) not null;

删除非空约束:alter table employee modify name varchar(20);

```mysql
alter table employee add PRIMARY key (id);
-- 添加主键约束
alter table employee auto_increment=100;
--  自增长约束auto_increment：
alter TABLE testmysql.employee MODIFY name VARCHAR(20) not null; 
-- 非空约束not null： 如果送进去的name是 NULL不可以，是'NULL'可以代表值为NULL的字符串，是''也可以代表空串
alter table employee add constraint unique_ph unique(phone_number);
--  唯一约束unique，NULL！=NULL
alter table employee modify address varchar(20) default  ‘北京’; 
--  默认约束default，
id int zerofill 
--  0填充约束zerofill，默认0填充的数字是int(10)，前面9个0，指定0填充约束的列为unsigned无符号类型
```

4. 唯一约束

限制不能重复 字段后面+	 unique

在MySQL里面，**<font color='red'>NULL和自己的值不相同，NULL！= NULL</font>**

# DQL 数据查询语言

## 基本查询

<font color='red'>where与from连用，having与group by连用</font>

select 
  [all|distinct]
  <目标列的表达式1> [别名],
  <目标列的表达式2> [别名]...
from <表名或视图名> [别名],<表名或视图名> [别名]...
[where<条件表达式>]
[group by <列名> 
[having <条件表达式>]]
[order by <列名> [asc|desc]]
[limit <数字或者列表>];

### From关键字

```mysql
查全表：SELECT * from product; -- 全部列
指定字段查询：select pname, price from product; -- 查询特定列
去重列查询 SELECT DISTINCT price from product as p; -- 取出某一列不重复的值，取别名
去重行查询 SELECT DISTINCT * from product
select pname, price + 10 from product; -- 列为price+10
```



运算符：

```mysql
+ - * / % = != < ...
<=> 安全等于，两个操作码均为NULL时，其所得值为1；而当一个操作码为NULL时，其所得值为0
is null和is not null
least greatest -- 最小值，最大值
between and / in / not in -- between and 是两个闭区间
		select * from product where price BETWEEN 200 and 1000;
like -- 通配符
regexp -- 正则表达式匹配
and or xor not -- 逻辑运算符，与或异或非
| & ^ << >> ~ -- 位运算符

```

```mysql
select * from product where pname like '__冰%'; -- 查询第三个字为‘冰’的
select * from product where pname like '海%'; -- 查询海字开头的
select greatest(10, null, 30); -- null，在least和greatest函数中，如果有null，均直接返回null
```

distinct：去重，别名：多表查询

在least和greatest函数中，如果有null，均直接返回null

### 排序查询order by

```mysql
SELECT * FROM product ORDER BY price asc; -- 默认升序
SELECT * FROM product ORDER BY catergory_id asc,price desc; -- 按照catergory_id升序，price降序
```

### 聚合查询

针对列,全部忽略null值

```mysql
count() -- 求行数，不包括null
sum() max() min(),avg() -- 不包括null
select COUNT(*) FROM product where price > 200;
select max(price) from product;
select avg(price) FROM product WHERE catergory_id = 'c002'
```

### group by 分组查询，limit 分页查询

在select 中的列，没有在group by 中出现，那么这个SQL是不合法的

**select** 字段1**,**字段2**…** **from** 表名 **group** **by** 分组字段 **having** 分组条件**;**

如果要进行分组的话，则SELECT子句之后，只能出现**<font color='red'>分组的字段和统计函数</font>**，其他的字段不能出现。

having是对分组之后的结果进行筛选，但不能用where，where与from联用

```mysql
SELECT catergory_id, count(*) from product GROUP BY catergory_id; -- 根据catergory_id分组得到个数（select * ）,在前一列显示catergory_id
SELECT catergory_id, count(pid) from product GROUP BY catergory_id having count(pid) > 4; -- 分组后，筛选出count(*)> 4的数据

select 字段1，字段2... from 表明 limit m,n --从第m+1条开始，显示n条
select * from tablea limit 0, 60 --从第一页开始，显示60条
limit n offset m 跳过m条从m+1条开始取n条
```

### 两个表 insert into select

```mysql
insert into pro3 select catergory_id, count(*) from product GROUP BY catergory_id HAVING COUNT(*) > 4;
-- pro3这个表必须存在，而且字段类型与select后面的相符，字段名可以任意取
```

### 总结:star:

SQL书写顺序：**<font color='red'>select catergory_id caid, count(*) cnt FROM product WHERE price > 100 GROUP BY catergory_id HAVING cnt > 4 limit 5;</font>**

select:arrow_right: 要返回的数据列:arrow_right: from:arrow_right: 表名:arrow_right:<left join, right ,join >:arrow_right:on :arrow_right:where:arrow_right: group by :arrow_right: having:arrow_right: order by :arrow_right:limit

caid cnt是别名

执行顺序：from --->where ---> group by ---->count(pid) ---->having --->select ---->order by ---->limit 

## 正则表达式查询 regexp

| **模式**       | **描述**                                                     |
| -------------- | ------------------------------------------------------------ |
| **^**          | 匹配输入字符串的开始位置。                                   |
| **$**          | 匹配输入字符串的结束位置。                                   |
| **.**          | 匹配除 "\n" 之外的任何单个字符。                             |
| **[...]**      | 字符集合。匹配[]所包含的任意一个字符。例如，  '[abc]' 可以匹配  "plain" 中的  'a'。 |
| **[^...]**     | 负值字符集合。匹配[]未包含的任意字符。例如，  '[^abc]' 可以匹配  "plain" 中的'p'。 |
| **p1\|p2\|p3** | 匹配 p1 或 p2  或 p3。例如，'z\|food' 能匹配  "z" 或  "food"。'(z\|f)ood' 则匹配  "zood"  或  "food"。 |
| *****          | 匹配前面的子表达式零次或多次。例如，zo * 能匹配              |
| **+**          | 匹配前面的子表达式一次或多次。例如，'zo+'  能匹配  "zo" 以及  "zoo"，但不能匹配  "z"。+ 等价于  {1,}。 |
| **{n}**        | n 是一个非负整数。匹配确定的 n 次。例如，'o{2}'  不能匹配  "Bob" 中的  'o'，但是能匹配  "food"  中的两个 o。 |
| **{n,m}**      | m 和 n 均为非负整数，其中n  <= m。最少匹配 n 次且最多匹配 m 次。   例如，'au{3,5}c'能匹配auuuc,auuuuc,auuuuuc |

```
select 'auc' REGEXP 'au{1,5}c'; -- 1
select * from product WHERE pname REGEXP '^海'; -- 以海开头
```

^只有在[]才表示不取，其他时候表示开头

# 多表关系

一对一：可以直接合并表

一对多/多对一关系：大表和小表有字段重，可以小表join进大表

多对多：两个1对多，需要中间表，有两个或多个主表，控制一个从表

![image-20230401092950327](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230401092950327.png)

## 外键约束

建立主表与从表的关联关系，主表控制从表，主表有的从表才能写，约束两个表中数据的一致性和完整性。

主表必须已经存在于数据库中，或者是当前正在创建的表。
主表中：主键不能包含空值，外键值是可以有重复的，可以是空值。一个表可以有多个外键。
从表中：主表的外键必须是它的主键。
外键中列的数目必须和主表的主键中列的数目相同。
外键中列的数据类型必须和主表主键中对应列的数据类型相同。

```mysql
-- 创建部门表
create table if not exists dept(
  deptno varchar(20),  -- 部门号
  name varchar(20), -- 部门名字
  CONSTRAINT pri PRIMARY KEY(deptno) -- 主键约束
);
-- 创建员工表
create table if not EXISTS emp(
eid VARCHAR(20) PRIMARY key,
ename VARCHAR(20),
age int,
dept_id VARCHAR(20),
CONSTRAINT emp_dept FOREIGN key(dept_id) REFERENCES dept (deptno) -- 外键约束
);
-- 添加数据
必须先给主表添加数据，才能给从表添加数据
给从表添加数据时，外键列的值不能随便写，必须依赖主表的主键列，主表没有的值不能写

-- 删除数据
主表被依赖的数据，不能删除
从表的数据可以随便删除
```

先拉取所有主表，再拉取从表

![image-20230331232912394](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230331232912394.png)

## 多表联合查询

外键约束对多表查询无影响

交叉连接查询 [产生笛卡尔积，了解，很多冗余数据]
      语法：select * from A,B;  

内连接查询(使用的关键字 inner join  -- inner可以省略)
    隐式内连接（SQL92标准）：select * from A,B where 条件;
    显示内连接（SQL99标准）：select * from A inner join B on 条件;

外连接查询(使用的关键字 outer join -- outer可以省略)
        左外连接：left outer join
            select * from A left outer join B on 条件;
        右外连接：right outer join
            select * from A right outer join B on 条件;
        满外连接: full outer join
             select * from A full outer join B on 条件;
子查询
       select的嵌套
表自关联：
       将一张表当成多张表来用

```mysql
-- 1.交叉联合查询，笛卡尔积，一行去连接所有行
select * from dept3,emp3;
-- 2.内连接求多张表的交集,inner可以不写，推荐99标准
select * from dept3, emp3 where dept3.deptno = emp3.dept_id;
select * from dept3 JOIN emp3 ON dept3.deptno = emp3.dept_id; -- 逗号编程inner join,where变成on
-- 查询研发部和销售部的所属员工，用in运算符查询
select * from dept3 JOIN emp3 ON dept3.deptno = emp3.dept_id and dept3.name in ('研发部', '销售部');
-- 查询人数>=3的每个部门的员工数,并升序排序（每个部门需要group by，限制需要having，升序排序需要order by，记得起别名）
select a.deptno,count(1) cnt from dept3 a JOIN emp3 b on a.deptno = b.dept_id GROUP BY a.deptno HAVING cnt >= 3 ORDER BY cnt desc; 

-- 3.外连接,outer join,outer可以不写，只写left join, right join, full join
-- 左外连接，以左表为主，左表的全部数据输出，右表有就输出，没有就右表null
-- 满外连接，将左外的右外的连接合并
-- 查询没有员工的部门信息，left join
select * from dept3 a left join emp3 b on a.deptno = b.dept_id where b.eid <=> NULL;
-- full join，使用union将两个结果连接起来，union去重
select * from dept3 left outer join emp3 on dept3.deptno = emp3.dept_id 
union 
select * from dept3 right outer join emp3 on dept3.deptno = emp3.dept_id;
```

子查询关键字

1.ALL关键字：与子查询返回的**所有值**比较为true 则返回true

2.ANY关键字 ： SOME关键字 与子查询返回的**任何值**比较为true 则返回true

4.IN关键字：用于判断某个记录的值，是否在指定的集合中； not in 在IN关键字前边加上not可以将条件反过来

5.EXISTS关键字

该子查询如果“有数据结果”(至少返回一行数据)， 则该EXISTS() 的结果为“true”，外层查询执行
该子查询如果“没有数据结果”（没有任何数据返回），则该EXISTS()的结果为“false”，外层查询不执行
EXISTS后面的子查询不返回任何实际数据，只返回真或假，当返回真时 where条件成立
注意，EXISTS关键字，**比IN关键字的运算效率高**，因此，在实际开发中，特别是大数据量时，推荐使用EXISTS关键字       

```mysql
-- 4.子查询，单表即可；select嵌套，可以解决求最值以及最值的条目
-- 4.1 单行单列 查询最大年龄
SELECT * from emp3  WHERE age = (SELECT min(age) FROM emp3);
SELECT * from emp3 a WHERE a.age = (SELECT min(b.age) FROM emp3 b);
-- 4.2 查询研发部和销售部的小于50岁的员工信息，包含员工号、员工名字
SELECT * from emp3 b WHERE b.dept_id in (select a.deptno from dept3 a WHERE a.name = '研发部' or a.name = '销售部') and b.age < 50; 
-- 4.3 all查询
-- 查询年龄大于‘1003’部门所有年龄的员工信息
select * from emp3 where age > all(select age from emp3 where dept_id = '1003');
-- 查询不属于任何一个部门的员工信息 
select * from emp3 a WHERE a.dept_id != all(SELECT b.deptno from dept3 b); 
-- 4.4 any some关键字
select * from emp3 where age > any(select age from emp3 where dept_id = '1003');
-- 4.5 in 关键字
-- 4.6 exist关键字，与其他关键字不同，前面没有主语
select * from emp3 a where exists(select 1); -- 全表输出
SELECT * from emp3 WHERE EXISTS (SELECT * from emp3 WHERE age > 60); -- 全表输出
SELECT * from emp3 a WHERE EXISTS (SELECT * from emp3 WHERE a.age > 60); -- 查询年龄大于60岁的员工输出
-- 查询有所属部门的员工信息
select * FROM emp3 a WHERE dept_id in (SELECT b.deptno FROM dept3 b); 
select * from emp3 a WHERE EXISTS(SELECT b.deptno from dept3 b WHERE a.dept_id = b.deptno); -- 查询每个员工时，都看一下这个部门号是否存在于dept3中，比上一句in高效
```

关联查询：自关联自己的主键

```mysql
-- 创建表,并建立自关联约束
create table t_sanguo(
    eid int primary key ,
    ename varchar(20),
    manager_id int,
 foreign key (manager_id) references t_sanguo (eid)  -- 添加自关联约束
);

```



```mysql
-- 查询每个三国人物及他的上级信息，如:  关羽  刘备 ，内连接
SELECT * FROM t_sanguo a join t_sanguo b on a.manager_id = b.eid;
-- 查询所有人物和上级，左外连接，用left join 和on
SELECT a.ename, b.ename FROM t_sanguo a left join t_sanguo b on a.manager_id = b.eid;
```

# mysql函数

聚合函数
数学函数
字符串函数
日期函数
控制流函数
窗口函数

## 聚合函数

聚合查询的函数count,sum,min,max,avg + group_concat()

```mysql
-- 根据部门分组，根据薪资降序排，将每个部门降序排的人的emp_name拼接为一行
-- 顺序不能乱，先是group_concat,order by 后面是separator
SELECT a.department, GROUP_CONCAT(a.emp_name order by a.salary desc SEPARATOR ';')  from emp a group by a.department;
```

## 数学函数

| **函数名**         | **描述**                                                     | **实例**                                                     |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| abs(x)             | 返回 x 的绝对值                                              | 返回 -1 的绝对值：  SELECT  ABS(-1) -- 返回1                 |
| ceil(x)            | 返回大于或等于 x 的最小整数                                  | SELECT  CEIL(1.5) -- 返回2                                   |
| floor(x)           | 返回小于或等于 x 的最大整数                                  | 小于或等于  1.5 的整数：  SELECT  FLOOR(1.5) -- 返回1        |
| greatest(数字列表) | 返回列表中的最大值                                           | 返回以下数字列表中的最大值：  SELECT  GREATEST(3, 12, 34, 8, 25); -- 34  返回以下字符串列表中的最大值：  SELECT  GREATEST("Google", "Runoob", "Apple");  -- Runoob |
| least(数字列表)    | 返回列表中的最小值                                           | 返回以下数字列表中的最小值：  SELECT  LEAST(3, 12, 34, 8, 25); -- 3  返回以下字符串列表中的最小值：  SELECT  LEAST("Google", "Runoob",  "Apple");  -- Apple |
| max(表达式)        | 返回字段 expression 中的最大值                               | 返回数据表  Products 中字段  Price 的最大值：  SELECT  MAX(Price) AS LargestPrice FROM Products; |
| min(表达式)        | 返回字段 expression 中的最小值                               | 返回数据表  Products 中字段  Price 的最小值：  SELECT  MIN(Price) AS MinPrice FROM Products; |
| mod(x,y)           | 返回 x 除以 y 以后的余数                                     | 5 除于 2 的余数：  SELECT  MOD(5,2) -- 1                     |
| PI()               | 返回圆周率(3.141593）                                        | SELECT  PI() --3.141593                                      |
| pow(x,y)           | 返回 x 的 y 次方                                             | 2 的 3 次方：  SELECT  POW(2,3) -- 8                         |
| rand()             | 返回 0 到 1 的随机数                                         | SELECT  RAND() --0.93099315644334                            |
| round(x)           | 返回离 x 最近的整数（遵循四舍五入）                          | SELECT  ROUND(1.23456) --1                                   |
| round(x,y)         | 返回指定位数的小数（遵循四舍五入）                           | SELECT  ROUND(1.23456,3) –1.235                              |
| truncate(x,y)      | 返回数值 x 保留到小数点后 y 位的值（与  ROUND 最大的区别是不会进行四舍五入） | SELECT  TRUNCATE(1.23456,3) -- 1.234                         |

## 字符串函数

| **函数**                                                   | **描述**                                                     | **实例**                                                     |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| char_length(s)  =   character_length(s)                    | 字符数                                                       | 返回字符串  RUNOOB 的字符数  SELECT  CHAR_LENGTH("RUNOOB") AS LengthOfString; |
| **length**                                                 | 字节数                                                       | 更常用                                                       |
| concat(s1,s2...sn)                                         | 合并为一个字符串，无分隔符                                   | 合并多个字符串SELECTCONCAT("SQL ", "Runoob ", "Gooogle ","Facebook") AS ConcatenatedString; |
| **concat_WS(x, s1,s2...sn)**                               | 第一个位置加上个分隔符                                       | 合并多个字符串，并添加分隔符：SELECT CONCAT_WS("-", "SQL", "Tutorial","is", "fun!")AS Concatenated String; |
| field(s,s1,s2...)                                          | 返回第一个字符串 s 在字符串列表(s1,s2...)中的第一个位置      | 返回字符串 c 在列表值中的位置：SELECT FIELD("c", "a", "b", "c","c", "e");  -- 3 |
| ltrim(s),trim(s),rtrim(s)                                  | 去掉字符串 s 左边、前后、右边的空格                          | SELECT trim(' s dds   ');                                    |
| mid(s,n,len) = substring(s,n,len)=<br />substr(s,n,length) | 从字符串 s 的 n 位置截取长度为  len 的子字符串，同  SUBSTRING(s,n,len) | 下标从1开始数 SELECT substring('abcdef',2,3); -- bcd         |
| position(s1in s)                                           | 从字符串 s 中获取 s1 的开始位置                              | SELECT position('c' in 'abcdef'); -- 3                       |
| replace(s,s1,s2)                                           | 将字符串 s2 替代字符串 s 中的字符串 s1 ，不论长度是否相等，全部替换 | 将字符串  abc 中的字符 a 替换为字符 x：  SELECT  REPLACE('abc','a','x') --xbc |
| reverse(s)                                                 | 将字符串s的顺序反过来                                        | 将字符串 abc 的顺序反过来：  SELECT  REVERSE('abc')  -- cba  |
| right(s,n)                                                 | 返回字符串 s 的后 n 个字符                                   | 返回字符串  runoob 的后两个字符：  SELECT  RIGHT('runoob',2) -- ob |
| strcmp(s1,s2)                                              | 比较字符串 s1 和 s2，如果 s1  与 s2  相等返回 0 ，如果  s1>s2 返回 1，如果  s1<s2 返回 -1 | 比较字符串：  SELECT  STRCMP("runoob", "runoob"); -- 0       |
| ucase(s)  = upper(s)                                       | 将字符串转换为大写                                           | 将字符串  runoob 转换为大写：  SELECT  UCASE("runoob"); -- RUNOOB |
| lcase(s) = lower(s)                                        | 将字符串  s  的所有字母变成小写字母                          | 字符串  RUNOOB 转换为小写：  SELECT LCASE('RUNOOB') --  runoob |

## 日期函数



```mysql
select(DATE_FORMAT(now(),'%Y-%M-%D')); -- 2023-April-3rd
select(DATE_FORMAT(now(),'%y-%m-%d')); -- 23-04-03
select(DATE_FORMAT(now(),'%W-%w')); -- 星期大小写，Monday-1
select(DATE_FORMAT(now(),'%j')); -- 一年中的第多少天
select(DATE_FORMAT(now(),'%h-%H-%i-%S')); -- 10-10-49-50
-- %h(0-12) %H(0-23小时) %i(0-59分钟) %s(0-59秒)093-10-47-37

SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW()), HOUR(NOW()), MINUTE(NOW()), SECOND(NOW());
SELECT DATE_FORMAT(NOW(),'%h'); -- 2023
SELECT NOW() as "today", DATE_ADD(NOW(),INTERVAL 1 day) as "add1day", DATE_SUB(NOW(),INTERVAL 1 year);
-- 加一天，减一年
```



## 控制流函数

| 格式                 | 解释                                                         | 案例                                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| IF(expr,v1,v2)       | 如果表达式 expr 成立，返回结果 v1；否则，返回结果 v2。       | SELECT  IF(1 > 0,'正确','错误')    ->正确        |
| IFNULL(v1,v2)        | 如果 v1 的值不为  NULL，则返回 v1，否则返回 v2。             | SELECT  IFNULL(null,'Hello Word')  ->Hello  Word |
| ISNULL(expression)   | 判断表达式是否为 NULL                                        | SELECT  ISNULL(NULL);  ->1                       |
| nullif(expr1, expr2) | 比较两个字符串，如果字符串  expr1 与  expr2 相等  返回  NULL，否则返回  expr1 | SELECT  NULLIF(25, 25);  ->                      |

if  case then逻辑语句

```mysql
select *, if (score > 85, '优秀', '及格') flag from score; -- 给score表追加一列flag，表示score列的值是否大于85

select *, case payType when 1 then '微信支付',
						when 2 then '支付宝支付',
						when 3 then '银行卡支付',
						else '其他支付方式'
			end as payStr from tablePay; -- 给tablePay加了一列
```

## 窗口函数，开窗函数

聚合函数：对一组数据计算后返回单个值（即分组）

窗口函数：在行记录上计算某个字段的结果时，可将窗口范围内的数据输入到聚合函数中，并不改变行数。

```
window_function ( expr ) OVER ( 
  PARTITION BY ... -- 将数据行拆分成多个分区（组），类似于group by
  ORDER BY ...  -- 指定排序方式，类似于order by
  frame_clause  -- 窗口大小，在当前分区内指定一个计算窗口，也就是一个与当前行相关的数据子集
)
```

开窗聚合函数：sum,avg,min,max具有两者的特点

![image-20230401140932009](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230401140932009.png)

### 序号函数

```mysql
row_number() / rank() / dense_rank() over(
partition by
order by
)
无参数
```

```mysql
-- 1.对每个部门的员工按照薪资排序，并给出排名 
select 
dname,
ename,
salary,
row_number() over(partition by dname order by salary desc) as rn1, -- row_number对于相等的排名递增 1 2 3
rank() over(partition by dname order by salary desc) as rn2, -- rank对于相等的排名相等，后一个递增 1 1 3
dense_rank() over(partition by dname order by salary desc) as rn3 
-- dense_rank对于相等的排名相等，后一个增1： 1 1 2
from employee;

-- 2.排名topn问题，要用子查询，包裹起来，给这个表起个名字叫做t
select * from (
SELECT
dname, ename,salary,
row_number() over (partition by dname order by salary desc) as rn1
FROM employee
) as t where t.rn1 <= 3;
```

### 开窗聚合函数

sum,avg,min,max 有一个参数

四种的用法类似，都需要加上对哪一列进行操作，记住rows between and preceding following current row关键字

```mysql
select *,
avg(salary) over (
partition by dname -- 分组
order by eid -- 如果不排序，则没有累加效果，表示从之前所有行的累加和/均值/最大值/最小值
rows between ... and ...
)
as rn1 from employee;
-- between and可以填写
between unbounded preceding and current row -- 默认的，从之前的到当前行
between 3 preceding and 1 following -- 表示对当前行前的三行，后的一行（最多情况下共5行）
between current row and unbounded following -- 从当前行到最后一行
```

### 分布函数

cume_dist 和percent_rank 无参数

```mysql
select *,
cume_dist() over (order by salary) as rn0, -- 总共一组，该组内薪资<=本行的数量 / 该组总行数
cume_dist() over (partition by dname order by salary) as rn1 
from employee;

select *,
rank() over (PARTITION by dname order by salary) as rn0, -- 1 2 2 4这种
PERCENT_RANK() over (partition by dname order by salary) as rn1 
-- (rank - 1) / (本组的row - 1)
from employee;
```

### 前后函数

lag 和lead 有2-3个参数

```mysql
select *,
lag(hiredate,1,'2000-01-01') over (PARTITION by dname order by hiredate) as time1,
-- 对于每一组的hiredate列，将上1行的值放过来，如果没有上1行，则有默认值
lag(hiredate,2) over (PARTITION by dname order by hiredate) as time2
-- 对于每一组，将上2行的值放过来，如果没有上2行（第1行和第2行都没有上2行），则null
from employee;
```

### 头尾函数

first_value和last_value 有一个参数

```mysql
select *,
first_value(salary) over(PARTITION by dname order by hiredate) as rn1,
-- 本组内，通过hiredate排序的到目前为止的第一个salary值
last_value(salary) over(PARTITION by dname order by salary) as rn2
-- 本组内，通过salary排序的最后一个salary值，也就是本行值
from employee;
```

### 其他窗口函数

nth_value(salary,n)  求排名第n的值，nth(salary,1) == first_value(salary)

ntile(n) 将数据均分为n组，返回组别1-n

```mysql
select *,
nth_value(salary,2) over(PARTITION by dname order by hiredate) as rn1,
-- 本组内，到目前为止的hiredate排名第二的salary
nth_value(salary,3) over(PARTITION by dname order by hiredate) as rn2
-- 本组内，到目前为止的本行值
from employee;

select *,
ntile(4) over(PARTITION by dname order by hiredate) as rn1
-- 本组内，分成4组，如果除不尽，余数给前面的组。例如6条数据四组（2，2，1，1）
from employee;
```

# 视图

虚拟表，只是记录原数据的位置（快捷方式和映射），找数据的时候还是从原表里面找。视图中的数据是依赖于原来的表中的数据的。一旦表中的数据发生改变，显示在视图中的数据也会发生改变。

数据库中只存放了视图的定义，而并没有存放视图中的数据。这些数据存放在原来的表中

作用：简化代码，简化sql语句；更安全，对不同的用户可以设定不同视图（列）

**create [or replace]** **view** view_name **as** select语句

[with [cascaded | local] check option] -- 权限设置

参数说明：
（1）algorithm：可选项，表示视图选择的算法。
（2）view_name ：表示要创建的视图名称。
（3）column_list：可选项，指定视图中各个属性的名词，默认情况下与SELECT语句中的查询的属性相同。
（4）select_statement
：表示一个完整的查询语句，将查询记录导入视图中。
（5）[with [cascaded | local] check option]：可选项，表示更新视图时要保证在该视图的权限范围之内

```mysql
-- 创建视图，一定有as，后面是个查询语句
create or replace view view_emp
as
select ename, job from emp;

-- 修改视图，修改视图定义
alter view view_emp
as 
select a.deptno,a.dname,a.loc,b.ename,b.sal from dept a, emp b where a.deptno = b.deptno;

-- 更新视图，很多情况都不能更新
聚合函数，distinct，group by having,子查询，join，union都不能更新

-- 重命名视图
rename table view_emp to new_view;

-- 删除视图
drop view if exists view_emp;
```

# sql存储过程

封装一组SQL语句集，实现代码的封装和复用。

## 变量定义

局部变量，在begin end中起作用

用户变量，范围广，会话内起作用

系统变量——全局变量，MYSQL启动的时候由服务器自动将它们初始化为默认值，my.ini，存放端口号设置、存储殷勤类别

系统变量——会话变量，建立连接时mysql初始化，会话变量时全局变量的拷贝

用set语句修改，但是有些系统变量是只读的

select into赋值

```mysql
-- 局部变量，需要声明名称和类型
drop PROCEDURE if EXISTS proc02; -- 清空procedure
delimiter $$ -- 开始
create procedure proc02()
begin
declare var_name01 VARCHAR(20) DEFAULT 'aaa'; -- 局部变量，需要声明名称和类型
set var_name01 = 'zhangsan';
select ename into var_name01 from emp where empno = 1001; -- select into赋值
SELECT var_name01;
end $$ -- 结束
delimiter ; -- 恢复;
call proc02();

-- 用户变量，不需要声明，使用即声明
drop PROCEDURE proc04;
delimiter $$
create procedure proc04()
begin
set @var_name01 = 'bj'; -- 一定要有分号
end $$
delimiter ;
call proc04;
select @var_name01; -- 使用变量

-- 全局变量
-- 查看全部全局变量，用show
show global variables;
-- 查看某一个，用select
select @@global.auto_increment_increment;
-- 修改用set
set global general_log = 1; -- 开启查询日志 
select @@global.sort_buffer_size;
```



in 输入参数，in inname demical(7,2)

out 输出参数:该值可在存储过程内部被改变，并向外输出

inout 输入输出参数，既能输入一个值又能传出来一个值)

```mysql
-- 封装有参数的存储过程，传入员工编号，返回员工名字和薪资
drop PROCEDURE if EXISTS proc04;
delimiter $$
create PROCEDURE proc04(in empno int, out outname varchar(20), out outsalary decimal(7,2) )
begin
	SELECT a.ename, a.sal into outname, outsalary from emp a where a.empno = empno;
	-- select into多个用逗号
end $$
delimiter ;

call proc04(1001, @outname, @outsalary);
SELECT @outname tt1, @outsalary tt2; -- 必须用逗号而不是and
```



## 流程控制 

条件判断if case

循环 while repeat loop  (leave跳出 iterate跳出本次)

```mysql
-- 1.根据员工empno判断薪资类型 if流程：
/*
	if ... then
	elseif ... then
	else (没有then)
	endif; (有分号)
*/
drop PROCEDURE if EXISTS procif_02;
delimiter $$
CREATE PROCEDURE procif_02(in empno int, out outcondition varchar(20))
begin
	DECLARE temp DECIMAL(8,2) default 0;
	select a.sal into temp from emp a where a.empno = empno; 
	if temp < 10000 
		then set outcondition = '试用薪资';
	elseif temp <30000 
		then set outcondition = '转正薪资';
	else 
		set outcondition = '错误薪资';
	end if;
end $$
delimiter ;

call procif_02(1002,@out);
select @out tt;
```



```mysql
-- 2.循环
/*
2.1while ... do
end while
2.2[标签:]repeat 
 循环体;
until 条件表达式
end repeat [标签];
2.3[标签:] loop
  循环体;
  if 条件表达式 then 
     leave [标签]; 
  end if;
end loop;
*/
create table if not EXISTS user (
    uid int primary key,
    username varchar ( 50 ),
    password varchar ( 50 )
);
-- 向表中添加n条数据
drop PROCEDURE if EXISTS procwhile01;
delimiter $$
create PROCEDURE procwhile01(in n int)
begin
	DECLARE i int DEFAULT 1;
	label:while i <=n do
				if i = 8 then set i = i + 1; -- 跳过8
				ITERATE label;
				end if; -- if 结束
				insert into user values(i, concat_WS('-','user',i), '123456');
				if i = 10 then leave label; -- 到10截止，跳出while
				end if; -- if结束
				set i = i +1; -- i++的操作
	end while label; -- 标签为label的while循环结束
end $$
delimiter ;

call procwhile01(100);
select * FROM user;
truncate table user;
```

## 游标，句柄

在语法中，变量声明、游标声明、handler声明是必须按照先后顺序书写的，否则创建存储过程出错。

handler异常处理，

```mysql
-- 游标，迭代器，一行一行
use mydb6;
drop PROCEDURE if EXISTS proc20_cursor;
delimiter $$
create procedure proc20_cursor(in in_dname varchar(50))
begin
 -- 定义局部变量
 declare var_empno varchar(50);
 declare var_ename varchar(50);
 declare var_sal  decimal(7,2);
 -- 句柄需要的变量
 declare flag int default 1; 
 
 -- 声明游标
 declare my_cursor cursor for
  select empno , ename, sal 
    from  dept a ,emp b
    where a.deptno = b.deptno and a.dname = in_dname;
	-- 声明句柄handle，当无数据时1329设置为0
	declare continue handler for 1329 set flag = 0;   
    
    -- 打开游标
  open my_cursor;
  -- loop死循环，但是外面有游标，会自己跳出的，不用在里面写leave
  label:loop
		fetch my_cursor into var_empno, var_ename, var_sal;
		if flag = 1 then
			 select var_empno, var_ename, var_sal;
		else leave label;
		end if;
  end loop label;
    -- 关闭游标
  close my_cursor;
end $$
delimiter ;
 
 -- 调用存储过程
 call proc20_cursor('销售部');
```

# 触发器

触发器是一种特殊的存储过程，多表之间的关联操作，执行A表的insert update delete时候，与之关联的B表自动执行触发器规定的操作。确保数据的完整性 , 日志记录 , 数据校验等操作 。

使用别名 OLD 和 NEW 来引用触发器。mysql只支持**行级触发**，不支持语句级触发。

触发器的特性：

1、什么条件会触发：I、D、U

2、什么时候触发：在增删改前或者后

3、触发频率：针对每一行执行

4、触发器定义在表上，附着在表上

触发器的注意事项：

 MYSQL中触发器中不能对本表进行操作

2.尽量少使用触发器，假设触发器触发每次执行1s，insert table 500条数据，那么就需要触发500次触发器，光是触发器执行的时间就花费了500s，而insert 500条数据一共是1s，那么这个insert的效率就非常低了。

触发器是针对每一行的；对增删改非常频繁的表上**切记不要使用触发器**，因为它会**非常消耗资源**。 

| **触发器类型**   | **触发器类型NEW  和 OLD的使用**                            |
| ---------------- | ---------------------------------------------------------- |
| INSERT  型触发器 | NEW  表示将要before或者已经新增after的数据  ，没有od       |
| UPDATE  型触发器 | OLD  表示修改之前的数据  ,  NEW 表示将要或已经修改后的数据 |
| DELETE  型触发器 | OLD  表示将要before或者已经删除after的数据  ，没有new      |

格式：

```mysql
create trigger nameTriger before|after  insert | delete | update 
on 表名 for each row
begin
     执行语句列表 -- 对于日志表，执行记录操作
end;

drop trigger if EXISTS trigger1;
show triggers; -- 查看所有触发器
delimiter $$ -- 这里面用begin end就需要delimiter
-- 在更新user表之后，自动执行user_logs表的操作
create TRIGGER trigger1 after update on user for each row
begin
INSERT into user_logs values(null,now(),CONCAT_WS(',','修改前信息为',OLD.uid, OLD.username,OLD.password));
-- OLD记录更新前的信息，NEW记录更新后的信息，before或者after都可以，不过insert只有new，delete只有old
INSERT into user_logs values(null,now(),CONCAT_WS(',','修改后信息为',NEW.uid, NEW.username,NEW.password));
end $$
delimiter ;

-- 测试一下
update user set password = 'newpass' where uid =  4;
SELECT * from user_logs;
```

# 索引

让查询更快速，而不是一条一条来找

索引的结构组织要尽量减少查找过程中磁盘I/O的存取次数

Hash索引 B+Tree索引

## 索引类型

| 索引分类 | 描述                                                         | 说明                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 单列索引 | 普通没有限制，index index_name(name)默认normal b+tree<br />唯一，要求这一列的值不能重复，可以为空<br />主键，只要是主键，默认创建，唯一非空 | 一个表中可以有多个单列索引                                   |
| 组合索引 | 和前面的一样，只是()里可以有多列                             |                                                              |
| 全文索引 | fulltext                                                     | 大数据下全文索引比 like + % 快 N 倍，但精度有问题；<br />5.6以后的MyIsam 和InnoDB均支持全文索引；<br />只有字段为char varchar text才能全文索引；<br />默认搜索长度区间[3,84]，只有这个区间内的内容会启用全局索引 |
| 空间索引 | spatial，5.7之后的版本支持了空间索引，支持OpenGIS几何数据模型，线点多边形 |                                                              |
| 前缀索引 | 对文本或者字符串的前几个字符建立索引                         | 索引的长度更短，查询速度更快                                 |

```mysql
1.单列索引 唯一索引
-- 创建索引，使用on，索引名需要是唯一的
index | unique | fulltext index_gender(gender) -- 创建时，普通关键字index，唯一用unique
create [unique | fulltext] index index_gender on student(gender); 
alter table student add index | unique index_age(age);
-- 查看表索引，使用from
show index from student;
-- 删除索引，不用加unique
drop index index_gender on student;
alter table student drop index index_gender;
2.联合索引 最左匹配原则，顺序颠倒没关系，有一层sql优化，先写phone_num或name在mysql眼中等价
create  unique index index_phone_name on student(phone_num,name); 
index index_lianhe(name, gender)
3.全文索引
create fulltext index index_content on t_article(content);
-- 使用全文索引需要match和against两个关键字，
select * from t_article where match(content) against('you'); -- 必须>=3个字符才能生效
select * from t_article where content like '%you%'; -- 效率低
```

## 索引创建原则

适合索引的列是出现在where、order by、join子句中的列
数据量>300的库,中大型表
查询频繁，但更新不频繁
主键和外键的数据列一定要建立索引
索引字段越小越好，尽量使用短索引
重复数据不多的字段（重复数据<15%）

在进行联合索引创建时，要遵循**最左前缀匹配原则**，即"带头大哥不能死，中间兄弟不能断"
**索引下推**（Index Condition Pushdown）： 在非聚簇索引遍历过程中，对索引中包含的字段先做判断，过滤掉不符合条件的记录，减少回表次数

百万数据如何删除：

1. 先删除索引（3min）

2. 删除其中无用数据（<2min）

3. 重新创建索引（数据少了，创建索引很快）




优缺点：加快检索速度，随机IO变为顺序IO，加速表之间的连接

但需要时间区创建维护，需要空间存储

## 索引算法

| 索引算法 | 描述                                                         | 效率    | 范围/区间查询 | 顺序检索 | IO次数     |
| -------- | ------------------------------------------------------------ | ------- | ------------- | -------- | ---------- |
| B+树     | 中间节点**存的是索引，不存储数据**，数据都保存在叶子节点中<br />平衡**多叉**搜索树 | O(logn) | √             | √        | 少，且稳定 |
| B树      | 所有节点都可以存储数据                                       | O(logn) | ×             | √        | 多，不稳定 |
| 红黑树   | 平衡的二叉搜索树，中序遍历得到有序序列。**层数**容易过高     | O(logn) | ×             | √        | 比B+高很多 |
| 跳表     | 多层链表，二分查找的链表结构                                 | O(logn) | √             | √        | 比B+高     |
| Hash索引 | 如果只查询单个值的话，hash 索引的效率非常高                  | O(1)    | ×             | ×        | 最少       |



B+树：B+树的中间节点**存的是索引，不存储数据**，数据都保存在叶子节点中，而且数据是按照顺序排列的，因此范围查找、排序查找、分组查找都很简单。单一节点存储更多的元素，使得查询的**IO次数更少**；所有查询都要查找到叶子节点，查询性能稳定；所有叶子节点形成有序链表，便于**范围查询**，顺序检索

B+树不是二叉树，是个平衡搜索树

Hash索引(散列表)：如果只查询单个值的话，hash 索引的效率非常高，时间复杂度为O(1)。但是 hash 索引有几个问题：1）**不支持范围查询**；2）不支持索引值的排序操作；3）不支持联合索引的最左匹配规则。

平衡二叉树（红黑树）：查询性能也好，时间复杂度O(logn)，中序遍历可以得到一个从小到大有序的数据序列，但**不支持区间查找**。而且由于是二叉树，当数据量很大时树的层数就会很高，从树的根结点向下寻找的过程，每读1个节点，都相当于一次IO操作，因此他的**I/O操作会比B+树多的多**。

B树索引：不支持范围查询，所有节点都可以存储数据，IO次数高。查询不够稳定。

跳表：是一种链表加多层索引的结构，时间复杂度O(logn)，**支持区间查找**，而B+树是一种多叉树，可以让每个节点大小等于操作系统每次读取页的大小，从而使读取节点时只需要进行一次IO即可。而且同数量级的数据，跳表索引的高度会比 B+ 树的高，导致 **IO 读取次数多**，影响查询性能。

## 聚簇索引 VS 非聚簇索引:star:

|               | 定义                                                         | 举例                | 优点                                                         | 缺点                                               |
| ------------- | ------------------------------------------------------------ | ------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 聚簇/聚集     | 索引结构和数据一起存放的索引                                 | InnoDB 中的主键索引 | 查询速度非常快（找到索引就找到了数据，少一次IO）<br />有序查找和范围查找速度很快 | 依赖于有序的数据；更新代价大（数据改了索引就得改） |
| 非聚簇        | 索引结构和数据分开存放的索引，叶子节点放指向数据的指针或数据的主键 | 辅助、MyISAM 的索引 | 更新代价比聚簇索引要小                                       | 依赖于有序的数据；<br />可能会二次查询(回表)       |
| 辅助/二级索引 | 叶子节点存储的数据是主键，定位主键的位置，**回表**查数据     | 唯一/普通/前缀      |                                                              |                                                    |

​		B+树索引分为：主索引（聚簇索引）主键索引B+树，叶子节点存储完整的数据记录

​		辅助索引（非聚簇索引）叶子节点存储主键，需要找到主键后去主索引找数据

数据和索引是否分开存储：

InnoDB 中 数据 的存储方式就是采用聚簇索引 的方式 (数据 和 索引 在一起的形式)。 而**MyISAM采用的是 数据 和 索引分开 存储的方式**。 查询时先查到 索引,然后再通过一个叫回表的方式查找 数据 。

# 存储引擎

MVCC，即**Multi-Version Concurrency Control （多版本并发控制）**。在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存。数据库隔离级别读**已提交、可重复读** 都是基于MVCC实现。

## mysql引擎

**mysql的核心是存储引擎**

show engines;查看

5.6以后的MyIsam 和InnoDB均支持全文索引

只有innoDB支持行锁，MYISAM和memory只支持到表锁

![在这里插入图片描述](https://img-blog.csdnimg.cn/40465d3722be473db19b5443a6f4eb31.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBASUNoZW4u,size_20,color_FFFFFF,t_70,g_se,x_16)

MySQL常用的引擎有:ISAM,MyISAM,HEAP,InnoDB和Berkley(BDB).

|                                          | 优点                                                         | 缺点                                 |
| ---------------------------------------- | ------------------------------------------------------------ | ------------------------------------ |
| ISAM                                     | 读取操作的速度很快，而且不占用大量的[内存](https://so.csdn.net/so/search?q=内存&spm=1001.2101.3001.7020)和存储资源 | 不支持事务，不支持外键，不容错       |
| MyISAM5.5前默认存储引擎b+tree            | 提供了全文索引和数据压缩的功能，使用表格锁定机制，并发读写，表锁机制 | 有存储限制，不支持事务，不支持外键   |
| MERGE                                    | 一组MyISAM的组合，对MERGE的操作其实是对MyISAM的操作，和MyISAM的区别在于不支持全文索引和压缩 |                                      |
| HEAP（Memory）                           | 没有存储限制，驻留内存很快，表锁机制                         | 管理数据不稳定，关机前不保存数据丢失 |
| Inno DB BDB（5.5以上默认搜索引擎）b+tree | 支持**事务、外键、MVCC、行锁**、数据库异常崩溃后的安全恢复   | 速度慢                               |

## mysql事务

事务是一个**不可分割的数据库操作序列**，是数据库并发控制的基本单位，要么都执行，要么都不执行。

1. 特性：**用AID保证C**，数据一致性是目的

原子性（Atomicity）：事务是最小的执行单位，不允许分割，要么全部执行，要么完全不执行
一致性（Consistency）：指一个事务执行前和执行后数据库必须处于一致性状态
隔离性（Isolation）：并发的事务是相互隔离的。
持久性（Durability）：事务对数据库中数据的改变是持久的。

2. 事务操作：

开启事务：**Start** Transaction

任何一条DML语句(insert、update、delete)执行，标志事务的开启
命令：BEGIN 或 START TRANSACTION

提交事务：**Commit** Transaction
成功的结束，将所有的DML语句操作历史记录和底层硬盘数据来一次同步
命令：COMMIT

回滚事务：**Rollback** Transaction
失败的结束，将所有的DML语句操作历史记录全部清空
命令：ROLLBACK 



脏读(Dirty Read)：两个事务，一个改动未提交，另一个来读发生脏读
不可重复读（Non-repeatable read，修改数据）：一个事务，两次查询中数据不一致，可能是两次查询过程中间插入了一个事务更新的原有的数据。
幻读（Phantom Read，新增删除）：一个事务，两次查询中数据记录数不一致。可能是两次查询过程中插入了数据。



事务隔离级别：

| 隔离级别                                                     | 脏读 | 不可重复读 | 幻读 |
| ------------------------------------------------------------ | ---- | ---------- | ---- |
| read-uncommitted（读未提交，不需要锁）                       | √    | √          | √    |
| read-committed（读已提交，加共享锁）Oracle neo4j 默认        | ×    | √          | √    |
| repeatable-read（可重复读，加共享锁）<font color='red'>**MySQlL默认采用**</font><font color='cornflowerblue'></font> | ×    | ×          | √    |
| serializable（可串行化，**锁定整个范围的键**）               | ×    | ×          | ×    |

read-uncommitted（读未提交，不需要锁）：允许读尚未提交的数据。可能导致脏读、不可重复读和幻读。
read-committed（读已提交，加共享锁）：允许读取并发事务已经提交的数据，可以阻止脏读，但无法阻止幻读和不可重复读。
**repeatable-read（可重复读，加共享锁）**：对同一字段多次读取的结果是一致的。可以组织脏读和不可重复读，无法阻止幻读。
serializable（可串行化，**锁定整个范围的键**）：最高的隔离级别，完全服从ACID的隔离级别，所有事务依次逐个执行，事务之间完全不可能产生干扰。防止幻读、不可重复读和脏读
**Mysql默认采用可重复读隔离级别**

## 锁机制

当数据库有并发事务时，会产生问题，需要锁。

### 分类

锁粒度：

行锁innoDB：粒度最细，表示只针对当前行操作加锁，冲突少，并发性强，但开销大，会出现死锁。分为共享锁和排它锁。

表锁MyISAM：MySQL中锁定粒度最大的锁，冲突多，并发度低，开销少，不会死锁。分为读锁写锁。

页级锁：锁定粒度介于行级锁和表级锁中间的一种锁，表级锁速度快，但冲突多，行级锁冲突少，但速度慢，因此采取了折中的页集。

**InnoDB的行锁是针对索引加的锁，不是针对记录加的锁。**



锁类别：

读锁（共享锁）：会阻塞写，不阻塞读

写锁（排它锁）：会阻断读也阻塞写

MyISAM：

读锁/写锁：lock table student read | write

在执行查询语句（SELECT）前，MyISAM会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT 等）前，会自动给涉及的表加写锁，这个过程并不需要用户干预，因此，用户一般不需要直接用 LOCK TABLE 命令给 MyISAM 表显式加锁。

**MyISAM写优先**，这也是MyISAM不适合做写为主的表的存储引擎的原因。因为写锁后，其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞。

InnoDB：

共享锁（S）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE 
排他锁（X) ：SELECT * FROM table_name WHERE ... FOR UPDATE

对于UPDATE、DELETE和INSERT语句，InnoDB会自动给涉及数据集加排他锁（X)；普通SELECT语句，InnoDB不会加任何锁；



乐观锁和悲观锁：

悲观锁：假设发生并发冲突，**屏蔽一切可能违反数据库完整性的操作**，直到提交事务。

乐观锁：假设不会发生并发冲突，**只在提交操作时检查是否违反数据完整性**。一般使用版本号机制或CAS算法实现。

适用场景：乐观锁适用于写比较少的情况下，反之悲观锁

### 死锁及解决

概念：两个或多个事务在同一资源上相互占用，并请求锁定对方资源。

约定以相同顺序对表操作
做到一次锁定所有需要的资源
升级锁粒度。

## 日志



| 日志类型   | 定义                                                         | 其他     |
| ---------- | ------------------------------------------------------------ | -------- |
| 错误日志   | 错误信息，数据库出现任何故障导致无法正常使用时，可以首先查看此日志 |          |
| 二进制日志 | 数据恢复，记录了DDL和 DML，不记录DQL<br />主从复制           | 日志格式 |
| 查询日志   | DQL，默认未开启                                              |          |
| 慢查询     | 用于排查哪些sql耗时，查询时间超过某个值的sql记录下来         |          |

错误日志
二进制日志
查询日志
慢查询日志

```mysql
-- 查看日志位置
show variables like 'log_error%';
-- 查看MySQL是否开启了binlog日志/查询日志
show variables like 'log_bin' | 'general_log';
-- 查看binlog日志的格式
show variables like 'binlog_format';
-- 查看所有日志
show binlog events;
-- 查看最新的日志
show master status;
```

# sql优化

从设计上优化
从查询上优化
从索引上优化
从存储上优化

```mysql
-- 查看当前会话 / 全局的操作类型执行次数（Com_insert,Com_select这些）
show session | global status like 'Com_______';

-- 查看慢日志配置信息
show variables like 'slow_query_log%';
-- 开启慢日志查询
set global slow_query_log = 1;
-- 查看慢日志阈值，默认>=10s的操作记录到慢日志
show variables like 'long_query_time%';

-- 通过show processlist查看执行线程的动态效果，定位低效率执行SQL，
show processlist;

```

## explain

可以连接任意一条select

```mysql
-- 1.id相同表示加载表的顺序从上到下u->ur->r
EXPLAIN select * from user u join user_role ur join role r on u.uid = ur.uid and ur.rid = r.rid;
-- id不同的（select嵌套），先执行id最大的（最内层select）
```

![image-20230402160016094](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230402160016094.png)

2. **select type**
   simple:没有子查询和union
   primary(主查询，最外层的selelct)
   subQuery（被嵌套一层两层...的查询）
   derived：衍生表，也就是from (select...) 这个from后面的查询：EXPLAIN select * from(select * from user limit 2)t;
   union & union result：包含union的查询
3. **type**表示访问类型
   null：不访问任何表和索引，比如直接select now()
   system：查询系统表，从内存读取，5.7以上合并显示all
   const：**主键索引或者唯一索引的列的查询**
   eq_ref：**join时左表有主键**，对于左表的每一行，都与右表刚好匹配
   ref：**join时左表非唯一索引**，左表普通索引，和右表可能匹配多行
   range：范围查询
   index：把索引列的全部数据都扫描 select id from user;
   all：可能需要查询所有的表数据，效果最差
4. table 表的名字（别名）
5. **rows**需要扫描的行数
6. key：实际使用的索引
7. key_len : 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下， 长度越短越好 
8. **extra**

![image-20230402163944263](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230402163944263.png)

<font color='red'>**system > const > eq_ref > ref > range > index > all，优化尽量在range及之前**</font>

## show profile

show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。。

```mysql
-- 查看是否支持profile
select @@have_profiling; 
set profiling=1; -- 开启profiling 开关； 
-- 查看全部的查询语句的执行时间
show profiles;
-- 查看单个的时间消耗，需要通过上一句找到query id
show profile for query 20;
-- 查看cpu消耗
show profile cpu for query 20;
```

## track优化器

通过trace文件能够进一步了解为什么优化器选择A计划, 而不是选择B计划

## 索引优化

**联合索引最左匹配原则**：从最左边为起点开始连续匹配，遇到范围查询（<、>、between、like）会停止匹配。

比如（a,b,c）三个列的联合索引，B+树实现，a先有序，a相等时b有序，b相等时c有序，因此只有确定左边的值，右边的索引才能生效。

```mysql
select * from t where b=1 and c=1;     #这样不可以利用到定义的索引（a,b,c）
select * from t where a=1 and b >1 and c=1;  # 可以用到（a,b,c），ab两列可以用到，c索引用不到
select * from t where b=1 and a=1;     #这样可以利用到定义的索引（a,b,c），有优化器

-- 4.尽量使用覆盖索引，避免select *
explain select * from tb_seller where name='小米科技'  and address='北京市';  -- 需要从原表及磁盘上读取数据，效率低
explain select name,status,address from tb_seller where name='小米科技'  and address='北京市';  -- 效率高，从索引树中就可以查询到所有数据
-- like模糊查询，
explain select name from tb_seller where name like '%科技%'; -- 用到索引,type = index
explain select * from tb_seller where name like '%科技'; -- select * %开头 失效，all
explain select * from tb_seller where name like '科技%'; -- select * 不是%开头，有效，range
```

**索引失效**：

范围查询右边的列失效
索引列上进行运算操作（!=,substring）失效
or语句失效，含有or语句的，无论是不是有索引，哪怕遵循最左匹配，也不会用到任何索引
like开头的语句失效，%结尾的用到了索引进行范围查询。
字符串不加单引号失效
避免使用select *
索引字段越小越好，尽量使用短索引
重复数据不多的字段（重复数据<15%）

**其他原则：**

1、如果MySQL评估使用索引比全表更慢，则不使用索引。 这种情况是**由数据本身的特点**来决定的（重复数据过多）

```mysql
create index index_address on tb_seller(address); 
explain select * from tb_seller where address = '北京市';  -- 不用索引，北京市重复的太多了，all
explain select * from tb_seller where address = '西安市';  -- 生效，西安市就一个，index

```

2、is NULL ， is NOT NULL 有时有效，有时索引失效。null少的时候is null有效(重复的少)，而is not null索引无效（非null太多了）

3、in走索引，not in不走索引（in重复的少）

4、单列索引和复合索引，**尽量使用复合索引**

如果一张表多个单列索引，即使where都使用了，也只有一个最优的索引生效，由优化器决定使用哪一个(重复少的)

```mysql
explain select * from tb_seller where name='小米科技' and status = '1' and address='北京市'; -- name,status,address三列都有单列索引，但只是用name,这是数据决定的，重复的最少
```

5.限制每张表上的索引数量，<5个

6.**索引下推**（Index Condition Pushdown）： 在非聚簇索引遍历过程中，对索引中包含的字段先做判断，过滤掉不符合条件的记录，减少回表次数

## 大批量插入数据的优化方法

使用load 命令导入数据的时候，innoDB下可以采用如下方式提高效率：

**主键顺序插入**：InnoDB类型的表是按照主键的顺序保存的，所以将导入的数据按照主键的顺序排列，会更快（主键有序100w行13s，无序时220s，很慢）

**关闭唯一性校验**：SET UNIQUE_CHECKS=0，可以提高效率

**insert优化**：每次insert都会建立连接，然后关闭连接，所以可以合并为一条语句进行insert

​					插入时候尽量有序

## 其他语句优化

### order by优化

1.两种排序方式：Extra里面，Using filesort不是通过索引，using index通过索引

select的字段是索引字段的速度会快，避免select *，避免使用filesort
order by后边的多个排序字段要求尽量同升同降
order by后边的多个排序字段顺序尽量和组合索引字段顺序一致

2.filesort优化：

MySQL 通过比较系统变量 max_length_for_sort_data 的大小和Query语句取出的字段总大小， 来判定是否那种排序算法，如果max_length_for_sort_data 更大，那么使用一次扫描算法；否则使用两次扫描（有磁盘IO）。可以适当提高 sort_buffer_size（缓存大小）  和 max_length_for_sort_data（缓存排序数据更多）  系统变量，来增大排序占内存的大小，提高排序的效率。

### 子查询优化

多表join查询优于子查询

### group by优化

执行order by null 禁止排序：GROUP BY 实际上也同样会进行排序操作，而且与ORDER BY 相比，GROUP BY 主要只是多了排序之后的分组操作，可以用order by null 避免排序结果的消耗

explain select age,count(*) from emp group by age order by null;

### 优化limit查询：

就是 limit 90000,10  排序90010条只取最后10条，很消耗

```mysql
create index idx_emp_age_salary on emp(age,salary); -- 有age和salary联合索引，主键id索引
-- 1.select的字段是索引字段的速度会快，select * 速度慢
explain select * from emp order by age;        -- Using filesort
explain select * from emp order by age,salary; -- Using filesort

explain select id from emp order by age;  -- Using index
explain select id,age from emp order by age;  -- Using index
explain select id,age,salary,name from emp order by age;  -- name不是索引字段， Using filesort

-- 2.order by后边的多个排序字段要求尽量排序方式相同，都是asc或者desc
explain select id,age from emp order by age asc, salary desc;  -- Using index; Using filesort
explain select id,age from emp order by age desc, salary desc;  -- Backward index scan; Using index

-- 3.order by后边的多个排序字段顺序尽量和组合索引字段顺序一致
explain select id,age from emp order by salary,age; -- Using index; Using filesort

-- 5.limit查询优化
-- 在索引上完成排序分页操作，最后根据主键关联回原表查询所需要的其他列内容
SELECT * from tb_user LIMIT 900000,10; -- 0.423s
select * from tb_user a join (SELECT b.id from tb_user b limit 900000,10) t on a.id = t.id; -- 0.102s
-- 该方案适用于主键自增的表，可以把Limit 查询转换成某个位置的查询 。
select * from tb_user WHERE id > 900000 limit 10; -- 0.022s
```



# leetcode题目

```mysql
-- 182. 查找重复的电子邮箱id email，返回邮箱名
select email from Person group by email having count(email) > 1;
-- 1050. 合作过至少三次的演员和导演，group by可以根据多个列进行分组
select actor_id, director_id 
from ActorDirector group by actor_id, director_id having count(actor_id) >=3;
-- 2.183. 从不订购的客户 某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户
-- 左外连接，不是内连接
select a.name Customers from Customers a where a.id not in (select CustomerId from Orders); 
select c.name as Customers from Customers c left join Orders o on c.id = o.CustomerId where o.id is null;
select a.name Customers from Customers a where not exists (select b.id from Orders b where a.id = b.CustomerId );
-- 1667.修复表中的名字，left是取左，right取右
select user_id  ,
 concat(
   left(upper(name),1), right(lower(name),length(name) - 1)) as name 
   from Users order by user_id;
-- 180.连续出现的数字，需要找到连续出现>=3次的数字
-- 为了防止id不连续，需要重构表格，使用row_number窗口函数重构3个表格，子查询嵌套
select distinct l1.num as ConsecutiveNums 
from (select num,row_number() over (order by id) as newid from logs) as l1,
 (select num,row_number() over (order by id) as newid from logs) as l2,
  (select num,row_number() over (order by id) as newid from logs) as l3
where l1.newid = l2.newid -1 and l2.newid = l3.newid - 1 and l1.num = l2.num and l2.num = l3.num;
-- 使用rows between and窗口函数，当均值，最大，最小相等时，说明出现了目标
select distinct t.ConsecutiveNums from (
select
  num as ConsecutiveNums,
  sum(num) over(rows between 1 preceding and 1 following) as sumCol, -- 三行的和
  min(num) over(rows between 1 preceding and 1 following) as minCol, -- 三行最小值
  max(num) over(rows between 1 preceding and 1 following) as maxCol -- 三行最大值
 from Logs
) as t where t.sumCol / 3 = t.minCol and t.minCol = t.maxCol; -- 为了防止首尾不够三行，所以有/3操作

-- 610.判断是否是三角形，新增一列
select x,y,z,
case
	when x+y>z and y+z>x and x+z>y  then 'Yes'
	else 'No'
end as triangle from Triangle;

-- 第二高的薪水，提防null值
-- 用ifnull(v1,v2)
select ifnull((select distinct salary from Employee order by salary desc limit 1,1) 
              , null) 
as SecondHighestSalary;

-- 1045.买下所有产品的用户，从 Customer 表中查询购买了 Product 表中所有产品的客户的 id。用了很多次distinct
select distinct tt.customer_id from  
(select customer_id, product_key from Customer 
group by customer_id having count(distinct product_key) = (select distinct count(*) from Product )) tt;
```

```mysql
-- 177. 第N高的薪水，有传参，mysql大小写不敏感，所以n和N是同一个
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
 declare nn int;
 SET N = N -1; -- 不能在下面limit里面直接写N-1会报错
  RETURN (
      # Write your MySQL query statement below.
      select ifnull((select distinct salary from Employee order by salary desc limit N,1) 
              , null) 
  );
END

-- 181.超过经理收入的员工，自关联
-- 这里面涉及到新起名字，这样可以引用到值
select a.name as Employee from Employee a join Employee b on a.managerId = b.id where a.salary > b.salary;
-- 570. 至少有5名直接下属的经理，join表group by
select distinct b.name as name from Employee a join Employee b on a.managerId = b.id group by a.managerId having count(a.managerId) >=5;
-- 626. 换座位,相邻学生换座位，就是重命名id，每个id变换成(id+1)^1 -1，但是最后一个奇数的id是不变的，涉及到一个求表格总记录数暂时记录问题：(select count(*) as counts from Seat) as seat_counts一定要有as seat_counts，用counts
select
case 
	when a.id = counts and a.id % 2 = 1 then a.id
	else (a.id + 1) ^ 1 - 1 
end 
as id,a.student as student 
from Seat a,
(select count(*) as counts from Seat) as seat_counts order by id; 

-- 1164. 指定日期的产品价格，难
select distinct a.product_id, -- 
ifnull( 
    (select b.new_price -- 子查询 <=指定日期的最大日期的产品价格，如果没有的话，选默认值10
    from Products b -- 这里只能select一个，因为后面默认值10是单个数，这里必须是单个值的返回值
    where b.change_date <= '2019-08-16' and b.product_id = a.product_id
    order by b.change_date desc limit 1),10)  as price 
from Products a;
-- 511.游戏玩法分析，获取每位玩家 第一次登陆平台的日期，最小日期，分组查询每一组的最小日期
select t.player_id, min(t.event_date) as first_login from Activity t group by player_id -- 直接min的值返回

-- 1393.股票的资本损益
select a.stock_name,
sum(
  case 
  when a.operation = 'Buy' then -price
  else price
  end
) 
as capital_gain_loss 
from Stocks a group by a.stock_name 
```

```mysql
-- SC学生成绩表，包含学生id，课程id，成绩；Student学生信息表，包含学生id，name。筛选出有>=2门课程成绩<60的学生id，name，平均成绩
select
student.sname,
t.sid,
t.avg
from (
	select 
		sid,
		AVG(score) as avg,
		SUM(if(score <60,1,0)) as cnt
	from sc
	GROUP BY sid
	having cnt >= 2
)t join student on t.sid = student.sid;
```

# 常见面试题

## 四种排序方式比较

| 排序方式      | 描述                                                         | 优缺点                               |
| ------------- | ------------------------------------------------------------ | ------------------------------------ |
| order by      | 全局排序，只有一个reducer，通过order字段进行降序或者升序     | 大规模的数据集 order by 的效率非常低 |
| sort by       | Sort by 为每个reducer 产生一个排序文件。每个 Reducer 内部进行排序，对全局结果集来说不是排序。 | 每个reducer内部排序                  |
| distribute by | 类似MR的partition，控制在map端如何拆分数据给reduce端，默认hash算法，distribute by经常和sort by配合使用 | 多reducer才能看到效果                |
| cluster by    | cluster by除了具有distribute by的功能外还兼具sort by的功能。但是排序只能降序 |                                      |
