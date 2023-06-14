# Spark概述

与Hadoop的MapReduce(将中间结果输出到磁盘上，进行存储和容错)相比，Spar**k基于内存**的运算要快100倍以上，基于硬盘的运算也要快10倍以上。Spark实现了高效的DAG(有向无环图)执行引擎，可以通过**基于内存**来高效处理数据流，**流式计算**

## Spark vs Hadoop

|                  | Hadoop                    | Spark                                                        |
| ---------------- | ------------------------- | ------------------------------------------------------------ |
| 开发语言         | Java                      | scala                                                        |
| 作用             | 海量数据的存储、分析计算  | 分析计算                                                     |
| 多个作业通信问题 | 基于磁盘                  | 基于内存                                                     |
| 启动方式         | 创建新进程，时间长        | fork进程，速度快                                             |
| 缓存机制         |                           | 更高效                                                       |
| 写入磁盘的时间   | shuffle                   | 多个MR作业之间的数据交互都依赖磁盘                           |
|                  | MapReduce不适用于流式计算 | 适合流式计算，满足循环迭代式数据流处理（机器学习、图挖掘算法），反复查询操作同样的数据<br />核心技术是**弹性分布式数据集**。提供了比 MapReduce 丰富的模型，可以快速在内存中对数据集进行多次迭代，来支持复杂的数据挖掘算法和图形计算算法 |

在绝大多数的数据计算场景中，Spark 确实会比 MapReduce 更有优势。但是 Spark 是基于内存的，所以在实际的生产环境中，由于内存的限制，可能会由于内存资源不够导致 Job 执行失败，此时，MapReduce 其实是一个更好的选择，所以 Spark并不能完全替代 MR。

## 核心模块

![image-20230329110254065](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230329110254065.png)

Spark Core：Spark Core 中提供了 Spark 最基础与最核心的功能，Spark 其他的功能都是在 Spark Core 的基础上进行扩展的

Spark SQL： Spark 用来操作结构化数据的组件。通过 Spark SQL使用 SQL HQL来查询数据。

Spark Streaming：针对实时数据进行流式计算的组件，提供了丰富的处理数据流的 API。

Spark MLlib：机器学习算法库（门槛高，涉及数学）。

Spark GraphX： 面向图计算提供的框架与算法库。

## 上手

3.0.0的spark对应3.12的scala

创建的可运行的必须是Object   New -> Scala Class -> kind:Object

WordCount

```
建立和Spark框架的连接
执行业务操作
关闭连接
```

spark比scala提供更多功能，可以将分组和聚合使用一个方法实现

## spark运行环境

主流yarn

1. local模式：

spark-shell，用于测试练习使用

![image-20230406092838293](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230406092838293.png)

idea开发的应用程序提交到本地环境

bin/**spark-submit** --class org.apache.spark.examples.SparkPi --master local[2] ./examples/jars/spark-examples_2.12-3.0.0.jar 10

--class：执行程序的主类，可替换成应用程序
--master local[2] 部署模式，默认为本地模式，数字表示分配的虚拟 CPU 核数量
第三个参数：jar包，可替换为本地打包的jar包
10：表示程序入参，设定当前应用的任务数量

2. Standalone模式

独立部署（Standalone）模式由 Spark 自身提供计算资源，无需其他框架提供资源。这种方式降低了和其他第三方资源框架的耦合性，独立性非常强。但Spark 主要是计算框架，而不是资源调度框架。

配置HA高可用，在集群中配置多个 Master 节点，一旦处于活动状态的 Master发生故障时，由备用 Master 提供服务，保证作业可以继续执行。这里的高可用一般采用Zookeeper 设置。

3. Yarn模式：在国内工作中，Yarn 使用的非常多，为spark提供资源调度，spark负责分析计算。

4. **K8S & Mesos** 模式：国外比较多

| 模式       | spark安装机器数 | 需启动的进程数 | 所属者 | 应用场景 |
| ---------- | --------------- | -------------- | ------ | -------- |
| Local      | 1               | 无             | Spark  | 测试练习 |
| Standalone | 3               | Master Worker  | Spark  | 单独部署 |
| Yarn       | 1               | Yarn HDFS      | Hadoop | 混合部署 |

端口号

| 端口名称                                           | 端口号           |
| -------------------------------------------------- | ---------------- |
| Spark-shell 运行任务情况端口号（计算）             | 4040             |
| Spark Master 内部通信服务端口号                    | 7077             |
| Standalone 模式下，Spark Master Web 端口号（资源） | 8080             |
| Spark历史服务器端口号                              | 18080            |
| Hadoop YARN 任务运行情况查看端口号                 | 8088             |
|                                                    |                  |
| Hadoop3.x                                          | 端口号           |
| NameNode内部通信端口                               | 8020 / 9000/9820 |
| NameNode HTTP UI                                   | 9870             |
| MapReduce查看执行任务端口                          | 8088             |
| 历史服务器通信端口                                 | 19888            |

# Spark运行架构

spark作为计算引擎，采用标准的master（Dirver，管理整个集群的作业调度）-slave（Excutor，实际执行任务）架构

![image-20230406123135710](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230406123135710.png)

## 核心组件:star:

计算核心组件：Driver和Excutor

### Driver（计算组件）

在整个的编程过程中没有看到任何有关Driver 的字眼。所以简单理解，所谓的 Driver 就是驱使整个应用运行起来的程序，**用于监控和调度Excutor**，也称之为Driver 类。

执行 Spark 任务中的 main 方法，负责实际代码的执行工作。

### Excutor（计算组件）

一个 <font color='red'>JVM 进程</font>，负责运行具体任务（Task），任务彼此之间相互独立。

负责运行组成 Spark 应用的任务，并将结果返回给驱动器进程
它们通过自身的块管理器（Block Manager）将**RDD缓存在Excutor进程内**，加速运算。

相关参数配置：

| 名称                                                         | 说明                          |
| ------------------------------------------------------------ | ----------------------------- |
| --num-excutors                                               | 配置excutor数量               |
| --excutor-memory                                             | 每个excutor内存大小           |
| --excutors-cores                                             | 每个excutors的**虚拟**CPU核数 |
| 并行度：整个集群并行执行任务的数量，实际占用的CPU**物理核数**，可以配置 |                               |

DAG（有向无环图）：程序执行的迭代计算的拓扑结构，支持job内部的DAG（不跨越job）

### master和worker（资源组件）

standalone模式下的资源调度：Master负责资源的调度和分配（Yarn的RM），worker运行在单个节点服务器上（Yarn的NM），负责根据Master的指令进行数据处理和计算。

### ApplicationMaster（连接计算和资源）

负责向RM申请Container，运行job，监控任务执行。解耦合RM（资源）和Driver（计算）

## Yarn提交流程:star:

分为申请资源和执行计算

Yarn模式下，Spark应用程序的部署执行方式分为Client和Cluster两种，**Client下Driver的执行位置是集群外Client上，充当client的角色；Cluster下Driver执行位置是集群里面，充当AM的角色**。

### Yarn Client(集群外运行Driver)

![image-20230408093552394](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230408093552394.png)

Client 模式将用于监控和调度的 Driver 模块在客户端执行，而不是在 Yarn 中，所以一般用于**测试**。

1)客户端提交一个application，在客户端启动driver。
2)Driver向RM发送请求，RM分配一个NM，在NM上启动AM，AM向RM请求一批Container，用于启动Executor。
3)AM会向NM发送命令启动Executor
4)Executor 进程启动后会向 Driver 反向注册，全部注册完成后 Driver 开始执行main 函数
5)之后执行到 Action 算子时，触发一个 Job，并根据宽依赖开始划分 stage，每个 stage 生成对应的 TaskSet，之后将 task 分发到各个 Executor 上执行

### Yarn Cluster(集群里运行Driver)

![image-20230408093652884](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230408093652884.png)

Cluster 模式将用于监控和调度的 Driver 模块启动在 Yarn 集群资源中执行。一般应用于**实际生产环境**。（AM就是driver）

1)客户端提交一个application，client向RM发送请求，申请AM，分配NM。
2)在NM上启动AM（driver），driver向RM请求一批Container，用于启动Executor。
3)driver会向NM发送命令启动Executor
4)Executor 进程启动后会向 Driver 反向注册，全部注册完成后 Driver 开始执行main 函数
5)之后执行到 Action 算子时，触发一个 Job，并根据宽依赖开始划分 stage，每个 stage 生成对应的 TaskSet，之后将 task 分发到各个 Executor 上执行

# Spark Core

三大数据结构：RDD : 弹性分布式数据集； 累加器：分布式共享**只写**变量； 广播变量：分布式共享**只读**变量

## RDD

### RDD特点与逻辑

RDD是spark中最基本的数据处理模型，**最小计算单元**，代表表一个弹性的、不可变、可分区、里面的元素**可并行计算**的集合。一个RDD能做的很少，需要多个RDD**组合完成复杂计算**。以wordCount为例，一层一层调用不同的RDD，实现计数逻辑。

RDD的数据处理方式类似于IO，也有**装饰者设计模式**；
RDD的数据只有在调用collect方法时，才会真正执行业务逻辑，之前只是创建好逻辑
RDD**不保存数据**，但IO可以暂存一部分数据

![image-20230406150432502](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230406150432502.png)

弹性：存储（内存磁盘自动切换）；容错（数据丢失可自动回复）；计算（计算出错重试）；分片（可根据需要重新分片）
分布式：存储在大数据集群不同节点上
数据集：**RDD封装了计算逻辑，并不保存数据**
数据抽象：RDD是一个抽象类，需要子类具体实现
不可变：RDD的计算逻辑不可改变，只能产生新的RDD
为了实现并行计算，是可分区，形成独立的task并行执行

### RDD核心5属性

| 源码介绍                                        | 属性                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| 分区列表 A list of partitions                   | 用于执行任务时并行计算，实现分布式计算                       |
| 分区计算函数A function for computing each split | 使用同样的分区函数对每一个分区进行计算                       |
| 依赖关系A list of dependencies on other RDDs    | RDD是计算模型的封装，当需求中需要将多个计算模型进行组合时，就需要将多个RDD建立依赖关系 |
| 可选，分区器Partitioner                         | 当数据为KV类型数据时，可以通过设定分区器**自定义数据的分区** |
| 可选，首选位置，preferred locations             | 判断Task发送到哪一个节点进行计算效率最优，发给有数据的节点效率最高，**移动数据不如移动计算** |

### 执行原理

Spark通过申请资源创建调度节点Driver和计算节点Excutor；Spark框架根据需求将计算逻辑根据分区划分成不同的任务；调度节点将任务根据计算节点状态发送到对应的计算节点进行计算。RDD主要将逻辑进行封装，生成Task发送给Excutor进行计算。

![image-20230406154259700](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230406154259700.png)

### 创建RDD && 并行度分区

1. 从集合中创建RDD：parallelize和makeRDD两个是一个方法。makeRDD第二个参数，表示**分区的数量**，默认是CPU可使用的最大核数12，可以在代码里配置。数据分区的方法，根据源码**partition方法**

```scala
 // 从内存中创建RDD，将内存中集合的数据作为处理的数据源
 val seq = List[Int](1,2,3,4)
 // parallelize : 并行
 // makeRDD方法在底层实现时其实就是调用了rdd对象的parallelize方法。
 //val rdd:RDD[Int] =  sc.parallelize(seq)
 val rdd:RDD[Int] = sc.makeRDD(seq)

		sparkConf.set("spark.default.parallelism", "5") // 设置默认分区数为5
		val rdd1 = sc.makeRDD(List(1,2,3,4,5),3)
		val rdd2 = sc.makeRDD(List(1,2,3,4)) // 默认是spark.default.parallelism的值，初始值CPU最大核数12
 // 将处理的数据保存成numslices个分区文件
 rdd1.saveAsTextFile("output")
// 数据分区的方法：根据源码，是对index划分[0,1) [1,3) [3,5)得到（1）（2，3）（4，5）
```

2. 从外部存储创建RDD，**textFile**可以读取本地文件、hadoop、HDFS的内容，以行为单位。如果一次性读取目录，为了区分内容所属的文件，可以使用**wholeTextFiles**，返回二元组（文件名，内容）。

   textFile第二个参数minPartitions表示最小分区，**Spark读取文件，底层其实使用的就是Hadoop的读取方式**，所以用的是textInputFormat的切片方式，一行一行读取，实际分区数量不一定= minPartitions

```scala
// 从文件中创建RDD，将文件中的数据作为处理的数据源
 // path路径默认以当前环境的根路径为基准。可以写绝对路径，也可以写相对路径
 //sc.textFile("D:\\mineworkspace\\idea\\classes\\atguigu-classes\\datas\\1.txt")
 val rdd: RDD[String] = sc.textFile("datas/1.txt")
 // path路径可以是文件的具体路径，也可以目录名称
 //val rdd = sc.textFile("datas")
 // path路径还可以使用通配符 *
 //val rdd = sc.textFile("datas/1*.txt")
 // path还可以是分布式存储系统路径：HDFS
// val rdd = sc.textFile("hdfs://linux1:8020/test.txt")
 rdd.collect().foreach(println)
// textFile : 以行为单位来读取数据，读取的数据都是字符串
// wholeTextFiles : 以文件为单位读取数据
//    读取的结果表示为元组，第一个元素表示文件路径，第二个元素表示文件内容
 val rdd = sc.wholeTextFiles("datas")

 rdd.collect().foreach(println)
```



## RDD算子

RDD方法两大类：转换（功能的补充和**封装**，将旧的RDD包装成新的RDD）
行动（**触发**任务的调度和作业的执行）

### transformer——Value类型

| Value算子                            | 返回类型                         | 功能                                                         |
| ------------------------------------ | -------------------------------- | ------------------------------------------------------------ |
| map                                  | RDD[Int]                         | **串行操作**，逐条进行**值**转换，或者**类型**转换 val makeRDD:RDD[Int] = rdd.map(x => x * 2)<br />逐条指的是，同一个分区内的数据有序执行全部逻辑，只有前一个数据的全部逻辑执行完才会轮到该分区的下一个数据执行；不同分区的数据无序执行 |
| ==mapPartitions==                    | RDD[Int]                         | 以分区为单位**批处理**（**缓冲区**），可以增加、修改、过滤数据。 |
| 对比map和mapPartitions               |                                  | map串行性能较低，mapPartitions分区批处理，性能高但是会长时间占用内存，可能内存溢出<br />map只是改变映射值，不改变数据数量；mapPartitions要求传入一个迭代器返回一个迭代器，数量可以增减 |
| mapPartitionsWithIndex               | **RDD[(Int,Int)]**               | 在分区处理基础上，可获得分区索引                             |
| flatMap                              | RDD[Int]                         | 多个list都转化为一个list中的元素                             |
| glom无参数                           | **RDD[Array[Int]]**              | 同一个分区的数据转为相同类型的array                          |
| <font color='red'>groupBy</font>     | **RDD[(Char,Iterable[String])]** | shuffle按照指定规则重新分组，分区不管                        |
| filter                               | RDD[Int]                         | 分区不变，但是不同分区内过滤剩下多少未知，可能会数据倾斜     |
| sample                               | RDD[Int]                         | 抽样（是否放回，抽取概率/次数，随机种子）                    |
| <font color='red'>distinct</font>    | RDD[Int]                         | 去重，用的不是hashMap，而是一个分布式reducebyKey             |
| coalesce                             | RDD[Int]                         | 缩减分区，当数据量小的时候，按照设定的缩减分区，但默认不会打乱重新组合。如果shuffle为true，是可以扩大分区的 |
| <font color='red'>repartition</font> | RDD[Int]                         | 一定shuffle，执行的就是coalesce。所以更适合扩大分区          |
| <font color='red'>sortBy</font>      | RDD[Int]                         | 一定shuffle，指定规则排序                                    |

```scala
val rdd:RDD[Int] = sc.makeRDD(List(1,2,3,4,5,6),2)
// 1.map算子    
val makeRDD:RDD[Int] = rdd.map(_* 2) 
// 2.mapPartitions算子，批量map
val rdd2:RDD[Int] = rdd.mapPartitions(_.map(_ * 2)) // 迭代器类型
val rdd3:RDD[Int] = rdd.mapPartitions(_.filter(_ % 2 == 1)) // mapPartitions算子，过滤
val rdd4:RDD[Int] = rdd.mapPartitions(iter => {
List(iter.max).iterator // 返回每个分区的最大值，3，6，需要给的是迭代器类型
})
rdd2.collect().foreach(println)
// 3.mapPartitionsWithIndex，批量map有分区索引
 // 只要第1个分区的数据
 val rdd5 = rdd.mapPartitionsWithIndex((index, data) => {
     if (index == 1) data
     else Nil.iterator
 })
 // 返回数据和分区号
 val rdd6:RDD[(Int,Int)]= rdd.mapPartitionsWithIndex((index,data) => {
     data.map(num => (index, num))
 })
// 4.flatMap：多类型的扁平化映射，match case
        val list1 = sc.makeRDD(List(List(1,2),List(4,5),3,List("Sta", "qwe")))
        val rdd7:RDD[Any] = list1.flatMap {data => {
            data match {
                case list: List[_] => list
                case i: Int => List(i)
            }
        }
// 5.glom：同一个分区的数据转为相同类型的array
        val glomRDD:RDD[Array[Int]] = rdd.glom()
        glomRDD.collect().foreach(data => println(data.mkString(",")))
// 取每个分区的最大值，取和
        val maxRDD:RDD[Int] = glomRDD.map(data => data.max) // 3,6
        val sumMax = maxRDD.sum()// 9
        maxRDD.collect().foreach(println)
        println("aaa+" + sumMax)
// 6.groupBy：将数据按照制定规则分组，需要将数据从原来的分区中打乱，重新组合（shuffle）分区和最后得到的group没有任何关系，shuffle打散了
        val rdd  = sc.makeRDD(List("Hello", "Spark", "Scala", "Hadoop"), 2)
        val groupRDD:RDD[(Char,Iterable[String])] = rdd.groupBy(_.charAt(0)) // H开头的一组，S开头的一组，虽然是不同分区，但是shuffle不考虑分区
// 7.filter,将数据根据指定的规则进行筛选过滤，符合规则的数据保留。当数据进行筛选过滤后，分区不变，但是分区内的数据可能不均衡，生产环境下，可能会出现数据倾斜
        val filterRDD: RDD[Int] = rdd.filter(num=>num%2!=0) // 保留奇数
        filterRDD.collect().foreach(println)
//8.sample
/* 
 1. 第一个参数表示withReplacement，抽取数据后是否将数据返回 true（放回），false（丢弃）
 2. 第二个参数表示fraction，
         如果抽取不放回的场合（伯努利）：数据源中每条数据被抽取的概率的基准值（不一定是这样的概率，只是基准）
         如果抽取放回的场合（泊松算法）：表示数据源中的每条数据被抽取的可能次数（基准值）
 3. 第三个参数表示seed，抽取数据时随机算法的种子，用于还原
*/ 
println(rdd.sample(false,0.2,1 ).collect().mkString(","))
// 9. distinct
val rdd1: RDD[Int] = rdd.distinct()
// 10. coalesce，默认情况下不会将分区的数据打乱重新组合，缩减分区可能会导致数据不均衡，可以进行shuffle处理让数据平衡
val newRDD: RDD[Int] = rdd.coalesce(2,true) // 缩减到两个分区，打散重组
// 11. repartiton，一个参数
val newRDD: RDD[Int] = rdd.repartition(3) // 相当于coalesce(3,true)
// 12.sortBy,可以根据指定的规则对数据源中的数据进行排序，默认为升序，第二个参数可以改变排序的方式
val rdd = sc.makeRDD(List(("1", 1), ("11", 2), ("2", 3)), 2)
val newRDD = rdd.sortBy(t=>t._1.toInt, false) // 按照rdd的字符串的int值降序排列

```

### transformer——双value类型

| 双value算子                       | 返回类型 | 功能                                                         |
| --------------------------------- | -------- | ------------------------------------------------------------ |
| intersection union  subtract  zip | RDD[Int] | 求交集，并集，差集，拉链。**zip不要求数据类型一致，其余均要求数据类型一致** |

```scala
// 13-16.交集，并集和差集要求两个数据源数据类型保持一致
        // 拉链操作两个数据源的类型可以不一致
        val rdd1 = sc.makeRDD(List(1,2,3,4))
        val rdd2 = sc.makeRDD(List(3,4,5,6))
        val rdd7 = sc.makeRDD(List("3","4","5","6"))

        // 交集 : 【3，4】
        val rdd3: RDD[Int] = rdd1.intersection(rdd2)

        // 并集 : 【1，2，3，4，3，4，5，6】
        val rdd4: RDD[Int] = rdd1.union(rdd2)

        // 差集 : 在1不在2中的【1，2】
        val rdd5: RDD[Int] = rdd1.subtract(rdd2)

        // 拉链 : 【1-3，2-4，3-5，4-6】
        val rdd6: RDD[(Int, Int)] = rdd1.zip(rdd2)
        val rdd8 = rdd1.zip(rdd7)
        println(rdd6.collect().mkString(","))
```

### transformer——key-value类型（都会shuffle）

| 算子                              | 返回值                                                       | 功能                                                         |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| partitionBy                       | RDD[(Int,Int)]                                               | 根据指定的分区规则对数据进行重分区<br />不是RDD.scala的方法，是PairRDDFunctions的方法，隐式转换 |
| reduceByKey                       | RDD[(String, Int)]                                           | 有shuffle，对相同key的value两两聚合，只对value聚合           |
| groupByKey                        | RDD[(String, Iterable[Int])]                                 | 有shuffle，相同key的数据分在一个组中                         |
| reduceByKey与reduceBy区别         | 前者固定按照key分组，后者需要指定；前者的迭代器中只有value，后者是Iterable(key,value) |                                                              |
| aggregateByKey                    | RDD[(String,Int)]不是与输入格式一致，而是与**初始值类型一致** | 根据不同的规则进行分区内计算和分区间计算                     |
| foldByKey                         | RDD[(String,Int)]                                            | 如果分区内和分区间规则相同，则变成reduceByKey，可以用foldByKey简化代码 |
| conbineByKey                      | 可以不与输入格式一致                                         | 最通用的对 key-value 型 rdd 进行聚集操作的聚集函数,允许value**返回值格式与输入不一致**，可以直接定义该格式，而不是通过初始值。 |
| sortByKey                         | RDD[(String,Int)]                                            | 类似于sortBy，只是这个不需要指定排序字段，是根据key排序      |
|                                   |                                                              |                                                              |
| 双key-Value类型                   | 返回值                                                       | 功能                                                         |
| join                              | 返回类型不记                                                 | sql inner join，join有笛卡尔积几何增长，还会shuffle，不推荐使用 |
| leftOuterJoin，也有rightOuterJoin | 返回类型不记                                                 | sql left join                                                |
| cogroup                           | 返回类型不记                                                 | 类似sql union，但没有笛卡尔积，对于每一个RDD，先按照key分组，再union连接 |



```scala
// 17.partitionBy,根据指定的分区规则对数据进行重分区
val rdd = sc.makeRDD(List(1,2,3,4),2)
        val mapRDD:RDD[(Int, Int)] = rdd.map((_,1))
        // RDD => PairRDDFunctions 隐式转换（二次编译）
		// HashPartitioner是一个分区器，进行重新分区
        val newRDD = mapRDD.partitionBy(new HashPartitioner(2))
        newRDD.partitionBy(new HashPartitioner(2))
// 18.reduceByKey：相同的key的数据进行value数据的聚合操作，两两聚合
 val rdd = sc.makeRDD(List( ("a", 1), ("a", 2), ("a", 3), ("b", 4)))
 val reducRDD:RDD[(String, Int)] = rdd.reduceByKey((x,y) => x+y, 2) // 2个分区
        reduceRDD.saveAsTextFile("output2")
// 19.groupByKey
        // groupByKey : 将数据源中的数据，相同key的数据分在一个组中，形成一个对偶元组
        //              元组中的第一个元素就是key，
        //              元组中的第二个元素就是相同key的value的集合
        val groupRDD: RDD[(String, Iterable[Int])] = rdd.groupByKey() // (a,CompactBuffer(1, 2, 3))

        val groupRDD1: RDD[(String, Iterable[(String, Int)])] = rdd.groupBy(_._1) // (a,CompactBuffer((a,1), (a,2), (a,3)))
// 20.aggregateByKey
        val rdd = sc.makeRDD(List(
            ("a", 1), ("a", 2), ("a", 3), ("a", 4)
        ),2)
        // aggregateByKey存在函数柯里化，有两个参数列表
        // 第一个参数列表,需要传递一个参数，表示为初始值
        //       主要用于分区内碰见第一个key的时候，和value进行计算
        // 第二个参数列表需要传递2个参数
        //      第一个参数表示分区内计算规则
        //      第二个参数表示分区间计算规则
        rdd.aggregateByKey(0)( // 第一个参数，初始值
            (x, y) => math.max(x, y), // 分区内最大值分别为2，4
            (x, y) => x + y // 分区间取和2+4=6
        ).collect.foreach(println) // ('a',6)
// 21.foldByKey ，以下三种结果等价
        val rddAggre:RDD[(String,Int)] = rdd.aggregateByKey(0)( // 分区内和分区间相同
            (x, y) => x +y,
            (x, y) => x + y
        )
        val rddFold:RDD[(String,Int)] = rdd.foldByKey(0)( // 两行变一行
            (x,y) => x + y
        )
        val rddRed:RDD[(String,Int)] = rdd.reduceByKey((x,y) => x + y) // 不是柯里化
// aggregateByKey补充，最终的返回数据结果应该和初始值的类型保持一致
        // 获取相同key的数据的平均值 => (a, 3),(b, 4)
        val newRDD : RDD[(String, (Int, Int))] = rdd.aggregateByKey( (0,0) )( // 值累加，出现次数累加
            ( t, v ) => { // t指代(0,0),v表示rdd相同key的value
                (t._1 + v, t._2 + 1)
            },
            (t1, t2) => { // 分区间将相同key的累加和与累加次数分别相加
                (t1._1 + t2._1, t1._2 + t2._2)
            }
        )
        val resultRDD:RDD[(String,Int)] = newRDD.mapValues{ // mapValues相同key只对value操作，不能用map，map要求输入输出类型一致
            case (num, cnt) => num /cnt
        }
// 22.combineByKey : 方法需要三个参数
        // 第一个参数表示：将相同key的第一个数据进行结构的转换，实现操作
        // 第二个参数表示：分区内的计算规则
        // 第三个参数表示：分区间的计算规则
        val newRDD : RDD[(String, (Int, Int))] = rdd.combineByKey(
            v => (v, 1), // 对key的value值进行转化，变成（值，次数）
            ( t:(Int, Int), v ) => { // t的类型就是刚才定义的变换后的类型
                (t._1 + v, t._2 + 1)
            },// 这些都与上面的aggregateByKey类似
            (t1:(Int, Int), t2:(Int, Int)) => {
                (t1._1 + t2._1, t1._2 + t2._2)
            }
        )
        val resultRDD: RDD[(String, Int)] = newRDD.mapValues {
            case (num, cnt) => {
                num / cnt
            }
        }
        resultRDD.collect().foreach(println)
// 23. sortByKey，一个参数，升序或者降序
        val dataRDD1 = sc.makeRDD(List(("a",1),("b",2),("c",3)))
        val sortRDD11: RDD[(String, Int)] = dataRDD1.sortByKey(true)
        val sortRDD12: RDD[(String, Int)] = dataRDD1.sortByKey(false)
        var sortBy:RDD[(String,Int)] = dataRDD1.sortBy(t => t._1,false) // 与上一行相同，都是key降序排列
// 24. join，sql内连接
        val rdd1 = sc.makeRDD(List( ("a", 1), ("a", 2), ("c", 3)))
        val rdd2 = sc.makeRDD(List(("a", 5), ("c", 6),("a", 4)))
        // join : 两个不同数据源的数据，相同的key的value会连接在一起，形成元组
        //        如果两个数据源中key没有匹配上，那么数据不会出现在结果中
        //        如果两个数据源中key有多个相同的，会依次匹配，可能会出现笛卡尔乘积，数据量会几何性增长，会导致性能降低。
        val joinRDD: RDD[(String, (Int, Int))] = rdd1.join(rdd2)
// 25.leftOuterJoin 左外连接，左边的为主，有的话就连Some，没有的话就None
        val rdd1 = sc.makeRDD(List( ("a", 1), ("b", 2), ("d", 6)))
        val rdd2 = sc.makeRDD(List(("a", 5), ("c", 6),("a", 4)))
 val leftJoinRDD = rdd1.leftOuterJoin(rdd2) // (a,(1,Some(4))) (a,(1,Some(6))) (b,(2,Some(5))) (d,(6,None))
// 26. cogroup 分组+union
        val rdd1 = sc.makeRDD(List(("a", 1), ("b", 2), ("a", 6)))
        val rdd2 = sc.makeRDD(List(("a", 4), ("b", 5),("c", 6),("c", 7)))
        val cgRDD: RDD[(String, (Iterable[Int], Iterable[Int]))] = rdd1.cogroup(rdd2)
// (a,(CompactBuffer(1, 6),CompactBuffer(4)))
// (b,(CompactBuffer(2),CompactBuffer(5)))
// (c,(CompactBuffer(),CompactBuffer(6, 7)))
```

### 算子区别

reduceByKey aggregateByKey foldByKey combineByKey的区别：

调用的底层方法完全一样，都是combineByKeyWithClassTag

| 算子           | 第一个值                                                     | 分区内与分区间规则 |
| -------------- | ------------------------------------------------------------ | ------------------ |
| reduceByKey    | 不计算，value直接放进去，所以输入输出类型相同                | 相同               |
| FoldByKey      | 可以计算，分区内数据与初始值计算，输出类型=初始值类型        | 相同               |
| AggregateByKey | 同上                                                         | 可以不同           |
| CombineByKey   | 发现数据结构不满足要求时，可以让第一个数据转换结构，不是通过设定初始值，而且直接转换第一个<key,value>的value结构 | 可以不同           |

map VS mapPartitions：

map串行性能较低，mapPartitions分区批处理，性能高但是会长时间占用内存，可能内存溢出<br />map只是改变映射值，不改变数据数量；mapPartitions要求传入一个迭代器返回一个迭代器，数量可以增减



reduceByKey VS reduceBy：

前者固定按照key分组，后者需要指定；前者的迭代器中只有value，后者是Iterable(key,value)



groupByKey VS reduceByKey：

shuffle操作必须落盘（写入磁盘），不能让他在Excutor的内存中等待，否则会内存溢出。shuffle性能低，因为要与磁盘交互。
shuffle角度：都会shuffle，但是reduceByKey有分区内的预聚合（combiner），落盘的数据量减少，会快一些；groupByKey落盘的数据量不变 。
功能角度：reduceByKey是分组+聚合，groupByKey分组不聚合

### action算子

触发job执行的方法，比如collect

| action算子       | 输入输出                                                     | 功能                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ==reduce==       | RDD[Int] => Int                                              | 聚集 RDD 中的所有元素，先聚合分区内数据，再聚合分区间数据，同一操作 |
| ==collect==      | RDD[Int] =>Array[Int]                                        | 不同分区的数据连接为数组                                     |
| count            | RDD[Int] =>Int                                               | 数据源中数据的个数                                           |
| first            | RDD[Int] =>Int                                               | 数据源中数据的第一个                                         |
| take             | RDD[Int] =>Array[Int]                                        | 获取N个数据                                                  |
| takeOrdered      | RDD[Int] =>Array[Int]                                        | 正序排序后，获取N个数据                                      |
| aggregate        | RDD[Int] =>Int                                               | 有初始值，分区内和分区间不同规则的合并计算，初始值在分区内和分区间都生效 |
| fold             | RDD[Int] =>Int                                               |                                                              |
| ==countByValue== | RDD[Int] =>RDD[(String,Int)]                                 | 统计一个值出现的次数                                         |
| ==countByKey==   | RDD[(String,Int)]=>RDD[(String,Int)]                         | 统计key出现的次数                                            |
| save算子三个     | saveAsTextFile保存为text，能够看懂；saveAsObjectFile保存为对象，saveAsSequenceFile保存为序列化文件都看不懂 |                                                              |
| ==foreach==      | 分布式遍历 RDD 中的每一个元素，调用指定函数                  | rdd.foreach(println)                                         |

```scala
 val rdd = sc.makeRDD(List(1,2,3,4),2)
        // 1.reduce，聚集 RDD 中的所有元素，先聚合分区内数据，再聚合分区间数据，同一操作
        val i: Int = rdd.reduce(_+_)
        println(i) // 10

        // 2.collect : 方法会将不同分区的数据按照分区顺序采集到Driver端内存中，形成数组
        val intss: Array[Int] = rdd.collect()
        println(intss.mkString(",")) // 1,2,3,4

        // 3.count : 数据源中数据的个数
        val cnt = rdd.count()
        println(cnt) // 4

        // 4.first : 获取数据源中数据的第一个
        val first = rdd.first()
        println(first) // 1

        // 5.take : 获取N个数据
        val ints: Array[Int] = rdd.take(3)
        println(ints.mkString(",")) // 1,2,3

        // 6.takeOrdered : 数据排序后，取N个数据
        val rdd1 = sc.makeRDD(List(4,2,3,1))
        val ints1: Array[Int] = rdd1.takeOrdered(3)
        println(ints1.mkString(",")) // 1,2,3

        // aggregateByKey : 初始值只会参与分区内计算
        // 7.aggregate : 初始值会参与分区内计算,并且参与分区间计算
        val aggregate = rdd.aggregate(10)(_+_, _+_) // (10+1+2) + (10+3+4) + 10 = 40
        // 8. fold
        val fold = rdd.fold(10)(_+_)

        // 9.countByValue 统计一个值出现的次数（value，time）
        val rdd = sc.makeRDD(List(1,1,1,4),2)
        val rdd2 = sc.makeRDD(List(
            ("a", 1),("a", 2),("a", 3)
        ))
       val countByValue: collection.Map[Int, Long] = rdd.countByValue()
        println(countByValue) // Map(4 -> 1, 1 -> 3)
		// 10.countByKey 统计key出现的次数（key，time）
        val countByKey: collection.Map[String, Long] = rdd2.countByKey()
        println(countByKey) // Map(a -> 3)

```



```scala
        val rdd = sc.makeRDD(List[Int](1,2,3,4))
        val user = new User()
        // RDD算子中传递的函数是会包含闭包操作，那么就会进行检测功能
        // 闭包检测
        rdd.foreach( num => {println("age = " + (user.age + num))})
        sc.stop()
    }
    // 样例类在编译时，会自动混入序列化特质（实现可序列化接口）
//    case class User() {
    class User extends Serializable {
        // 在Driver端构建一个user，action算子内部在excutor执行，Driver和excutor的数据网络传输过去必须序列化
        var age : Int = 30
    }
```

### RDD序列化

 **<font color='red'>算子以外的代码都是在 Driver 端执行, 算子里面的代码都是在 Executor端执行</font>**。那么在 scala 的函数式编程中，就会导致算子内经常会用到算子外的数据，这样就形成了闭包的效果。**闭包检测**：要在执行任务计算前，检测闭包内的对象是否可以进行序列化。

**类的构造参数其实是类的属性**, 构造参数需要进行闭包检测，其实就等同于类进行闭包检测，所以要记得extends Serializable

Java序列化很重，产生的参数多。Spark2.0 开始支持另外一种 Kryo 序列化机制。Kryo 速度是 Serializable 的 10 倍。当 RDD 在 Shuffle 数据的时候，简单数据类型、数组和字符串类型已经在 Spark 内部使用 Kryo 来序列化。

### 依赖关系

相邻两个RDD的关系是**依赖**：A用到B，则A依赖B（新的RDD依赖旧的RDD，rdd1 = rdd.map(_ * 2),rdd1依赖rdd）
多个连续的RDD的依赖关系是**血缘**：A->B->C->D，A与D血缘
每个RDD会保存血缘关系，用于**恢复丢失的分区**。RDD不保存数据，当某一步骤出错时，依靠血缘关系重新读取数据源并计算

每走一步，血缘关系都会多一层(toDebugString查看)
RDD依赖关系(dependencies查看)

```
val lines: RDD[String] = sc.textFile("datas/1.txt")
println(lines.dependencies)
```

![image-20230407091853071](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230407091853071.png)

#### 宽窄依赖

OneToOne依赖（窄依赖）：新的RDD（下游，子RDD）的一个分区的数据依赖于旧的RDD（上游，父RDD）一个分区的数据，即每一个父上游RDD的分区最多被子下游RDD的一个分区使用。
shuffle依赖（宽依赖）：新的RDD（下游）的一个分区的数据依赖于旧的RDD多个分区的数据，每一个父上游RDD的分区可以被子下游RDD的多个分区使用

宽依赖算子：groupBy distinct repartition sortBy

 groupByKey reduceByKey sortByKey aggregateByKey conbineByKey

join

#### 阶段划分和任务划分

窄依赖不需要划分阶段，两个分区没有交叉操作，不需要彼此等待，那就每个分区独立运行自己的一串任务。但是宽依赖需要划分阶段，只要判断出来存在shuffle依赖，阶段数量自动+1，所以**阶段数量= shuffle依赖数量+1**，1表示初始的resultStage。

任务划分：

RDD 任务切分中间分为：Application、Job、Stage 和 Task
Application：初始化一个 SparkContext 即生成一个 Application
Job：一个 Action 算子就会生成一个 Job；
Stage：阶段数量= shuffle依赖数量+1；
Task：一个 Stage 阶段中，Task 的个数=到目前为止最新的RDD的分区个数
**Application->Job->Stage->Task 每一层都是 1 对 n 的关系。**Application与Job多个行动算子；Job与Stage多个shuffle依赖；Stage与Task多个分区。

#### RDD持久化

RDD不存储数据，如果需要重复使用该RDD对象mapRDD，需要从头开始获取数据（所谓的重用只是少写几行代码，但内部还是从头到尾执行了两遍）
如果想重复使用，或者数据过大一旦出错重新加载太耗时，为了**重用或出错时不从头获取**，那就另外缓存在**内存或磁盘**。这个持久化作业完成后就删除。

```scala
mapRDD.cache() // cache默认持久化的操作，只能将数据保存到内存中
mapRDD.persist(StorageLevel.DISK_ONLY) // 如果想要保存到磁盘文件，需要更改存储级别。持久化操作必须在行动算子执行时完成的。
 mapRDD.checkpoint() // 一般保存路径都是在分布式存储系统：HDFS
```

![image-20230407100034019](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230407100034019.png)

RDD CheckPoint 检查点：将RDD中间结果落盘（写入磁盘），需要指定检查点保存路径，作业执行完也不会删除。

三者区别：

|            | 定义                                                         | 优点     | 缺点                                                         |
| ---------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| cache      | 将数据临时存储在内存中进行数据重用<br />会在血缘关系中添加新的依赖。一旦，出现问题，可以重头读取数据 |          | 数据不安全                                                   |
| persist    | 将数据**临时**存储在磁盘文件中重用，job完之后删除，多个job不可共享 | 数据安全 | 涉及到磁盘IO，性能较低                                       |
| checkpoint | 将数据**长久**保存在磁盘文件中重用，多个job共享<br />需要和cache联合使用，先cache再checkpoint 提高效率<br />执行过程中，会<font color='red'>**切断血缘关系**</font>。重新建立新的血缘关系，checkpoint等同于改变数据源（不再从头读，从这里读） | 数据安全 | 涉及到磁盘IO，性能较低<br />不与cache联用时，独立执行作业，没有真正复用，性能太低 |

#### RDD分区器

只有 Key-Value 类型的 RDD 才有分区器，非 Key-Value 类型的 RDD 分区的值是 None
每个 RDD 的分区 ID 范围：0 ~ (numPartitions - 1)，决定这个值是属于那个分区的。

支持Hash分区（HashPartitioner）：对于给定的 key，计算其 hashCode,并除以分区个数取余。比如new HashPartitioner(3)，计算出来每个的HashCode，根据mod放到0,1,2三个分区中

Range分区（RangePartitioner）：将一定范围内的数据映射到一个分区中，尽量保证每个分区数据均匀，而且分区间有序

自定义分区：

```scala
    class MyPartitioner extends Partitioner{
        // 分区数量
        override def numPartitions: Int = 3
        // 根据数据的key值返回数据所在的分区索引（从0开始）
        override def getPartition(key: Any): Int = {
            key match {
                case "nba" => 0
                case "wnba" => 1
                case _ => 2
            }
        }
    }
```

## 累加器：分布式共享**只写**变量

分布式的改变，聚合这些改变

累加器用来把 **Executor 端变量信息传回给Driver 端并聚合**。在 Driver 程序中定义的变量，在Executor 端的每个 Task 都会得到这个变量的一份新的副本，每个 task 更新这些副本的值后，传回 Driver 端进行 merge。

一般放在行动算子中调用累加器，而不是转换算子（会少加map或者多加collect）

```scala
// 1.系统累加器
val sum = sc.longAccumulator("sum1") // 定义long数值类型的累加器
        rdd.foreach(
            num => {
                sum .add(num) // 调用方法add
            }
        )
        println("sum = " + sum.value) // 获取累加器的value,1+2+3=4 = 10
        println("sum = " + sum.avg) // 获取均值，2.5
// 2.自定义累加器
object Spark04_Acc_WordCount {
    def main(args: Array[String]): Unit = {
        val sparConf = new SparkConf().setMaster("local").setAppName("Acc")
        val sc = new SparkContext(sparConf)
        val rdd = sc.makeRDD(List("hello", "com/atguigu/bigdata/spark", "hello"))
        // 累加器 : WordCount
        // 1）创建累加器对象
        val wcAcc = new MyAccumulator()
        // 2）向Spark进行注册
        sc.register(wcAcc, "wordCountAcc") // 放入对象和名字，名字随便起
        rdd.foreach(
            word => {
                wcAcc.add(word)// 数据的累加（使用累加器）
            }
        )
        println(wcAcc.value)// 获取累加器累加的结果
        sc.stop()
    }
    /*
      自定义数据累加器：WordCount
      1. 继承AccumulatorV2, 定义泛型
         IN : 累加器输入的数据类型 String
         OUT : 累加器返回的数据类型 mutable.Map[String, Long]
      2. 抽象类，重写方法（6）
     */
    class MyAccumulator extends AccumulatorV2[String, mutable.Map[String, Long]] {
        private var wcMap = mutable.Map[String, Long]()
        // 判断是否初始状态
        override def isZero: Boolean = {
            wcMap.isEmpty
        }
        override def copy(): AccumulatorV2[String, mutable.Map[String, Long]] = {
            new MyAccumulator()
        }
        override def reset(): Unit = {
            wcMap.clear()
        }
        // 重点：获取累加器需要计算的值
        override def add(word: String): Unit = {
            val newCnt = wcMap.getOrElse(word, 0L) + 1
            wcMap.update(word, newCnt)
        }
        // 重点：Driver合并多个累加器
        override def merge(other: AccumulatorV2[String, mutable.Map[String, Long]]): Unit = {
            val map1 = this.wcMap
            val map2 = other.value
            map2.foreach{
                case ( word, count ) => {
                    val newCount = map1.getOrElse(word, 0L) + count
                    map1.update(word, newCount)
                }
            }
        }
        // 累加器结果
        override def value: mutable.Map[String, Long] = {
            wcMap
        }
    }
}
```

## 广播变量：分布式共享**只读**变量

**不可变，只读的，相同**的变量，该节点每个任务都能访问，起到节省资源和优化的作用。

闭包数据是以task为单位发送，每个任务都包含闭包数据，这样会导致，一个Excutor含有大量重复的数据，每个task都存了一份重复的在Excutor中。

广播变量可以将闭包数据保存在Excutor内存中，用于Excutor内Task共享，但**不可以更改**

![image-20230407110801843](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230407110801843.png)

```
 // 封装广播变量Map类型，不能修改mapVar了，使用时候是bc.value. | getOrElse | get
        val bc: Broadcast[mutable.Map[String, Int]] = sc.broadcast(mapVar)
```

# Spark SQL

Shark 的出现，使得 SQL-on-Hadoop 的性能比 Hive 有了 10-100 倍的提高。但Shark太依赖Hive，发展受限于Hive架构，因此开发了SparkSQL，不再受限于 Hive，只是兼容 Hive。SparkSQL 可以简化 RDD 的开发，提高开发效率，提供了两个编程抽象：DataFrame和DataSet

特点：

易整合：无缝的整合了 SQL 查询和 Spark 编程
统一的数据访问：使用相同的方式连接不同的数据源（HBase Hive MySQL）
兼容 Hive：在已有的仓库上直接运行 SQL 或者 HiveQL
标准数据连接：通过 JDBC 或者 ODBC 来连接

DataFrame：以 RDD 为基础的分布式数据集，类似于**二维表格**。比起RDD只关心数据和基于数据的变换，Spark Core 只能在 stage 层面进行简单、**通用的流水线**优化。DataFrame更关心**数据的结构**，能洞察更多的数据结构信息。DataFrame为数据提供了 **Schema 的视图**，可以把它当做数据库中的一张表来对待。
DataFrame 也是懒执行的，但性能上比 RDD 要高，其**查询计划**通过 Spark catalyst optimiser 进行**优化**

![image-20230407112647633](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230407112647633.png)

DataSet：SparkSQL 最新的数据抽象

用户友好的 API 风格，既具有类型安全检查也具有 DataFrame 的查询优化特性；
用样例类来对 DataSet 中定义数据的结构信息，样例类中每个**属性的名称**直接映射到DataSet 中的**字段名称**；
DataSet 是强类型的。比如可以有 DataSet[Car]，DataSet[Person]。
DataFrame 是 DataSet 的特列，DataFrame=DataSet[Row] ，所以可以通过 as 方法将DataFrame 转换为 DataSet。Row 是一个类型，跟 Car、Person 这些的类型一样，所有的表结构信息都用 Row 来表示。获取数据时需要指定顺序

```scala
val sparkConf = new SparkConf().setMaster("local[*]").setAppName("sparkSQL")
val spark = SparkSession.builder().config(sparkConf).getOrCreate() // 用SparkSession而不是SparkContext
val dfdf: DataFrame = spark.read.json("datas/user.json") // {}{}{}不能有逗号，方括号之类的
// 创建普通临时表，仅有本session的sql可以访问到
dfdf.createTempView("user1") 
spark.sql("select sum(age) from user1").show // 直接用sql语句得到age平均值
// 创建全局临时表，访问时需要使用全路径global_temp.user2访问，newSession可以访问到
dfdf.createGlobalTempView("user2") 
        spark.sql("select sum(age) from global_temp.user2").show()
        spark.newSession().sql("select sum(age) from global_temp.user2").show()
```

## DSL

不需要创建视图，本身把dataFrame当作数据源。直接调用这个语言就可以管理数据结构

```scala
        val dfdf: DataFrame = spark.read.json("datas/user.json")
        // 1.查看 DataFrame 的 Schema 信息
        dfdf.printSchema()
        // 2.涉及到运算的时候, 每列都必须使用$, 或者采用引号表达式：单引号+字段名
        dfdf.select($"name", $"age" + 1).show()
        dfdf.select('age + 1).show()
        // 3.过滤
        dfdf.filter($"age" > 10).show()
        // 4.根据age分组，查看每组的count
        dfdf.groupBy("age").count.show()
```

## RDD与DF、DS

```scala
  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setMaster("local[*]").setAppName("sparkSQL")
    val sc = new SparkContext(sparkConf)
    val spark = SparkSession.builder().config(sparkConf).getOrCreate()
    import spark.implicits._ // 创建sparkSession后直接导入
    // 上面这几行一定要有
    val rdd = sc.makeRDD(List(("1","张三",5),("2","李四",30),("3","王五",100)))
    val df = rdd.toDF("id", "name","salary") // RDD转为dataFrame
    df.show()
    val toto = df.rdd.collect() // dataFrame转为RDD
    toto.foreach(println)
  }
```

转化：

![image-20230407145023183](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230407145023183.png)

```scala
val df = rdd.toDF("id", "name","salary") // RDD => dataFrame
val ds = rdd.map( t => Person(t._1, t._2)).toDS() // RDD => DS
val toto = df.rdd.collect() // dataFrame => RDD
val rdd1 = ds.rdd.collect()  // DS => RDD
val ds = df.as[Student] // DateFrame => dataSet
ds.toDF() // dataSet => DateFrame
val rdd = sc.makeRDD(List(("zhangsan",10), ("lisi",100)))
    

```

三者关系：如果同样的数据都给到这三个数据结构，他们分别计算之后，都会给出相同的结果。不同是的他们的执行效率和执行方式。

相同点：都有惰性机制，延迟执行，直到遇见action算子或foreach才会执行。
在对DataFrame和Dataset进行操作许多操作都需要这个包:importspark.implicits._（**在创建好SparkSession对象后尽量直接导入**）
三者都会根据Spark 的内存情况自动缓存运算，这样即使数据量很大，也不用担心会内存溢出
DataFrame和DataSet均可使用模式匹配获取各个字段的值和类型

不同点：

1）RDD一般和spark mllib同时使用；RDD不支持sparksql操作
2）DataFrame每一行的类型固定为Row，每一列的值没法直接访问，只有通过解析才能获取各个字段的值；
DataFrame与DataSet一般不与spark mllib 同时使用
DataFrame与DataSet均支持SparkSQL 的操作，比如select，groupby之类，还能注册临时表/视窗，进行sql 语句操作
DataFrame与DataSet支持一些特别方便的保存方式，比如保存成csv，可以带上表头，这样每一列的字段名一目了然(后面专门讲解)
3）DataFrame其实就是DataSet的一个特例，拥有完全相同的成员函数，区别只是每一行的数据类型不同，type DataFrame = Dataset[Row]

## UDF

```scala
        val df = spark.read.json("datas/user.json")
        df.createOrReplaceTempView("user")
        // 自定义一个函数，prefixName，给name加前缀
        spark.udf.register("prefixName", (name:String) => {
            "Name: " + name
        })
        // 自定义函数，将 age+10
        spark.udf.register("changeAge", (age:Int) => age + 10)
        spark.sql("select prefixName(username), changeAge(age) from user").show()
```

强类型的 Dataset 和弱类型的 DataFrame 都提供了相关的聚合函数， 如 count()，countDistinct()，avg()，max()，min()。除此之外，用户可以设定自己的自定义聚合函数。通过继承 UserDefinedAggregateFunction 来实现用户自定义弱类型聚合函数。从 Spark3.0 版本
后，UserDefinedAggregateFunction 已经不推荐使用了。可以统一采用强类型聚合函数Aggregator

UDAF弱类型继承UserDefinedAggregateFunction，重写6个方法，然后spark.udf.register("ageAvg", new MyAvgUDAF1())使用自定义的UDAF。

Spark3.0 版本可以采用强类型的 Aggregator 方式代替 UserDefinedAggregateFunction

## 数据加载和保存

spark.read.load 是加载数据的通用方法。df.write.save 是保存数据的通用方法

format("…")：指定加载的数据类型，包括"csv"、"jdbc"、"json"、"orc"、"parquet"和"textFile"。
load("…")：在"csv"、"jdbc"、"json"、"orc"、"parquet"和"textFile"格式下需要传入加载数据的路径。

csv format jdbc json load option options orc parquet schema 

```scala
// 1.json加载
val path = "/opt/module/spark-local/people.json"
val peopleDF = spark.read.json(path) 
// 2.csv加载
spark.read.format("csv").option("sep",";").option("inferSchema","true").option("header","true").load("data/user.csv")
// 3.mysql加载
spark.read.format("jdbc").option("url", "jdbc:mysql://linux1:3306/spark-sql").option("driver", com.mysql.jdbc.Driver")
// 4.Hive下载上传
```

# Spark Streaming

准实时（秒，分钟，没有达到真正的实时即毫秒，但也不是离线的小时天级别）、微批次（不是来一条处理一条，设定时间区间处理数据）的数据处理框架。

DStream 是随时间推移而收到的数据的序列。**DStream 是一系列连续的 RDD 来表示，每个 RDD 含有一段时间间隔内的数据**。DStream 就是对 RDD 在实时数据处理场景的一种封装。

特点：易用性、容错机制、易整合到spark体系

背压机制（spark.streaming.backpressure.enabled）：动态控制数据接收速率来适配集群数据处理能力。**根据JobScheduler 反馈作业的数据处理能力来动态调整 Receiver 数据接收率**

由于SparkStreaming采集器是长期执行的任务，所以不能直接关闭，ssc.stop()注释掉。

计算过程是由spark engine完成

       val ssc = new StreamingContext(sparkConf, Seconds(3))
       // 由于SparkStreaming采集器是长期执行的任务，所以不能直接关闭
        // 如果main方法执行完毕，应用程序也会自动结束。所以不能让main执行完毕
        //ssc.stop()
        // 1. 启动采集器
        ssc.start()
        // 2. 等待采集器的关闭
        ssc.awaitTermination()

## RDD队列

通过使用 ssc.queueStream(queueOfRDDs)来创建 DStream，每一个推送到这个队列中的 RDD，都会作为一个 DStream 处理。

## Kafka数据源

ReceiverAPI：需要一个专门的 Executor 去接收数据，接收数据的 Executor 和计算的 Executor 速度会有所不同，特别在接收数据的 Executor，当前版本不适用
**Direct**API：由计算的 Executor 来主动消费 Kafka 的数据，速度由自身控制。

## Dstream转化

旧的Dstream转换为新的Dstream，分为无状态操作（不需要保存前一个周期的数据，只对当前采集周期内的数据进行处理，之前那些算子都是无状态，mop flatmap redceByKey filter），有状态操作（UpdateStateByKey WindowOperations）

对于map操作，不管前一个周期的数据是不会出错的，但是对于reduceByKey，肯定是需要得到前面周期的聚合结果，所以用UpdataStateByKey，需要设定检查点路径
Window Operations 可以设置窗口的大小和滑动窗口的间隔来动态的获取当前 Steaming 的允许状态。

所有基于窗口的操作都需要两个参数，分别为**窗口时长**（计算内容的时间范围，将多长时间的作为一个整体计算）以及**滑动步长**（多久触发一次计算），这两者必须都是采集周期大小的整数倍。滑动步长>=窗口时长，不会有重复数据

```scala
// 1.updateStateByKey：根据key对数据的状态进行更新
// 传递的参数中含有两个值
// 第一个值表示相同的key的value数据
// 第二个值表示缓存区相同key的value数据
// 需要设置checkpoint的路径
val state = wordToOne.updateStateByKey(
    ( seq:Seq[Int], buff:Option[Int] ) => {
        val newCount = buff.getOrElse(0) + seq.sum
        Option(newCount)
    }
)
// 2.WindowOperations

```

## 优雅关闭

对立面”强制关闭“，可能会涉及到一些原子操作强制中断会有问题。

优雅关闭：计算节点不在接收新的数据，而是将现有的数据处理完毕，然后关闭

如果想要关闭采集器，那么需要创建新的线程，而且需要在第三方程序中（mysql的表）增加关闭状态。

ssc.stop(true, true) // 第二个参数就是优雅关闭

# spark3.x调优

## 算子调优

使用reduceByKey/aggregateByKey替代groupByKey，进行预聚合，减少数据量
使用mapPartitions替代普通map 批处理代替串行
使用foreachPartitions替代foreach 优化写数据库操作，forEach对每条数据都建立一个数据库连接，foreachPartition对一个分区的数据只建立一个数据库连接。
filter和coalesce配合使用：filter后的数据可能数据倾斜，所以用coalesce缩小分区，尽量不shuffle

## 资源调优

### 最优资源配置（提交作业的参数）

driver /executor的内存/内核数   executor个数

增加excutorCPU内核数：--executor-cores 3
增加Excutor个数：--num-executors  50-100
增加每个 Executor 的内存：--executor-memory  6-10G
增加driver内存（影响小）：driver-memory 1-5G
driver使用内核数：driver-cores —— 默认为1

### RDD优化

RDD复用：在对 RDD 进行算子时，要避免相同的算子和计算逻辑之下对 RDD 进行重复的计算
RDD持久化：cache checkpoint
尽早filter：尽早过滤掉不需要的数据，减少内存占用

### 并行度调节

并行度：各个 stage 的 task 的数量。**task 数量应该设置为 Spark 作业总 CPU core 数量的 2~3 倍**，一个CPU处理2-3个task。

### 广播大变量

所有 task 共用此广播变量，这让变量产生的副本数量大大减少

### Kryo 序列化

Java序列化很重，产生的参数多。Spark2.0 开始支持另外一种 Kryo 序列化机制。Kryo 速度是 Serializable 的 10 倍。当 RDD 在 Shuffle 数据的时候，简单数据类型、数组和字符串类型已经在 Spark 内部使用 Kryo 来序列化。

## SparkSql语法优化

SparkSQL在整个执行计划处理的过程中，使用了**Catalyst 优化器**。

### RBO的优化

谓词下推：将过滤条件的谓词逻辑都尽可能提前执行，减少下游处理的数据量。on比where提前执行，所以join的时候使用on
列剪裁：只读取查询相关字段，不select *
常量替换：Catalyst会自动将常量表达式替换为常量表达式的结果（12 + 18 => 30）

## 数据倾斜

数据倾斜一般是发生在shuffle类的算子，比如distinct、groupByKey、reduceByKey、aggregateByKey、join、cogroup等，涉及到数据重分区，如果其中某一个key数量特别大，就发生了数据倾斜。

### 数据倾斜大key定位

定位方法——用**局部取推算整体**：从所有key中，把其中每一个key随机取出来一部分，然后进行一个百分比的推算

当某个key导致数据倾斜时，将发生数据倾斜的key提取出来单独处理，加随机数打散，如果允许删除的话，直接过滤掉这些key

### map端预聚合

为减少shuffle和reduce的压力，spark sql会在map端做预聚合，提前对map的结果做combine减少数据量

### 广播join mapjoin

广播join：小表join大表的情况，可以规避掉shuffle，减少stage。<font color='red'>广播join也是Spark Sql中最常用的优化方案</font>

小表join大表，可以先将小表缓存到内存中，那么可以使用Broadcast Hash Join,其原理就是先将小表聚合到driver端，再广播到各个大表分区中，那么再次进行join的时候，就相当于大表的各自分区的数据与小表进行本地join，从而规避了shuffle。

### 提高reduce的并行度

 reduce端并行度的提高就增加了 reduce端 task的数量，那么每个task的数据量减少

## Map优化（属于job优化）

### map-side预聚合

类似combiner

### 读取小文件优化

将多个小文件读入同一个分区

### 增大map溢写时输出流buffer

1）map 端 Shuffle Write 有一个缓冲区，初始阈值 5m，超过会尝试增加到 2*当前使用内存。如果申请不到内存，则进行溢写。这个参数是 internal，指定无效。也就是说资源足够会自动扩容，所以不需要我们去设置。
2）**调节 map 端输出缓冲区大小，默认配置是 32KB，可增大**。这些缓冲区减少了磁盘搜索和系统调用次数，适当提高可以提升溢写效率。
3）Shuffle 文件涉及到序列化，是采取批的方式读写，默认按照每批次 1 万条去读写。设置得太低会导致在序列化时过度复制，因为一些序列化器通过增长和复制的方式来翻倍内部数据结构。这个参数是 internal，指定无效。

## Reduce优化（属于job优化）

reduce端有shuffle read操作，从上游stage的所有task节点上拉取属于自己的磁盘文件

调节 reduce 端拉取数据缓冲区大小，默认48M，可增大
调节 reduce 端拉取数据重试次数，默认3，如果性能好，可减小；如果网络不好，可增大
调节 reduce 端拉取数据等待间隔，默认5s，为了增加shuffle操作稳定性，可增大
调节 SortShuffle 排序操作阈值，默认200，如果不需要排序，可增大

## Shuffle优化

其实在上面两部分都有，再整合一次。shuffle过程分为shuffle write（map）和shuffle read（reduce）两部分。

shuffle过程中，各个节点会通过shuffle write过程将相同key都会先写入本地磁盘文件中，然后其他节点的shuffle read过程通过网络传输拉取各个节点上的磁盘文件中的相同key。这其中**大量数据交换涉及到的网络传输和文件读写操作**是shuffle操作十分耗时的根本原因。

选择合适的shuffle类型：spark.shuffle.manager用于设置ShuffleManager的类型。HashShuffleManager（1.2之前默认值），
SortShuffleManager（1.2之后默认值）、tungsten-sort（占用堆外内存排序）
由于SortShuffleManager默认会对数据进行排序，因此如果业务需求中需要排序的话，使用默认的SortShuffleManager就可以；但如果不需要排序，可以通过bypass机制（spark.shuffle.sort.bypassMergeThreshold，设置sortShuffle阈值）或设置HashShuffleManager避免排序，同时也能提供较好的磁盘读写性能。

## job整体优化

### 调节数据本地化等待时长

Task 数据本地化的级别，如果大部分都是PROCESS_LOCAL、NODE_LOCAL，那么就无需进行调节，但是如果发现很多的级别都是
**RACK_LOCAL（同一机架）、ANY（跨机架）**，说明需要**网络传输**，那么需要对本地化的等待时长进行调节，应该是反复调节，每次调节完以后，再来运行观察日志，看看大部分的 task 的本地化级别有没有提升；看看，整个spark 作业的运行时间有没有缩短。

### 使用堆外内存

堆外内存，指的是内存额外开销，需要将spark.memory.enable.offheap.enable打开为true，并设置堆外内存大小。

### 调节连接等待时长

Executor 优先从自己本地关联的 BlockManager 中获取某份数据，如果本地 BlockManager 没有的话，会通过 TransferService 远程连接其他节点上Executor 的 BlockManager 来获取数据。如果这个过程中遇到GC，Excutor就会停止工作，无法提供数据和响应。为了避免长时间暂停(如 GC)导致的超时，可以考虑调节连接的超时时长。

# 3.0新功能相关优化

## 自适应查询优化 (Adaptive Query Execution, AQE)

AQE属于3.0对Spark sql动态优化

动态Spark SQL优化机制，在运行时，每当 Shuffle Map 阶段执行完毕，AQE 都会结合这个阶段的统计信息，基于既定的规则动态地调整、修正尚未执行的逻辑计划和物理计划，来完成对原始查询语句的运行时优化。

### 动态合并分区

分区太少，会导致每个task数据量大，溢写IO效率低，并行度低；分区太多，每个task数据量太少，拥有过多task，读取大量小的网络数据块IO效率低。

可以在任务开始时先设置较多的 shuffle 分区个数，然后在运行时通过查看 shuffle 文件统计信息将相邻的小分区合并成更大的分区。希望合并后分区大小20MB，合并后最小分区数10

### 动态优化join策略

Spark SQL支持多种join策略，AQE 现在根据最准确的 join 大小运行时重新计划 join 策略。

### 动态调整join倾斜

将skew join加随机数打散重新join的方法交给AQE

## 3动态分区裁剪（Dynamic Partition Pruning，DPP）

 a join b on a.key=b.key and a.id<2

类似于谓词下推，先将一侧a表按照条件过滤，过滤后的结果作为一个子查询与b进行join

待裁剪的表 join 的时候，join 条件里必须有分区字段

如果是需要修剪左表，那么 **join 必须是 inner join** ,left semi join 或 right join,反之亦然。但如果是 left out join,无论右边有没有这个分区，左边的值都存在，就不需要被裁剪

**另一张表需要存在至少一个过滤条件**，比如 a join b on a.key=b.key and a.id<2

## Hint增强

5种join策略

优先级：Broadcast Hash Join > Sort Merge Join > Shuffle Hash Join > cartesian Join > Broadcast Nested Loop Join

### Broadcast Hash Join

即Map端JOIN。当有一张表较小时，我们通常选择Broadcast Hash Join，这样可以避免Shuffle带来的开销，从而提高性能。

Broadcast阶段 ：小表被缓存在executor中
Hash Join阶段：在每个 executor中执行Hash Join

条件与特点：
仅支持等值连接，join key**不需要排序**
支持除了全外连接(full outer joins)之外的所有join类型
Broadcast Hash Join相比其他的JOIN机制而言，效率更高。但是，Broadcast Hash Join属于网络密集型的操作(数据冗余传输)，除此之外，需要在Driver端缓存数据，所以当小表的数据量较大时，会出现OOM的情况
被广播的小表的数据量要小于spark.sql.autoBroadcastJoinThreshold值，默认是10MB(10485760)
被广播表的大小阈值不能超过8GB

### Sort Merge Join

Spark默认，可以通过参数spark.sql.join.preferSortMergeJoin进行配置，默认是true，即优先使用Sort Merge Join。一般在两张大表进行JOIN时，使用该方式。Sort Merge Join可以减少集群中的数据传输，该方式不会先加载所有数据的到内存，然后进行hashjoin，但是在JOIN之前需要对join key进行**排序**。

仅支持等值连接
支持所有join类型
Join Keys是排序的

### Shuffle Hash Join

当要JOIN的表数据量比较大时，可以选择Shuffle Hash Join。这样可以将大表进行按照join的key进行重分区，保证每个join key都发送到同一个分区中。

条件与特点：
仅支持等值连接，join key**不需要排序**
支持除了全外连接(full outer joins)之外的所有join类型
需要对小表构建Hash map，属于内存密集型的操作，如果构建Hash表的一侧数据比较大，可能会造成OOM
将参数*spark.sql.join.prefersortmergeJoin (default true)*置为false

### Cartesian Join

如果 Spark 中两张参与 Join 的表没指定join key（ON 条件）那么会产生 Cartesian product join，这个 Join 得到的结果其实就是两张行数的乘积。

仅支持内连接
支持等值和不等值连接

### Broadcast Nested Loop Join

该方式是在没有合适的JOIN机制可供选择时，最终会选择该种join策略。

支持等值和非等值连接

支持所有的JOIN类型，主要优化点如下：
当右外连接时要广播左表
当左外连接时要广播右表
当内连接时，要广播左右两张表
