# Scala基础

Scala是一门以Java虚拟机（JVM）为运行环境并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语言（静态语言需要提前编译的如：Java、c、c++等，动态语言如：js）。
1）Scala是一门多范式的编程语言，Scala支持**面向对象和函数式编程**。（多范式，就是多种编程方法的意思。有面向过程、面向对象、泛型、函数式四种程序设计方法。）
2）**Scala源代码（.scala）会被编译成Java字节码（.class），然后运行于JVM之上**，并可以调用现有的Java类库，实现两种语言的无缝对接。
3）Scala单作为一门语言来看，非常的简洁高效。
4）Scala在设计时，马丁·奥德斯基是参考了Java的设计思想，可以说Scala是源于Java，可以**完全吸收java的所有包和所有写法**

1）完全面向对象编程，对象就是对数据和行为进行封装
2）函数式编程，每个步骤封装成一个函数，函数就是可以当作一个值传递
Scala完全面向对象，故Scala去掉了Java中非面向对象的元素，如static关键字，void类型

## 关键字

package, import, class, object, **trait**, extends, with, type, for
private, protected, abstract, **sealed**, final, **implicit**, lazy, override
try, catch, finally, throw
if, else, **match**, **case**, do, while, for, return, yield
def, val, var
this, super
new
true, false, null

sealed关键字可以修饰类和特质（特质），防止继承的滥用。密封类提供了一种约束：不能在类定义的文件之外定义任何新的子类。

implicit修饰隐式函数

## 变量和常量

var变量 var i:Int = 10，变量类型可以自动推导；类型不可修改；必须有初始值（var name:String = _）；指向的引用可以修改（指向两个new）

val常量 val j:Int = 100，修饰的值不可修改，指向的引用不可改，引用对象的内容可以改

```scala
var myVar : String = "Foo"  // 定义变量
val myVal : String = "Foo"  // 定义常量
var z = new Array[String](3) // 一维数组
z(0) = "Runoob"; z(1) = "Baidu"; z(4/2) = "Google"
var z = Array("Runoob", "Baidu", "Google")
val myMatrix = Array.ofDim[Int](3, 3) // 二维数组3行3列

```

## 下划线用法

```scala
// 1.匿名函数参数占位符
scala> val list = List(5,3,7,9,1)
scala> list.map(_ * 10) // res0: List[Int] = List(50, 30, 70, 90, 10)
scala> list.sortWith(_ < _) // 排序 res1: List[Int] = List(1, 3, 5, 7, 9)
scala> list.reduceLeft(_ + _) // 求和，res2: Int = 25
// 2.无用匿名函数参数
list.foreach(_ => println("Hello Scala"))
// 3.泛型定义中的通配符
scala> def testPrint(l: List[_]) = { // 在Java中用问号来指代泛型中不确定类型的定义（如List<?>）。Scala用下划线来代替它
     |   list.foreach(x => println(x))
     | }
testPrint: (l: List[_])Unit
// 4.变量默认值初始化
scala> var count : Int = _
count: Int = 0
// 5.获取tuple的元素
val wordToCountMap:Map[String,Int] = wordToWordMap.map(kv => (kv._1, kv._2.size))
// 6.模式匹配通配符
// 下划线在模式匹配中除了可以用来代替Java switch-case表达方式中的default之外，还可以占位表示其他元素甚至类型
    def discribe(x:Any) = x match {
      case 5 => "Int five"
      case "hello" => "String hello"
      case true => "Boolean true"
      case '+' => "Char plus"
      case _ => "other"
    }
// 7.导入包
import java.util._
```

变量、方法、函数命名规则：

1）以字母或者下划线开头，后接字母、数字、下划线

2）以操作符开头，且只包含操作符   +*-/#!合法

3）用反引号`....`包括的任意字符串，即使是 Scala 关键字（39 个）也可以  ` if `是合法变量



## 输入和输出

字符串输出：字符串模板s""，自动换行

```scala
println(s"${age}岁的${name}在学习") // $获取值，s""是字符串模板写法
    val s = // 多行显示,用|换行显示，stripMargin做格式优化
    """
        |select
        |name,
        |age
        |from user
        |where name = "zhangsan"
        |""".stripMargin // 去掉前面的空格以及|
    println(s)

```

键盘输入：StdIn.readLine()、StdIn.readShort()、StdIn.readDouble()

## 数据类型

Scala中一切数据都是对象，是Any的子类，没有基本数据类型，完全的面向对象，数据类型和引用类型都是对象。

![img](https://img-blog.csdnimg.cn/20210115203803658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RURlRf,size_16,color_FFFFFF,t_70)

低精度类型自动向高精度值类型转换；
强制转换(a1+b1).toInt，可能引起精度缺失，比如int转byte，只截取最后一个字节

```scala
var n:Int = 128
var b :Byte = n.toByte
println(b) // -128,b截取到的是10000000为补码，认为是最大负数
// 如果130，b为-126。b截取到10000010为补码，-128+2=-126
```

Scala中的StringOps是对Java中的String增强

Unit：=void，表示空值
Null是一个类型，表示空引用。它是所有引用类型（AnyRef）的子类。
Nothing没有类型，不知道是值还是引用，是所有数据类型的子类。异常时使用

## 运算符和流程控制

==类似于java equals，判断内容是否相等（s1 == s2）；eq判断引用地址是否相等（s1.eq(s2)）

Scala没有++,--操作，可以用+=和-=

Scala没有Switch，没有break和continue，使用Break.breakable

For循环，to（左闭右闭） until（左闭右开） by（步长） reverse（倒序）

While循环，需要外部声明递增变量i，不推荐



```scala
    var age = StdIn.readShort()
// if语句直接赋值
    var res: String = if (age < 18) {
      "童年"
    }else if (age >= 18 && age < 30) "中年"
    else "老年"
    println(res)

// for
    for (i <- 1 to 3 by 1) // [1,3]
      print(i + ",")
    for(i <- 1 to 3 if i!=2) { // 循环守卫，类似于continue
      print(i + ",") // 1,3,
    }
    println()
    for (i <- 1 until 3) //[1,3)
      print(i + ",")
    println()
    for (i <- 1 to 10 by 2 reverse) // 倒序打印
      print(i +",")

// breakable
    Breaks.breakable {
      for (e <- 1 to 10) {
        print(e + ",")
        if (e == 5) Breaks.break
      }
    }
```

# 函数式编程

函数式编程：更关注数学意义上的映射关系

函数和方法：**函数是一个可运行的代码集合的子程序，方法是类中的函数**

Scala可以在代码块的任何地方定义函数，函数内可以定义函数，def套def

**函数没有重载和重写的概念，方法是可以重载和重写的**（def里面的def不能重载，但是Object的def可以重载）

返回值类型甚至可以不写

![image-20230404230854417](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230404230854417.png)

## 函数参数

```scala
    // 1.可变参数，java是...,scala是*
    def f1(str:String *): Unit = {
      println(str)
    }
    f1("alice", "atguigu") // WrappedArray(alice, atguigu)类型

    // 2.多参数情况，可变参数放在最后，否则会乱套
    def f2(str1:String, str2:String *):Unit = {
      println(str1 + "," + str2)
    }
    f2("str111", "str222", "str121212")

    // 3.参数默认值，有默认值放在最后
    def f3(str:String, num:Int = 2):Unit = {
      println(str)
      println(num)
    }
    f3("默认值测试")
```

## 函数至简

1. return可以省略, Scala**会使用函数体的最后一行代码作为返回值**
2. 如果函数体只有一行代码，可以省略花括号
3. 返回值类型如果能够推断出来，那么可以省略。变成def f(name:String) = name 类似于f(x) = x
4. 如果**有return， 则不能省略返回值类型**，必须指定
5. 如果函数明确声明unit ，那么即使函数体中使用retum关键字也不起作用
6. Scala如果期望是Unit无返回值，可以省略=，类似于java语言的方法
7. 如果函数无参，但是声明了参数列表为空参数列表即()，那么调用时，小括号可加可不加
8. 如果函数无参，没有声明参数列表，调用时小括号必须省略
9. 如果不关心名称，只关心逻辑处理，那么函数名( def )可以省略，匿名函数

匿名函数，lamda表达式，将函数作为值传递，也就是说A调用函数B的时候**传递一个操作**，也可以传递值：比如给x,y定义二元运算，具体是+ - * /可以参数传递进去
参数的类型可以省略,会根据形参进行自动的推导
类型省略之后,发现只有一个参数,则圆括号可以省略；对于匿名函数而言，无参是不能省略圆括号的。
匿名函数如果只有一行,则大括号也可以省略
如果参数只出现一次,则可以用_下划线代替

```scala
    def caculator(a:Int,b:Int,op:(Int,Int) => Int):Int = {
      op(a,b) // 这是int类型，所以上面的作为操作的参数op的返回值也是int
    }
    println(caculator(4,2,(x:Int,y:Int) => x / y))
    println(caculator(3,4,(x,y) => x*y)) // 类型可以省略
    println(caculator(2,3,_+_)) // 最简写法
```

### 高阶函数3种

1. 函数可以作为值传递，即函数赋值给一个变量

```scala
    def f1(a:Int):Int = {
      a + 2
    }
    val r1 = f1 _ // 法一：空格下划线，表示变量r1接收了f1这个函数
    println(r1(3))
    val r2:Int => Int = f1 // 法二：显式写出变量的输出输出类型，不用加下划线
    println(r2(4))

// 定义匿名函数，作为值传递给变量fun
    val fun = (i:Int, s:String, c:Char) => {
      if (i.equals(0) && s.equals("") && c.equals('0')) false
      else true
    }:Boolean // 匿名函数的返回值类型可以不写，自动推导
    println(fun(0,"",'0'))
```

2. 作为参数传递，即函数的形参是一个操作

```scala
    def f2(a:Int, b:Int, f: (Int,Int) => Int):Int = { // 声明要调用的参数是输入两个int，返回一个int的函数
      f(a,b)
    }
    def add(a:Int,b:Int):Int = {a+b}
    println(f2(2,4,add)) // 法一：显式调用作为函数的参数add
    println(f2(3,4, (x:Int, y:Int) => {x+y})) // 法二：定义匿名函数，有调用类型
    println(f2(2,4, (x,y) => x + y)) // 法二：定义匿名函数，省略调用类型，但是参数名不能省略
    println(f2(2,4, _+_)) // 法三：参数只出现一次，用_代替
```

3. 作为函数返回值传递

   闭包是函数式编程重要特性，内层函数作为返回值定义一个外层函数

   闭包：如果一个函数，访问到了它的外部（局部）变量的值，那么这个函数和他所处的环境，称为闭包
   函数柯里化：**把一个参数列表的多个参数，变成多个参数列表**。

```scala
    def f5():Int => Unit = { // 显式写法，返回值的类型映射直接写出来，也可以这里不写，最后一行写成 f6 _
      def f6(a:Int) = {
        println("f6调用:"+ a)
      }
      f6
    }
    // 直接写f5()报错，因为f5这个函数没有参数，但是他的返回值函数需要参数
    val ff = f5()
    println(ff(30)) // 法一：定义一个变量ff，ff调用了函数f5，再传递一个参数给f6
    println(f5()(25)) // 法二：直接写两个括号调用这个返回值为函数的函数

    // 1.定义函数作为返回值传递，传递两次
    def func(a:Int) = { // 这一层隐式return，最后一行写的是f1 _，自动推导类型
      def f1(s: String):Char => Boolean= { // 这一层显式return，写出来返回值类型是Char => Boolean，最后一行写f2
        def f2(c:Char) = { // 这一层隐式推导
          if (a.equals(0) && s.equals("") && c.equals('0')) false
          else true
        }
        f2
      }
      f1 _
    }
    println(func(0)("")('1'))
	// 2.匿名函数写法
    def funcJian(a:Int):String => Char => Boolean = {
      s => c =>  if (a.equals(0) && s.equals("") && c.equals('0')) false  else true
    }
    // 3.柯里化写法，不写复杂的返回值嵌套，全部用()传参进去，将一个参数列表+多层return变成多个参数列表
    def funKelihua(a:Int)(s:String)(c:Char):Boolean = {
      if (a.equals(0) && s.equals("") && c.equals('0')) false  else true
    }
    println(funKelihua(0)("")('1'))
```

4. 对数组进行处理，传入操作形成新的数组

```scala
    val arr:Array[Int] = Array(12,34,56) // 数组的定义
    def add(a:Int) = a + 3
    def arrayOperations(array: Array[Int], op: (Int) => Int):Array[Int] = {
      for (elem <- array) yield op(elem) // yield返回值直接是数组类型
    }
    println(arr.mkString(","))
    println(arrayOperations(arr,add).mkString(",")) // 显式定义了一个操作函数作为参数
    println(arrayOperations(arr,x => x + 4).mkString(",")) // 匿名函数
    println(arrayOperations(arr,_+5).mkString(",")) // 下划线代替只使用一次的参数x
```

### 惰性加载

当函数返回值被声明为 lazy 时，函数的执行将被推迟，直到我们首次对此取值，该函数才会执行

lazy 不能修饰 var 类型的变量，只能修饰val常量

### map filter reduce

```scala
    // 1.map映射
    def map(arr:Array[Int], op:Int => Int) = {
      for (elem <- arr) yield op(elem)
    }
    val arr = map(Array(1,2,3,4), x => x*x)
    println(arr.mkString(","))
    // 2.filter过滤，奇数留下
    def filter(arr:Array[Int], op:Int => Boolean):Array[Int] = {
      var arr1:ArrayBuffer[Int] = ArrayBuffer[Int]()
      for (elem <- arr if op(elem)) arr1.append(elem)
      arr1.toArray
    }
    val arr1 = filter(Array(1,2,3,4),_% 2 == 1) // 奇数拿出来
    println(arr1.mkString(","))
    // 3.reduce聚合，全部相乘
    def reduce(arr:Array[Int], op: (Int, Int) => Int) = {
      var init: Int = 1
      for (elem <- arr.indices) init = op(init, arr(elem))
      init
    }
    val reduce1 = reduce(Array(5,3,3,3),_*_)
    println(reduce1)
```

# 面向对象

## 包

导包和使用更灵活

局部导入：什么时候使用，什么时候导入。在其作用范围内都可以使用

通配符导入：import java.util._

花括号导入

scala中三个默认的导入：import java.lang._
import scala._
import scala.Predef._

## 类

类默认public

一个 Scala 源文件可以包含多个类，类可以有(参数)，分为主构造器和辅助构造器（def this），先调用主构造器，再调用辅助构造器

## 伴生对象

**scala完全面向对象，所以没有static关键字**，没有静态的概念，所有的操作都是针对于对象的，但为了与java的静态对象交互，产生了单例对象即object。若单例对象object名与类class名一致，则称该单例对象是这个类的伴生对象，这个类的所有“静态”内容都可以放置在它的伴生对象中声明。

**单例对象中的属性和方法都可以通过伴生对象名（类名）直接调用访问。**

```scala
object Test11 {
  def main(args: Array[String]): Unit = {
    val student = new Student11("alice", 18)
    student.printerInfo()
  }

}
class Student11(val name:String, val age:Int) {
  def printerInfo(): Unit = {
    println("student:" + name +",age:" + age + ",school:" + Student11.school)
  }
}
object Student11 {
  val school: String = "atguigu"
}

```

## 特质trait

scala的特质=java的抽象类+接口：

一个类可以混合多个特质多继承；
trait中可以有具体的属性方法，也可以有抽象的属性方法
所有java接口都可以当作scala特质使用

## 类型检查和转换

obj.isInstanceOf[T]：判断 obj 是不是 T 类型。
obj.asInstanceOf[T]：将 obj 强转成 T 类型。
classOf：获取对象的类名

type定义新类型：本质上是一个类型的别名（type S = String后面就可以用S声明字符串了）

# 高级函数

## 数组、List、集合

mutable变长数组，需要引入new mutable。val arr01 = **ArrayBuffer**[ Any ] (1, 2, 3 )// 可变长数组，**Array**是定长数组**Array.ofDim**[Double]  (3,4)多维数组// 二维数组中3个元素，每个元素含有4为数组

List默认定长列表var list:List[String] = List(1,2,3,4)。ListBuffer是可变列表val buffer = ListBuffer(1,2,3,4)可以增删改

Set集合默认不可变集合，数组无序val set = Set(1,2,3,4,5,6)。mutable.Set可变集合val set = mutable.Set(1,2,3,4,5,6)可以增删改

Map也是

元组：将多个无关的数据封装为一个整体，称为元组。Map中的值就是元素个数为2的元组

```scala
    val map1 = Map("a"-> 1, "b" -> 2, "c" -> 3)
    map1.foreach(tuple => println(tuple._1 + ":" + tuple._2))
```

常用操作：list.length 

list.size 

list.mkString(",")

 list.contains(element)

list.foreach(tuple => {})

list.head list.tail // 获取集合头部和尾部

list1.union(list2) list1.intersect(list2) list1.diff(list2) // 求并集、交集、差集

sum product(乘积) max min sortBy(x => x) sortWith

## 高级函数

map 

reduce=reduceLeft

 fold = foldLeft

```scala
// map  
def main(args: Array[String]): Unit = {
    var list:List[Int] = List(1,2,3,4,5,6,7,8,9)
    val nestedList:List[List[Int]] = List(List(1,2,3), List(4,5,6), List(7,8,9))
    val wordList:List[String] = List("hello world", "hello atguigu", "hello scala")
    // 1.过滤操作，留下偶数
    println(list.filter(x => x % 2 == 0)) // 留下偶数
    // 2.映射操作，挨个变换
    println(list.map(x => x+ 1))
    // 3.扁平化操作，无论是几维还是什么格式的，都映射到某个函数返回新集合，扁平为一长串
    println(nestedList.flatten)
    // 4.映射并且扁平化
    println(wordList.flatMap(x => x.split(" ")))
    // 5.将list按照x % 2分为两组
    println(list.groupBy( x => x % 2))
  }
// reduce
  def main(args: Array[String]): Unit = {
    var list = List(3,4,5,8,10)
    val i:Int = list.reduce( (x,y) => x-y) // reduce = reduceLeft,3-4-5-8-10 = -24
    println(i)
    val i1 = list.reduceLeft((x,y) => x-y) // -24
    println(i1)
    val i2 = list.reduceRight((x,y) => x-y) // reduceRight 3-(4-(5-(8-10))) = 6
    println(i2)
    println(list.sum)
    println(list.sortWith((x,y) => x < y))
    println(list.sortWith((x,y) => x > y))
  }
// fold，类似reduce，多了一个初始值
  def main(args: Array[String]): Unit = {
    val list = List(1,2,3,4)
    println(list.foldLeft(1)(_ - _)) // z是初始值，1 -1 - 2 - 3- 4 = -9
    println(list.foldRight(10)(_ - _)) // 10 - (1-(2-(3-4))) = 8

  }
// 两个map数据合并
def main(args: Array[String]): Unit = {
    // map合并
    val map1 = mutable.Map("a"-> 1, "b" -> 2, "c" -> 3)
    val map2 = mutable.Map("a"-> 4, "b" -> 5, "d" -> 6)
    val map3:mutable.Map[String,Int] = map2.foldLeft(map1){
      (mergedMap,kv) => {
        val key = kv._1
        val value = kv._2
        mergedMap(key) = mergedMap.getOrElse(key,0) + value
        // 将map2中的值与map1进行聚合，mergedMap初始是map2.对于map1每一个元素，取到key value，
        mergedMap
      }
    }
    println(map3)

  }
```



```scala
// 1.wordCount经典 List((HBase,3), (Hello,3), (Hive,2))
def main(args: Array[String]): Unit = {
    val StringList:List[String] = List("Hello Scala HBase Hive kafka", "Hello Scala HBase", "Hello Java Java HBase Hive kafka")
    // 1.切分打散单词
    val wordList:List[String] = StringList.flatMap(_.split(" "))
    // 2.相同的单词进行分组，每一个word按照word进行分组
    val wordToWordMap:Map[String, List[String]] = wordList.groupBy(word => word)
    // Map(Hive -> List(Hive, Hive), Scala -> List(Scala, Scala), HBase -> List(HBase, HBase, HBase), kafka -> List(kafka, kafka), Hello -> List(Hello, Hello, Hello), Java -> List(Java, Java))
    // 3.对分组之后的list取长度
    println(wordToWordMap)
    val wordToCountMap:Map[String,Int] = wordToWordMap.map(kv => (kv._1, kv._2.size))
    // 4.将map转化为list并排序,按照出现次数进行降序排列，take取前三
    val sortList:List[(String, Int)] = wordToCountMap.toList.sortWith((left, right) => left._2 > right._2).take(3)
    println(sortList)
  }
// 2.有预统计的wordCount

```

# 模式匹配

类似于switch，Scala没有switch。可以匹配任意类型（byte short int long float double boolean char String），可以匹配类型，模式守卫、匹配数组、匹配列表、匹配元组

```scala
  def main(args: Array[String]): Unit = {
    var a:Int = 10
    var b:Int = 20
    var operation:Char = '+'
    var result = operation match {
      case '+' => a + b
      case '-' => a - b
      case '*' => a * b
      case '/' => a / b
      case _ => "illegal"
    }
    println(result)

    def abs(x:Int) = x match { // 模式守卫
      case i:Int if i >= 0 => i
      case i:Int if i < 0 => -i
      case _ => "type illegal"
    }
    println(abs(2))
    // 可以匹配任意类型（byte short int long float double boolean char String）

    def discribe(x:Any) = x match {
      case 5 => "Int five"
      case "hello" => "String hello"
      case true => "Boolean true"
      case '+' => "Char plus"
      case _ => "other"
    }
    println(discribe(true))
    println("=======================")

    // 匹配类型
    def describeType(x:Any):String = x match {
      case i:Int => "Int:" + i
      case s:String => "String:" + s // 泛型擦除，list[String] list[Int]都是识别为List
      case list:List[String] => "List:" + list
      case array:Array[Int] => "Array[Int]:" + array.mkString(",")
      case a => "Something else: " + a
    }
    println(describeType(10))
    println(describeType("abc"))
    println(List(1,2,3,4))
    println(Array(1,2))
    println(Array("1,2,3", "hello"))
  }
```

# 异常处理

try catch{case case}

```scala
try {
      var n= 10 / 0
    }catch {
      case ex: ArithmeticException=>{
        // 发生算术异常
        println("发生算术异常")
      }
      case ex: Exception=>{
        // 对异常处理
        println("发生了异常 1")
        println("发生了异常 2")
      }
    }finally {
      println("finally")
    }
```

# 泛型

1. 协变和逆变

协变+：在定义泛型时，前面有+class MyList[+T]
逆变-：class MyList[-T]

协变：Son 是 Father 的子类，则 MyList[Son] 也作为 MyList[Father]的“子类”。协同变化
逆变：Son 是 Father 的子类，则 MyList[Son]作为 MyList[Father]的“父类”。
不变：Son 是 Father 的子类，则 MyList[Father]与 MyList[Son]“无父子关系”。不变，没有关系

```scala
object generator {
  def main(args: Array[String]): Unit = {
    val child:Parent = new Child // java多态
    val cc:MyCollection[Parent] = new MyCollection[Child]
    // 普通的这种写法是错的，因为Parent是Child父类，但是不能传递给MyCollection，
    // 但如果 MyCollection[+T]，就可以传递这种父子关系
  }

}
class Parent{}
class Child extends Parent{}
class MyCollection[+T] {}
```

2. 泛型上下限

```scala
  def main(args: Array[String]): Unit = {
    val child:Parent = new Child // java多态
    val cc:MyCollection[Parent] = new MyCollection[Child]
    // 普通的这种写法是错的，因为Parent是Child父类，但是不能传递给MyCollection，
    // 但如果 MyCollection[+T]，就可以传递这种父子关系

    def test[A <:Child] (a:A) :Unit = {
      // <:  说明A的泛型需要是Child以及其子类； >: 说明A的泛型需要是Child以及其父类
      println(a.getClass.getName)
    }
//    test[Parent](new Parent) // 在>:的时候生效, com.atguigu.chapter01.Parent
    test[Child](new Child) // com.atguigu.chapter01.Child
    test[SubChild](new SubChild) // 在<:时候生效，com.atguigu.chapter01.SubChild
  }
}
class Parent{}
class Child extends Parent{}
class SubChild extends Child{}
```

