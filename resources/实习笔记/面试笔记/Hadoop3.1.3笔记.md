# Hadoop概述

1. 大数据：海量、高增长率、多样化的信息，T，P，E。解决海量数据的**采集、存储和分析计算**

2. 特点：4V：Volume（大量，接近EB——用存储解决）；

Velocity（高速——运算调优解决）；

Variety（多样，结构化、非结构化——采集解决）；

Value（低价值密度，在海量中对有价值的进行提纯——数据清洗ETL）

3. 结构化：关系型数据库表示，二维表

   半结构化：自描述，HTML，json，数据库文件

   非结构化：没有固定格式，语音视频图像，一般存储为二进制

4. 大数据部门与产品部门打交道

   
   
   ![image-20230311155029786](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230311155029786.png)

## Hadoop入门

完全分布式：开发面试的重点

### 概念、发展历史

Apache开发的分布式系统基础架构。

解决海量数据的存储、分析计算

Hadoop生态圈（HDFS，MapReduce，Common）

doug cutting创始人

<font color='red'>三大发行版本</font>：

Apache：最原始（最基础）的版本2006

Cloudera：封装Apache，CDH，集成了很多大数据框架2008

Hortonworks：文档好，HDP，2011

Cloudera与Hortonworks合作，新品牌CDP

<font color='red'>四大优势：</font>

高可靠性：多个数据副本，数据丢失可能性小

高扩展性：集群间分配任务数据，可扩展上千节点

高效性：并行工作

高容错：自动将失败任务重新分配

### Hadoop组成，面试重点

![image-20230311161933234](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230311161933234.png)

3.x与2.x在组成上没有区别

#### HDFS（Hadoop Distributed File System，分布式文件系统）

NN，NameNode：记录文件存储位置、属性、目录结构

DN，DataNode：具体存储元数据和校验和（保证数据正确）

2NN，Secondary NameNode：辅助作用，隔时间对NameNode备份，不能让NameNode挂了

#### Yarn（Yet Another Resource Negotiator，资源协调者）

RM，Resource Manager：整个集群（CPU、内存）的老大

NM，Node Manager：单个节点服务器的老大

AM，ApplicationMaster：单个任务运行的老大

Container：容器，单个任务在Container里面运行，相当于一个独立的服务器，里面封装了内存、CPU、磁盘、网络

多Client，多AM，每个NM多Container

#### MapReduce

将计算过程分为两个阶段：**Map并行**处理输入数据，和**Reduce**对Map结果进行**汇总**

#### 关系

来了Task$\rightarrow$Yarn RM给Task分配一个NM，开启一个Container，作为AM$\rightarrow$AM向NM申请资源，AM开启MapTask

$\rightarrow$开始Map和reduce

$\rightarrow$Reduce的结果写入HDFS的DN，更新NN和2NN

#### 大数据体系

![image-20230311165628267](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230311165628267.png)

## 配置

两个用户：

atguigu Seq000*

root 000000

修改网络文件：/etc/sysconfig/network-scripts/ifcfg-ens33

修改主机名映射：/etc/hosts

关闭防火墙：systemstl stop firewalld

systemctl disable firewalld.service

克隆时修改ifcfg-ens33和hosts两个文件，通过ifconfig;hostname;ping www.baidu.com 查看是否成功

安装jdk8u212，hadoop3.1.3，配置/etc/profile.d/my_env.sh里面的环境变量

配置IP地址网关等，配置主机名称

![image-20230312101558873](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312101558873.png)



cd /opt/module/hadoop-3.1.3/bin

my_hadoop.sh start

my_jps.sh start

## 面试题::star:

（1）bin目录：存放对Hadoop相关服务（hdfs，yarn，mapred）进行操作的脚本

（2）etc目录：Hadoop的配置文件目录，存放Hadoop的配置文件

（3）lib目录：存放Hadoop的本地库（对数据进行压缩解压缩功能）

（4）sbin目录：存放启动或停止Hadoop相关服务的脚本

（5）share目录：存放Hadoop的依赖jar包、文档、和官方案例



**hadoop3.1.3/etc/hadoop的自定义配置文件：5个**

![image-20230312123810349](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312123810349.png)

| 配置文件名称    | 作用                                                         | 修改 |
| --------------- | ------------------------------------------------------------ | ---- |
| core-site.xml   | 指定NameNode的内部端口地址8020/9000/9820、hadoop数据存储目录、配置HDFS网页登录使用的静态用户（让web端可删除HDFS文件） |      |
| hdfs-site.xml   | NN web地址9870、2NNweb地址9868                               |      |
| yarn-site.xml   | RM hostname  hadoop103、环境变量继承<br>配置日志web地址、保留时间second |      |
| mapred-site.xml | 指定MapReduce运行在Yarn、<br>历史服务器内部端口hadoop102:**10020**、历史服务器web地址hadoop102:**19888** |      |
| workers         | 群起集群，102，103，104是一个集群。该文件中添加的内容结尾不允许有空格，文件中不允许有空行。 |      |



| 端口名称                          | Hadoop2.x   | Hadoop3.x        |
| --------------------------------- | ----------- | ---------------- |
| NameNode内部通信端口              | 8020 / 9000 | 8020 / 9000/9820 |
| NameNode HTTP UI                  | 50070       | 9870             |
| MapReduce查看执行任务端口,Yarn RM | 8088        | 8088             |
| 历史服务器通信端口                | 19888       | 19888            |

## 完全分布式模式下的集群配置



3种运行模式

本地模式：单机运行，数据存储在linux本地——测试偶尔用

伪分布式模式：单机运行，数据存储在HDFS——缺钱公司用

**完全分布式模式**：多台服务器工作，数据存储在HDFS——生产环境使用

scp实现server到server的拷贝

scp  -r     $pdir/$fname       $user@$host:$pdir/$fname

命令  递归   要拷贝的文件路径/名称  目的地用户@主机:目的地路径/名称

时间服务器配置与关闭防火墙都是systemctl命令



需求：HDFS的NN、2NN与YARN的RM在三台服务器上

![image-20230312123955762](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312123955762.png)

![image-20230422095530211](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230422095530211.png)

- 首次启用集群，需要初始化102上的nameNode：hdfs namenode -format，

  出现了data和logs两个文件夹在module/hadoop3.1.3

下面这个路径里面的VERSION是版本号

![image-20230312131629454](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312131629454.png)

- 启动HDFS：hadoop3.1.3/sbin/start-dfs.sh

![image-20230312133521009](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312133521009.png)

- 启动Yarn命令：hadoop3.1.3/sbin/start-yarn.sh

![image-20230312135612762](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312135612762.png)

查看NameNode http://hadoop102:9870

查看ResourceManager http://hadoop103:8088

查看JobHistory和日志：http://hadoop102:19888/jobhistory

- 创建文件夹：**hadoop fs -mkdir /wcinput**

- 上传文件：**hadoop fs -put  /opt/software/jdk-8u212-linux-x64.tar.gz  /wcinput**

- 下载文件：**hadoop fs -get /jdk-8u212-linux-x64.tar.gz /wcinput**
- 执行wordcount: **hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /wcinput /output**

- 启动历史服务器：mapred --daemon start historyserver

  整体启动NM、RM：sbin/start-yarn.sh

  整体启动HDFS：sbin/start-dfs.sh

  分别启动/停止HDFS组件 hdfs --daemon start/stop namenode/datanode/secondarynamenode

  分别启动/停止YARN         yarn --daemon start/stop  resourcemanager/nodemanager

## 集群崩溃处置

![image-20230312143041560](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312143041560.png)

- 杀死进程：sbin/stop-dfs.sh

- 删除所有节点服务器102，103，104历史数据data和logs：rm -rf data/ logs/

- 初始化集群：hdfs namenode -format

- 启动集群：sbin/start-dfs.sh

如果服务器在公网环境（能连接外网），可以不采用集群时间同步。**时间服务器配置（必须root用户）**

## 常见错误

![image-20230312155313556](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312155313556.png)

# HDFS

## HDFS概述

文件系统：HDFS，NTFS，ext4

适合一次写入，多次读出；只能查询追加，不能修改了

### 优缺点

|           | 优点                                                         | 缺点                                                         |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| HDFS      | 高容错性；适合海量数据TBPBEB；<br>可构建在廉价机器上         | 不适合低延时的数据访问；无法高效存储大量小文件<br>不支持多线程并发写入，不支持修改历史数据，只支持追加。<br>小文件存储的寻址时间可能超过读取时间 |
| MapReduce | 高容错；适合TB、PB级海量数据的离线处理<br>易于编程可布于廉价PC<br>可扩展：仅增加机器即可 | 不擅长实时计算（mysql）；不擅长流式计算（flink）；<br>不擅长DAG（有向无环图）计算(输出为新输入，迭代计算) |
| Yarn      |                                                              |                                                              |
| Flink     |                                                              |                                                              |
| Spark     |                                                              |                                                              |

优点：

- 高容错性（多副本，丢失可自动恢复）

- 适合处理大数据（数据规模T P E;文件规模百万级）

- 可构建在廉价机器上

缺点：

- 不适合低延时的数据访问（sql的毫秒级别）

- 无法高效存储大量小文件

- 小文件存储的寻址时间可能超过读取时间

- 不支持多线程并发写入，不支持修改历史数据，只支持追加。

  

### 组成（4部分）



![image-20230312195204657](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312195204657.png)

* NN：Master，管理HDFS的名称空间；配置副本策略；管理block的映射信息；处理client读写需求、

* DN：Slave，存储实际的数据块；处理block的读写操作

- 2NN：并非NN的热备。为了集群高可用，一般用两个NN，因为2NN不能马上替换上去，而且只能恢复一部分。

分担NN工作量，定期合并Fsimage和Edits，并推送给NN

- Client：文件切分为block（128M或256M）上传HDFS；与NN交互获取位置信息；与DN交互读写数据；

  管理HDFS的命令（hdfs namenode -format）;访问HDFS增删改查。



### HDFS文件块大小::star:

**块大小取决于磁盘传输速率**。如果太小，文件分的稀碎回增加寻址时间；如果太大，传输时间回远超寻址时间（不止100倍），处理数据很慢，不利于并发运算。

寻址时间（找到目标block的时间）为10ms，寻址时间为传输时间的1%是最理想的即1000ms

默认128M（普通硬盘的传输速率80-90M）；大厂256M（固态硬盘200-300M）



## Shell操作（开发重点）

### 上传下载

-moveFromLocal：剪切本地到hdfs

-copyFromLocal=-put

-appendToFile：在hdfs文件上追加本地文件

```
hadoop fs  -moveFromLocal  ./shuguo.txt  /sanguo
hadoop fs -copyFromLocal weiguo.txt /sanguo
hadoop fs -put ./wuguo.txt /sanguo
hadoop fs -appendToFile liubei.txt /sanguo/shuguo.txt
```

copyToLocal=get

```
hadoop fs -get /sanguo/shuguo.txt ./
```

### HDFS直接访问

修改owner：hadoop fs  -chown  atguigu:atguigu  /sanguo/shuguo.txt

创建文件夹：hadoop fs -mkdir /jinguo

-cp -mv -rm

展示尾数据：hadoop fs **-tail** /jinguo/shuguo.txt

统计文件夹总大小du -s;详情大小du -h

![image-20230312211041620](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312211041620.png)



设置副本数量：hadoop fs **-setrep** 10 /jinguo/shuguo.txt

如果有3台服务器，setrep10也每台服务器只存一个副本，但是给记账，以后多了服务器就拷贝过去。至于拷贝到哪几台上，有一个**副本存储设备**，按照**节点距离最近**原则保留。

## Windows API访问（上传下载）

```
创建一个文件夹fs.mkdirs
上传fs.copyFromLocalFile
下载fs.copyToLocalFile
文件删除fs.delete
更名和移动fs.rename
获取文件详细信息fs.listFiles 迭代器
判断是文件夹还是文件fs.listStatus，status.isFile()
```

```
@Before // 建立连接
连接NN的内部端口,8020,9000,9820
        URI uri = new URI("hdfs://hadoop102:8020");
创建一个配置文件
        Configuration configuration = new Configuration();
获取到客户端对象
        String user = "atguigu";
参数优先级 低->高：hdfs-default.xml => hdfs-site.xml => resources/hdfs-site.xml =>程序
在程序里设置的语法
        configuration.set("dfs.replication","2");
开始连接
        fs = FileSystem.get(uri,configuration,user);
 @After // 关闭
         fs.close();
```

## 读写流程（面试重点）

### <font color='red'>写数据</font>:star:

目的：将一个文件上传到NameNode

![image-20230312213936833](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230312213936833.png)

（8）当一个Block传输完成之后，客户端再次请求NameNode上传第二个Block的服务器。（重复执行3-7步）。

### 节点距离计算，机架感知

<font color='red'>节点距离：两个节点到达最近的共同祖先的距离总和。数线</font>

机架感知：副本存储节点选择。综合考虑 节点距离最近原则 和负载均衡原则

No1：Client所在节点的机架r1（距离最小） No2:另一个机架r2（负载均衡）No3:与No2在同一个机架r2(距离最近)

### 读数据:star:

![image-20230313100543523](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313100543523.png)



## NN和2NN（了解）

 NN的数据存在内存或磁盘都有弊端，因此引入三个东西：**FsImage镜像，Edits记录日志，NN合并前两个**

内存存储一份NN，FsImage作为NN在磁盘的备份，Client操作内存的NN时，Edits记录流程，但不同步操作FsImage，即FsImage+Edits=内存NN。等到NN断电关机或者**2NN定期合并FsImage与Edits**。

NN如何确定下次开机合并哪些Edits：根据时间和序列号，如果fsimage355,只合并>355的edits

![image-20230313102901851](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313102901851.png)

oiv oev查看fsimage和edits

NN里面不存储文件块信息，是通电时候DN主动汇报自己存储了哪个目录或者文件的文件块

2NN对NN的文件检查：定时时间：hdfs-default.xml 默认3600s即1h定时合并一次

数据满了100w：60s去查看edits数据是否满了

## DataNode工作机制（了解）

![image-20230313105101611](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313105101611.png)

数据完整性：当DataNode读取Block的时候，它会计算CheckSum（crc（32），md5（128），sha1（160））。如果计算后的CheckSum，与Block创建时值不一样，说明Block已经损坏。

# MapReduce

## MapReduce概述

是用户开发“基于Hadoop的数据分析应用”的核心框架。MapReduce核心功能是将用户编写的业务**逻辑代码**和自带**默认组件**整合成一个**完整的分布式运算程序**，并发运行在一个Hadoop集群上。

### 优缺点

优点：

易于编程：封装接口，可布于廉价PC

可扩展：仅增加机器即可

高容错：如果一个挂了，可以转移给其他节点

适合TB、PB级海量数据的离线处理，上千数据

缺点：

不擅长实时计算（mysql）：MapReduce无法像MySQL，在毫秒或者秒级内返回结果。

不擅长流式计算（Sparkstreaming，flink）：Mapreduce输入是静态的，不能动态变化的流式计算

不擅长DAG（有向无环图，spark中间结果基于**内存**）计算：后一个应用程序的输入为前一个的输出，MapReduce会造成大量的**磁盘**IO，性能非常低下。

### 核心思想

通过WordCount理解

- 第一个阶段的MapTask并发实例，**完全并行运行**，互不相干。

- 第二个阶段的ReduceTask**并发实例互不相干**，但是他们的数据依赖于上一个阶段的所有MapTask并发实例的输出。

MapReduce编程模型**只能包含一个Map阶段和一个Reduce阶段**，如果用户的业务逻辑非常复杂，那就只能多个MapReduce程序，**串行运行**（所以不适合流式计算）。

MapReduce进程3阶段：**MrAppMaster**(整个程序的过程调度及状态协调)、MapTask、ReduceTask。

### WordCount源码

| **Java类型**                    | **Hadoop Writable类型**       |
| ------------------------------- | ----------------------------- |
| Boolean                         | BooleanWritable               |
| Byte                            | ByteWritable                  |
| Int                             | IntWritable                   |
| Float                           | FloatWritable                 |
| Long                            | LongWritable                  |
| Double                          | DoubleWritable                |
| <font color='red'>String</font> | <font color='red'>Text</font> |
| Map                             | MapWritable                   |
| Array                           | ArrayWritable                 |
| Null                            | NullWritable                  |

### MapReduce编程

用户编写的程序分成三个部分：Mapper、Reducer和Driver。

Mapper阶段：

* 输入输出都是KV对；业务逻辑写进map()；**map()对每个KV对只调用一次；**
* 输入key是偏移量，比如第一行为atguigu atguigu，那么第一行的key是0，第二行的key是17（第一行最后有回车和换行算作两个）
* 输出为<atguigu,1> <atguigu,1>
* Mapper是每行文件执行一次

Reduce阶段：

- 输入输出都是KV对；业务逻辑写进reduce()；**reduce对每一组相同的KV对调用一次**
- 输入是经过处理的Mapper输出，先按照首字母排序（a->z），KV对为<atguigu,(1,1)>
- 输出<atguigu,2>
- Reducer是每个Key执行一次

Driver阶段：相当于Yarn集群的客户端



linux运行打包的jar包：hadoop jar MapReduceDemo-1.0-SNAPSHOT.jar com.atguigu.mapreduce.wordcount.WordCountDriver /newjinguo/newshuguo.txt /output

输出路径如果存在，hadoop直接报错。

导包时候找hadoop于mapreduce的

Mapper是每行文件执行一次，Reducer是每个Key执行一次

## 序列化

序列化：内存对象->字节序列（或其他数据传输协议），可存储到磁盘（持久化）和网络传输给另一个内存。 

反序列化：字节序列（或其他数据传输协议）或磁盘的持久化数据->内存对象。

序列化可以存储“活的”对象，可以将“活的”对象发送到远程计算机。

Java的序列化是一个重量级序列化框架（Serializable），会附带很多额外的信息（各种校验信息，Header，继承体系等），传输不高效。

Hadoop序列化特点：紧凑（存储空间小）；快速（额外开销小）；互操作（支持多语言交互）

### 自定义bean对象实现序列化接口Writeable

当默认的key<Text>和value<IntWriteable>不满足我们的需求时(我们想要数组类型的value)，需要定义bean，转为字节序列

bean是Writeable的实现类 public class FlowBean implements Writable 

FlowBean

```
/**
 * 1、定义类实现writable接口
 * 2、重写序列化write和反序列化方法readFields，write的顺序于readFields的顺序完全一致，先进先出
 * 3、重写空参构造
 * 4、重写toString方法，不使用“ ”而用“\t”
 * 5. Text自带set()方法
 */
```

FlowMapper

```

 读取一行
 切割
 封装outK outV
 context 写入
 
```

FlowReducer

```
读取Mapper的输出<K,V>
遍历K的value进行累加
封装outK outV
context 写入
```

FlowDriver

```
获取job
设置jar
关联mapper 和Reducer
设置mapper 输出的key和value类型
设置最终数据输出的key和value类型
设置数据的输入路径和输出路径
提交job
```



## 核心框架原理

![image-20230313161624669](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313161624669.png)

### InputFormat输入

Input默认**TextInputFormat**按行读取，也有其他读取方式**CombineTextInputFormat**、自定义InputFormat、KeyValueTextInputFormat、NLineInputFormat和自定义InputFormat等。都是FileInputFormat的实现类。

#### 切片机制，解析抽象类FileInputFormat的切片源码

MapTask个数决定并行度。一个job的Map阶段并行度由Client提交时的切片数决定（数据切成n片，就给n个mapTask）

默认情况下，切片大小=块大小

切片时不考虑数据集整体，无论client提交几个文件，都针**对每一个文件单独切片**

![image-20230313164236372](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313164236372.png)

#### Text Input Format vs CombineTextInputFormat

```
// 如果不设置InputFormat，它默认用的是TextInputFormat.class
job.setInputFormatClass(CombineTextInputFormat.class);
```

**TextInputFormat是默认的FileInputFormat实现类**。按行读取每条记录。键是存储该行在整个文件中的起始字节偏移量， LongWritable类型。值是这行的内容，不包括任何行终止符（换行符和回车符），Text类型。如果有大量小文件，就会产生大量的MapTask，处理效率极其低下。

CombineTextInputFormat用于**小文件过多**的场景，它可以将多个小文件**从逻辑上规划到一个切片**中，这样，多个小文件就可以交给一个MapTask处理。

![image-20230313182122473](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313182122473.png)



### MapReduce工作流程:star:

MapTask5个阶段：Read阶段：1-5TextInputFormat；Map阶段：6；Collect阶段：7.8；溢写阶段：9；归并排序阶段：10

![image-20230313191129325](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313191129325.png)



ReduceTask阶段：Copy阶段：13；Sort阶段：归并多个MapTask14；Reduce阶段：Reduce处理数据，输出到文件 TextOutputFormat

<img src="https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313191529387.png" alt="image-20230313191529387" style="zoom: 200%;" />

![image-20230313214012756](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313214012756.png)

### Shuffle排序，分区，压缩，Combiner

Map之后Reduce之前混洗的过程，进行排序、分区、压缩

#### 分区Partition:star:

```
job.setPartitionerClass(ProvincePartitioner2.class);
job.setNumReduceTasks(5);
```

不同分区的数据最后进入不同文件，所以一开始就要分开

![image-20230313192934208](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313192934208.png)

shuffle默认分区：在context.write()跳进去的key的hasCode对ReduceTasks的个数取模得到的，没法控制存储到哪个分区。Driver里面默认ReduceTasks=1。

![image-20230313193943366](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313193943366.png)

自定义Partitioner

![image-20230313194304036](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313194304036.png)

ReduceTask=5表示生成5个文件，getPartition里面有5个表示分为5类

如果ReduceTask>getPartition，正常运行，但有空文件

如果ReduceTask!=1<getPartition，报错，因为有数据没位置写

如果ReduceTask=1<getPartition，不走自定义partition，正常运行，只写入一个文件



#### 归并排序:star:

MapTsk分为几个是切片机制，依照文件大小和块大小。ReduceTask分为几个是想要分为几类几个分区写入文件

Map里面两次快排归并（8索引快排归并，10写入磁盘时候归并）

Reduce里面一次归并（将不同mapTask的同一分区数据归并，写入一个文件）



排序是MapReduce最重要的操作之一。**故输入的key必须支持排序，也必须进行排序，否则不能使用该框架**

为何一定要key排序：因为Reduce的进入机制是：key到不为key的放在一组里，送给reduce()进行value的遍历，如果不对key排序，这一步无法执行 。MapReduce这个机制为了提高效率，否则要一直遍历。

排序频率：放环形缓冲区的数量达到一定阈值之后，进行一次快排（No1索引排序），并溢写到磁盘，最后都处理完毕对磁盘数据归并排序（No2）

排序分类：

部分排序（多用）：输出文件内部有序（每个分区内部有序）

全排序（慎用）：只有一个ReduceTask。因为丧失了MapReduce架构并发性能。

辅助排序（少用）：

二次排序：自定义排序中两个判断条件（先按总流量排序，若相同，则按照上行流量排序）

#### 自定义排序FlowBean实现WritableComparable接口

```
重写CompareTo
```

对总流量进行倒序排序：Mapper将总流量作为key，Reducer颠倒一下key value输出。

区内排序（排序+分区）：要求每个省份手机号输出的文件中按照总流量内部排序。

#### Combiner

```
job.setCombinerClass(WordCountCombiner.class);
job.setCombinerClass(WordCountReducer.class);// 可以直接用Reducer而不是新写一个Combiner
```

是MR程序中Mapper与Reducer之外的一种可选组件，父类Reducer。

Combiner与Reducer的区别在于运行位置：

- Combiner是在每个MapTask所在节点运行，**只管这一个MapTask**上的溢写数据的归并，进行局部汇总，减少运输量。
- Combiner不能影响最终业务逻辑（部分均值+部分均值的均值!=总均值）而且Combiner的输出应与Reducer的输入相对应
- 是解决数据倾斜的一个方法

- Reducer是在所有MapTask之后运行接收所有MapTask的结果。

***如果设置job.setNumReduceTasks(0)没有reducer阶段，则不会有shuffle阶段，即使进行了自定义的Combiner，也不会执行***



### OutputFormat输出

OutputFormat：输出默认写入文件，默认是TextOUtputFormat，也可以写入数据库、HDFS等

### MapReduce源码解析

进入环形缓冲区后，key和value必须支持序列化，因为要传输给Reduce另一台机器。

在map结束之后，collect.close()里面，执行sortAndSpill排序并溢写

### ReduceJoin合并表

ReduceJoin在企业中很容易数据倾斜，因为大量数据都在reduce端进行汇总，效率很低。

![image-20230313223854320](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313223854320.png)

```
TableBean：有id，pid,amount,name,flag（表名）五个属性
TableMapper:分两个表，key为pid，value为TableBean
TableReducer：分两个表，输入为一个key得到的所有数据，对于order加入结果数组暂存，BeanUtils.copyProperties(tmptableBean,value); orderBeans.add(tmptableBean);不能orderBeans.add(value)
对于pd只保存pname，然后pname复值给order元素，选择属性输出
```

### MapJoin合并表

MapJoin的服务器比较多，因此速度快

适合一张大表（order）和一张很小的表（pd）

```
// Map端Join的逻辑不需要Reduce阶段，设置reduceTask数量为0
job.setNumReduceTasks(0);
```

读取pd表在内存中，pid为key，pname为value

读取order表，hashMap取pname，直接context.write

### ETL Extract-Transform-Load

在运行核心业务MapReduce程序之前，先数据清洗，清理掉不符合用户要求的数据。清理的过程往往**只需要运行Mapper程序，不需要运行Reduce程序**。

![image-20230313231349448](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230313231349448.png)

### MapReduce总结:star:

1）输入数据接口：InputFormat
（1）默认使用的实现类是：TextInputFormat
（2）TextInputFormat的功能逻辑是：一次读一行，起始偏移量：key，行内容：value。
（3）CombineTextInputFormat可以把多个小文件合并成一个切片处理，提高处理效率。

2）逻辑处理接口：Mapper 
用户根据业务需求实现其中三个方法：map()业务逻辑   setup()初始化获取   cleanup ()关闭资源 

3）Partitioner分区
（1）有默认实现 HashPartitioner，逻辑是根据key的哈希值和numReduces来返回一个分区号；key.hashCode()&Integer.MAXVALUE % numReduces
（2）如果业务上有特别的需求，可以自定义分区。

4）Comparable排序
（1）当我们用自定义的对象作为key来输出时，就必须要实现WritableComparable接口，重写其中的compareTo()方法。
（2）部分排序：对最终输出的每一个文件进行内部排序。
（3）全排序：对所有数据进行排序，通常只有一个Reduce。
（4）二次排序：排序的条件有两个。

5）Combiner合并：**可解决数据倾斜**
Combiner合并可以提前预聚合，提高程序执行效率，减少IO传输。但是使用时必须不能影响原有的业务处理结果。

6）逻辑处理接口：Reducer
用户根据业务需求实现其中三个方法：reduce() 业务逻辑  setup()初始化   cleanup ()关闭资源 

7）输出数据接口：OutputFormat
（1）默认实现类是TextOutputFormat，功能逻辑是：将每一个KV对，向目标文本文件输出一行。
（2）用户还可以自定义OutputFormat。

## 压缩算法

优点：以减少磁盘IO、减少磁盘存储空间。

缺点：增加CPU开销。

原则：

（1）运算密集型的Job，少用压缩

（2）IO密集型的Job，多用压缩

| 压缩格式                       | Hadoop自带？                          | 算法    | 文件扩展名 | 是否可切片 | 换成压缩格式后，原来的程序是否需要修改 |
| ------------------------------ | ------------------------------------- | ------- | ---------- | ---------- | -------------------------------------- |
| DEFLATE                        | 是，直接使用                          | DEFLATE | .deflate   | 否         | 和文本处理一样，不需要修改             |
| Gzip                           | 是，直接使用                          | DEFLATE | .gz        | 否         | 和文本处理一样，不需要修改             |
| <font color='red'>bzip2</font> | 是，直接使用                          | bzip2   | .bz2       | 是         | 和文本处理一样，不需要修改             |
| <font color='red'>LZO</font>   | <font color='red'>否，需要安装</font> | LZO     | .lzo       | 是         | 需要建索引，还需要指定输入格式         |
| Snappy                         | 是，直接使用                          | Snappy  | .snappy    | 否         | 和文本处理一样，不需要修改             |

性能比较

| 压缩算法 | 原始文件大小 | 压缩文件大小 | 压缩速度 | 解压速度 |
| -------- | ------------ | ------------ | -------- | -------- |
| gzip     | 8.3GB        | 1.8GB        | 17.5MB/s | 58MB/s   |
| bzip2    | 8.3GB        | 1.1GB        | 2.4MB/s  | 9.5MB/s  |
| LZO      | 8.3GB        | 2.9GB        | 49.3MB/s | 74.6MB/s |
| snappy   |              |              | >250MB/s | 500MB/s  |

方式选择：压缩/解压缩速度、是否支持切片、压缩率

位置选择：

![image-20230313232915231](C:/Users/21226/AppData/Roaming/Typora/typora-user-images/image-20230313232915231.png)

# Yarn

## 基础架构

Yarn是一个资源调度平台，负责为运算程序**提供服务器运算资源**，相当于一个分布式的操作系统平台，而MapReduce等运算程序则相当于运行于操作系统之上的应用程序。

Yarn-RM：处理Client请求；监控NM；启动监控AP；资源的分配与调度

Yarn-NM：管理单个节点资源；处理RM请求；处理AM的请求

Yarn-AM：向RM请求资源；监控自己底下MapTask ReduceTask的情况

Yarn-Container：Yarn资源抽象，封装网络、内存、CPU、磁盘资源

## 工作机制:star:

<img src="https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314093740011.png" alt="image-20230314093740011" style="zoom:200%;" />

设置yarnRunner模式:arrow_right:向RM提交job:arrow_right:向RM申请AM:arrow_right:RM放在自己的队列里，等待空闲的NM领走任务:arrow_right:NM领走之后，AM创建container，下载job到本地（根据split的数量决定创建几个maptask）:arrow_right:AM指示Maptask运行代码，产生yarnChild:arrow_right:ReduceTask获取对应分区的数据。

## 调度器和调度算法:star:

如何创建队列：

 按照框架：hive /spark/ flink 每个框架的任务放入指定的队列（10000条任务，一个3333，还是不够）

按照业务模块：登录注册、购物车、下单、业务部门1、业务部门2（多用）

创建多队列的好处：

（1）因为担心员工不小心，写递归死循环代码，把所有资源全部耗尽。

（2）实现任务的***\*降级\****使用，特殊时期保证重要的任务队列资源充足（618 双11）

### FIFO调度器

优点：简单易懂；

缺点：不支持多队列高并发，生产环境很少使用；

### 容量调度器（Capacity Scheduler）（Apache Hadoop3.1.3默认）

Yahoo开发的多用户调度器。

多队列：每个队列配置一定资源量，每个队列FIFO，但是每个队列可以**并发**启动多个任务。

容量保证：管理员可以为每个队列设置最低保障和资源上限

灵活性：虽然设有上限，但是如果别的队列B有剩余，A可以借过来用，但是一旦B有新的应用程序需要，则B可以直接抢过来。

多租户：支持多用户共享集群和多程序同时运行。为防止同一用户独占资源（递归死循环任务），会对同一用户所占资源进行限定。

![image-20230314095643266](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314095643266.png)

### 公平调度器（Fair Scheduler）（CDH框架默认）:star::star:

目标：所有作业获得公平的资源

|                                 | 容量调度器                                                   | 公平调度器                                                   |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 相同                            | **<font color='red'>多队列 容量保证 灵活性 多租户</font>**   |                                                              |
|                                 | 公平和容量默认1个队列，需要创建多队列                        |                                                              |
| 不同1核心策略                   | 优先选择资源利用率低的队列<br>每个队列：优先级，FIFO         | 优先选择对资源**缺额**比例大的<br>追求队列任务公平享有队列资源，每次都是均分后分配，直至公平<br />无论何时进来，对这个任务都公平，时间尺度上公平 |
| 不同2每个队列单独的资源分配方式 | FIFO、DRF                                                    | FIFO、**FAIR**、DRF                                          |
| 不同——多队列                    | 中小型公司，对并发度要求不高，多队列按照框架hive /spark/ flink | 大公司，对并发度要求高，多队列按照业务模块                   |
| 不同——默认                      | Apache Hadoop3.1.3默认                                       | CDH框架默认                                                  |

1. 缺额：

某一时刻一个作业应获资源和实际获取资源的**差距叫“缺额”**（刚进来的job）。

调度器会优先为缺额大的作业分配资源（从别的作业中释放资源）

2. FAIR策略：
   Fair策略(默认)是基于**最大最小公平算法实现的资源多路复用方式**，默认情况下，每个队列内部采用该方式分配资
   源。这意味着，如果一个队列中有两个应用程序同时运行，则每个应用程序可得到1/2的资源;如果三个应用程序同时运行，则
   每个应用程序可得到1/3的资源。
   具体资源分配流程和容量调度器一致;
   否饥饿、资源分配比、资源使用权重比)
   (1)选择队列
   (2)选择作业
   (3)选择容器
   以上三步，每一步都是按照公平策略分配资源

➢实际最小资源份额: mindshare = Mn (资源需求量，配置的最小资源)
➢是否饥饿: isNeedy =资源使用量< mindshare (实际最小资源份额)
➢资源分配比: minShareR atio =资源使用量1 Max (mindshare, 1)
➢资源使用权重比: useToWeightRato =资源使用量/权重
![image-20230314102146516](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314102146516.png)

3. DRF策略 考虑CPU和内存、网络带宽

## Yarn在生产环境下参数功能

yarn application -list	 // 列出所有Application： 
yarn application -list -**appStates** 	// 过滤状态，全部/新/已结束/正在运行  ALL/NEW/FINISHED/RUNNing
yarn application -**kill** application_1612577921195_0001 	// 根据app_id杀死任务
yarn **logs** -applicationId application_1612577921195_0001	 // 根据app_id查看某一任务的日志
yarn **applicationattempt** -list application_1612577921195_0001 	// 查看尝试执行的任务<br>

yarn container -list application_1612577921195_0001		// 查看该app的容器有哪些<br>yarn container -statuc container_1612577921195_0001_01_000001		// 根据container_id查看container状态

<font color='green'>只有在任务跑的途中才能看到container的状态</font>

yarn node -list -all		// 列出所有节点

![image-20230314105142825](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314105142825.png)

在对集群做大规模改动前，可以先**拍摄快照**。虚拟机右键->拍摄快照。如果整费了，直接点击“恢复快照”

一般一个CPU可以带动两个线程，6核12线程

## Yarn 的tool接口

自己写的程序想要在服务器上指定队列提交，直接使用args[0]这种不行，因为会把-Dmapreduce.job.queuename=root.test 这个队列命令作为args[0]。所以自己的代码需要实现tool接口。

```
 在Driver里面，运行重写的run函数，传参从args[1]开始，把-D的跳过去。
 int run = ToolRunner.run(conf, tool, Arrays.copyOfRange(args, 1, args.length));
```



# 生产调优手册

## HDFS核心参数、集群压测

每个文件块大概占用150byte，一台服务器128G内存为例，能存储多少文件块呢？

128 * 1024 * 1024 *1024 / 150byte = 9.1亿

namenode：最小值1G，NN集群的每增加100w个block,增加1G内存；

datanode：最小值4G, block数或副本数升高，都应该调大datanode的值。副本总数低于400w,调为4G；超过400w每增加1000w个副本数，增加1G内存

1. 内存配置：hadoop-env.sh

   export **HDFS_NAMENODE_OPTS**="-Dhadoop.security.logger=INFO,RFAS -Xmx**1024**m"

   export **HDFS_DATANODE_OPTS**="-Dhadoop.security.logger=INFO,RFAS -Xmx**1024**m"

2. NN心跳配置 hdfs-site.xml

   NameNode有一个工作线程池，用来处理不同DataNode的并发心跳以及客户端并发的元数据操作。即准备多少线程来接收DN的汇报：   **dfs.namenode.handler.count**=20*ln(集群个数)

   

3. 回收站设置 core-site.xml

   **<font color='red'>通过程序删除的文件,调用moveToTrash()才能进入回收站。通过网页上直接删除的文件也不会走回收站。只有在命令行hadoop fs -r删除可以进去回收站</font>**

   防止误操作

   （1）默认值fs.trash.interval = 0，0表示禁用回收站；其他值表示设置文件的存活时间。
   （2）默认值fs.trash.checkpoint.interval = 0，检查回收站的间隔时间。如果该值为0，则该值设置和fs.trash.interval的参数值相等。
   （3）要求fs.trash.checkpoint.interval <= fs.trash.interval。

4. 集群压测：集群上传速度、下载速度

   HDFS的读写性能主要受***\*网络和磁盘\****影响比较大

   100Mbps = 100M bit /s 

   100M/s=100MB/s

   压测指令：TestDFSIO -write为写性能测试，-read为读性能测试

   hadoop jar /opt/module/hadoop-3.1.3/**share**/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.1.3-**tests.jar** **TestDFSIO** **-write** -nrFiles 10 -fileSize 128MB

   ```
    -nrFiles 10 -fileSize 128MB：向HDFS集群写10个128M的文件，-nrfiles是让每个服务器上都有mapTask运行，比如三台服务器，每个都是处理器内核总数=2，那么4<nrFiles<=6
   ```

   虚拟内存是物理内存的2.1倍，在集群压测时，如果超出内存，在yarn-site.xml里面把虚拟内存关掉。

   ```
   <!--是否启动一个线程检查每个任务正使用的虚拟内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
   <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
   </property>
   ```

   

## HDFS多目录 hdfs-site.xml

NN多目录 

NameNode的本地目录可以配置成多个（在hadoop102上有NN1 NN2），且每个目录存放内容**一模一样**，只是单纯增加了可靠性，生产环境下可以不配置。

在不同节点服务器上搭建NN高可用——HA

DN多目录 hdfs-site.xml

DataNode可以配置成多个目录，每个目录存储的数据**都不一样（数据不是副本，而是扩容增加磁盘）**。生产环境下多用

刚加载的硬盘没有数据时，可以执行**磁盘数据均衡命令diskbalancer**。

## 集群扩容及缩容 hdfs-site.xml

### 添加白名单

白名单：表示在白名单的主机IP地址可以，用来存储数据。

企业中：配置白名单，可以尽量防止黑客恶意访问攻击。

创建白名单whitelist和黑名单blacklist之后（里面填hostname），在hdfs-site.xml里面配置

第一次添加白名单/黑名单必须重启集群，不是第一次，只需要刷新NameNode节点(**hdfs dfsadmin -refreshNodes**)即可

```
<!-- 白名单 -->
<property>
     <name>dfs.hosts</name>
     <value>/opt/module/hadoop-3.1.3/etc/hadoop/whitelist</value>
</property>

<!-- 黑名单 -->
<property>
     <name>dfs.hosts.exclude</name>
     <value>/opt/module/hadoop-3.1.3/etc/hadoop/blacklist</value>
</property>
```

### 黑名单退役服务器

白名单更严格，只允许那几台访问；黑名单时不允许访问，管控力度弱一些。

添加blacklist->添加hdfs-site.xml->分发给其他服务器->刷新NameNode->该服务器会将自己的副本交给其他服务器->执行负载均衡指令start-balancer->手动停止黑名单服务器的进程

### 节点间数据均衡

服役新服务器：动态增加一台服务器，此时不关闭当前的这些服务器

数据均衡：任意两台服务器上的数据量相差一般设置为<10%。但是新服务器上没有数据，在新服务器上执行数据均衡指令start-balancer

```
[atguigu@hadoop105 hadoop-3.1.3]$sbin/start-balancer.sh -threshold 10  // 10为相差的阈值
```

## HDFS存储优化:star:

### 纠删码：3.x新特性

HDFS默认情况下，一个文件有3个副本，这样提高了数据的可靠性，但也带来了2倍的冗余开销。Hadoop3.x引入了纠删码，用计算量为代码节省存储空间（50%）。不是每个服务器上都存放全部300M数据，而是把数据拆分为数据单元和校验单元，存在不同服务器上。

![image-20230314161423121](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314161423121.png)

纠删码指令：hdfs ec

```
[atguigu@hadoop102 hadoop-3.1.3]$ hdfs ec
Usage: bin/hdfs ec [COMMAND]
          [-listPolicies]
          [-addPolicies -policyFile <file>]
          [-getPolicy -path <path>]
          [-removePolicy -policy <policy>]
          [-setPolicy -path <path> [-policy <policy>] [-replicate]]  // 针对某一路径设置纠删码策略，对该路径上传
          [-unsetPolicy -path <path>]
          [-listCodecs]
          [-enablePolicy -policy <policy>]  // 开启某一策略
          [-disablePolicy -policy <policy>]  //关闭某一策略
          [-help <command-name>].
```

支持的纠删码策略-listPolicies

RS-3-2-1024k：使用RS编码，每3个数据单元，生成2个校验单元：这5个单元中，只要有任意的3个单元存在（不管是数据单元还是校验单元，只要总数=3），就可以得到原始数据，每个单元大小1024KB

RS-10-4-1024k：14个单元里面挂4个也可以复原

RS-6-3-1024k：

RS-LEGACY-6-3-1024k：使用rs-legacy编码

XOR-2-1-1024k：使用XOR编码

### 异构存储（冷热数据分离） hdfs-site.xml

异构存储指令：hdfs storagepolicies

将经常使用的热数据存储在固态盘、高价格、性能高的盘中，不经常使用的冷数据存储在机械盘、低性能、低价格的盘中，实现冷热数据分离存储在不同的介质，达到性能最佳。

```
<property>
	<name>dfs.replication</name>
	<value>2</value>
</property>
<property>
	<name>dfs.storage.policy.enabled</name>
	<value>true</value>
</property>
```

默认存储策略为HOT。

HOT(存储在DISK)

WARM(一半在DISK，一半在ARCHIVE)

COLD(在ARCHIVE)

One_SSD(一半在SSD，一半在DISK)

ALL_SSD(都在SSD)

## HDFS故障排除

### NN故障排除

NN进程挂了数据丢失，如何恢复NN

进程丢了——恢复进程：hdfs --daemon start namenode

数据没了——2NN拯救：用scp -r将hadoop104上的2NN数据拷贝过来，重启NN

### 集群安全模式&磁盘修复

安全模式：文件系统只接受读数据请求，而不接受删除、修改等变更请求

进入安全模式场景：启动时NameNode在加载镜像文件fsimage和编辑日志edits期间；NameNode接收DataNode注册时期

退出安全模式场景：过了集群启动的稳定时间默认30s

安全模式指令：bin/hdfs **dfadmin -safemode get/enter/leave/wait**

```
（1）bin/hdfs dfsadmin -safemode get	（功能描述：查看安全模式状态）
（2）bin/hdfs dfsadmin -safemode enter （功能描述：进入安全模式状态）
（3）bin/hdfs dfsadmin -safemode leave	（功能描述：离开安全模式状态）
（4）bin/hdfs dfsadmin -safemode wait	（功能描述：等待安全模式状态
```

### 慢磁盘监控

把老化的读写很慢的磁盘定位出来

* 通过心跳未联系时间
* fio命令，测试磁盘的读写性能

### 小文件归档

小文件弊端：文件按块存储，小文件1M也占用一个block128M

解决方法：HDFS存档文件或HAR文件，对内还是一个一个独立文件，对NameNode而言却是一个整体，减少了NameNode的内存。

![image-20230314170141615](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314170141615.png)

把/input目录里面的所有文件归档成一个叫input.har的归档文件，并把归档后文件存储到/output路径下。

```
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop archive -archiveName input.har -p  /input   /output
```

归档文件不能直接查看，需要加上har://的协议头

```
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -ls har:///output/input.har  		// 查看归档文件
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -cp har:///output/input.har/* /   	// 解压到根目录
```

### 集群迁移

服务器的数据拷贝：scp

集群的数据拷贝：distcp

```
[atguigu@hadoop102 hadoop-3.1.3]$  bin/hadoop distcp hdfs://hadoop102:8020/user/atguigu/hello.txt hdfs://hadoop105:8020/user/atguigu/hello.txt
```

## MapReduce调优:star:

MapReduce跑的慢的原因

**<font color='red'>1）计算机性能：CPU、内存、磁盘、网络</font>**
**<font color='red'>2）I/O操作优化：数据倾斜；Map运行时间太长，导致Reduce等待过久；小文件过多</font>**

### Map调优 mapred-site.xml

| 调优方法                                                     | 操作位置                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **自定义分区**。减少数据倾斜，定义类，继承Partition 接口，重写getPartition方法 | Driver里面job.setPartitionerClass(ProvincePartitioner.class); job.setNumReduceTasks(1); |
| **减少溢写的次数**：默认Shuffle中的环形缓冲区大小100M，可提升至200M；默认到80%开始反向写，提高为90%； | mapred-site.xml                                              |
| 增加每次Merge合并个数：默认每次Merge10个，在内存够用的情况下，增加每次merge的个数到20 | mapred-site.xml   mapreduce.task.io.sort.factor              |
| 在不影响业务结果的前提条件下可以提前采用Combiner提前在map处归并，减轻ReduceTask的工作量：job. setCombinerClass (xxxReducer. class) |                                                              |
| 为了减少磁盘IO，可以压缩（Sheppy或者LZO）                    |                                                              |
| 默认MapTask内存上线1G。原则128M数据对应1G内存，可以提高内存  | mapred-site.xml                                              |
| 控制MapTask堆内存大小（调整内存之后，堆大小=MapTask内存大小） |                                                              |
| 默认MapTask CPU 1核，可以增加CPU核数                         | mapred-site.xml                                              |
| 异常重试：默认MapTask异常重试次数4，可以根据性能调整（高性能调小，低性能调大） | mapred-site.xml                                              |
|                                                              |                                                              |

### Reduce调优 mapred-site.xml

| 调优方法                                                     | 操作            |
| ------------------------------------------------------------ | --------------- |
| 默认Reduce拉取Maptask数据的并行数5，如果服务器内存大，可加到10 | mapred-site.xml |
| 默认**Buffer大小占Reduce可用内存的比例0.7**（ReduceTask给拉取的数据多少内存），可提高0.8 | mapred-site.xml |
| 默认**Buffer中的数据达到66%**开始写入磁盘，可提高0.75        | mapred-site.xml |
| 默认ReduceTask内存上线1G。原则128M数据对应1G内存，可以提高内存2-4G | mapred-site.xml |
| 内存调了同时堆内存大小也要相等                               | mapred-site.xml |
| 默认Reduce阶段CPU核数1，可提高2-4                            | mapred-site.xml |
| 也有重试次数4                                                | mapred-site.xml |
| 默认MapTask进行0.05的时候Reduce开始运行                      |                 |
| 默认超时时间10分钟（10min内没有数据进出则认为Reduce卡死），如果自己的Reduce程序运行时间很长，可用调大 | mapred-site.xml |
| **能不用Reduce尽量不用，不用Reduce就不用Shuffle**            |                 |

### Yarn调优 yarn-site.xml

| 调优方法                                                     | 操作                                                 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| 选择调度器，默认容量                                         | yarn.resourcemanager.scheduler.class                 |
| RM**处理调度器请求的线程数量**,默认50；如果提交的任务数大于50，可以增加该值，但是不能超过3台 * 4线程 = 12线程 | yarn.resourcemanager.scheduler.client.thread-count 8 |
| 是否让yarn自动检测硬件进行配置                               | 默认false                                            |
| 是否将虚拟核数当作CPU核数                                    | 默认是false，采用物理CPU核数                         |
| 虚拟核数和物理核数乘数                                       | 默认是1.0                                            |
| NodeManager使用内存数                                        | 默认8G，修改为4G内存                                 |
| nodemanager的CPU核数                                         | 默认是8个，修改为4个                                 |
| 容器最小内存                                                 | 默认1G                                               |
| 容器最大内存                                                 | 默认8G                                               |
| 容器最小CPU核数                                              | 默认1个                                              |
| 容器最大CPU核数                                              | 默认4                                                |
| 虚拟内存检查                                                 | 默认打开，修改为关闭                                 |
| 虚拟内存和物理内存设置比例                                   | 默认2.1                                              |



## 数据倾斜:star:

所有的计算引擎MapReduce spark Hive都有这个问题

对于MapReduce框架，数据倾斜一般在Reduce阶段。

1. 首先检查是否空值过多造成的数据倾斜(进入同一个key)：生产环境，可以直接过滤掉空值；如果想保留空值，就自定义分区，将空值加随机数打散。最后再二次聚合。
2. 能在map阶段提前处理，最好先在Map阶段处理。如：Combiner、MapJoin
3. 设置多个reduce个数=分区个数

## 小文件调优:star:

小文件弊端：

无论多小，都会占用NN的150B

会产生太多切片，启动过多的MapTask

解决方案：

1. （数据源头）在数据采集的时候，就将小文件或小批数据合成大文件再上传HDFS

2. （存储方向）**Hadoop Archive**是一个高效的将小文件放入HDFS块中的文件存档工具，能够将多个小文件打包成一个HAR文件，从而达到减少NameNode的内存使用

3. （计算方向）**CombineTextInputFormat**用于将多个小文件在切片过程中生成一个单独的切片或者少量的切片。 

4. （计算方向）开启**<font color='red'>uber</font>**模式，实现JVM重用。如果每次运行时间很短,但是运行次数很多,会重复的开启和销毁JVM,开启Uber可以复用JVM，避免频繁的开关JVM的资源浪费。

# Hadoop源码解析

## RPC通信原理

客户端和服务器端是如何通信的

![image-20230314203224724](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314203224724.png)

## 源码

### NN启动

![image-20230314205737825](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314205737825.png)

### DN启动

![image-20230314211820792](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314211820792.png)

### HDFS写数据

客户端与NN 创建RPC通信，发送给NN上传请求。NN判断是否由权限、路径是否存在，存在则添加到目录树上。

客户端创建一个DataStreamer输出流，DataStreamer管理一个dataQueue队列，同时DataStreamer通过机架感知获取block，之后开始写数据。

512的chunk+4Byte的校验和在packet队列中等待packet满，进入dataQueue进行传输。DataStreamer请求与一个DN建立管道，然后将dataQueue发送队列的packet依次发送给DN1的DataXceiver，同时有一个接收队列ackQueue，接收应答ack。

DN1一边接受数据，一边发送给下一个副本节点，一边接收DN2的ack。

如果DN1收齐该packet的三个ack，就告诉DataStreamer的ackQueue，可用把packet删掉，否则，将这个packet重新放入dataQueue进行重传。

![image-20230314215450389](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230314215450389.png)

### Yarn源码



### Hadoop源码
