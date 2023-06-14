# java基础80题

## 一个".java"源文件中是否可以包括多个类

可以有多个类，但只能有一个public的类，并且public的类名必须与文件名相一致。

2.Java有没有goto?

没有，但是 goto 是 java 中的保留字。

## & VS &&

&和&&都可以用作逻辑与的运算符，表示逻辑与（and），当运算符两边的表达式的结果都为true时，整个运算结果才为true，否则，只要有一方为false，则结果为false。

**&&还具有短路的功能**，即如果第一个表达式为false，则不再计算第二个表达式。

**&还可以用作位运算符**，当&操作符两边的表达式不是boolean类型时，&表示按位与操作，我们通常使用0x0f来与一个整数进行&运算，来获取该整数的最低4个bit位，例如，0x31 & 0x0f的结果为0x01。

逻辑运算符：按位与& 按位或| 按位异或^ 按位非~ 

```java
int a = 0x0f, b = 0x4a4a39; // 输入2进制0b，8进制0，16进制数0x
int c = 0B1101;
//输出2进制Integer.toBinaryString，8进制%o,%#o,%#O；16进制%#x,%x,%X
 System.out.printf("十六进制：按位与：%x, 按位异或：%#x, 按位或：%#X\n", a & b, a^b, a | b);
 System.out.println("二进制按位非：" + Integer.toBinaryString(~c));
```

## 跳出多重嵌套循环

**让外层的循环条件表达式的结果可以受到里层循环体代码的控制**，例如，要在二维数组中查找到某个数字。

```java
    int[][] arr = {{1, 2, 3}, {4, 5, 6, 7}, {9}};
    boolean found = false;
    for (int i = 0; i < arr.length && !found; i++) {
for (int j = 0; j < arr[i].length; j++) {
    System.out.println("i = " + i + ", j = " + j);
    if (arr[i][j] == 5) {
found = true;
break;
    }
}
    }
```



## switch语句作用的基本类型

switch可作用在byte short int char string（语法糖）以及其引用类型上

switch 不支持 long 类型



## char存一个中文汉字

char型变量是用来存储Unicode编码的字符的，unicode编码字符集中包含了汉字，所以，char型变量中当然可以存储汉字啦。不过，如果某个特殊的汉字没有被包含在unicode编码字符集中，那么，这个char型变量中就不能存储这个特殊汉字。补充说明：unicode编码占用两个字节，所以，char类型的变量也是占用**两个字节**。



## x*power(2,n)

```java
x << n // x=3,n = 2表示3*2^2=12，左移位
```

case支持byte short int char Enum String(语法糖)

## 一百亿的计算器

首先要明白这道题目的考查点是什么，一是要对计算机原理的底层细节要清楚，要知道加减法的位运算原理和计算机中的算术运算会发生越界的情况；二是要具备一定的面向对象的设计思想。

首先，计算机中用固定数量的几个字节来存储的数值，所以计算机中能够表示的数值是有一定的范围的，先以byte 类型的整数为例，它用1个字节进行存储，表示的最大数值范围为-128到+127。-1在内存中对应的二进制数据为11111111，如果两个-1相加，不考虑Java运算时的类型提升，运算后会产生进位，二进制结果为1,11111110，由于进位后超过了byte类型的存储空间，所以进位部分被舍弃，即最终的结果为11111110，也就是-2，这正好利用溢位的方式实现了负数的运算。-128在内存中对应的二进制数据为10000000，如果两个-128相加，不考虑Java运算时的类型提升，运算后会产生进位，二进制结果为1,00000000，由于进位后超过了byte类型的存储空间，所以进位部分被舍弃，即最终的结果为00000000，也就是0，这样的结果显然不符合期望，这说明计算机中的算术运算是会发生越界情况的，两个数值的运算结果不能超过计算机中的该类型的数值范围。由于Java中涉及表达式运算时的类型自动提升，无法用byte类型来做演示这种问题和现象的实验，可以用下面一个使用整数做实验的例子程序体验一下：

```java
    int a = Integer.MAX_VALUE;
    int b = Integer.MAX_VALUE;
    int sum = a + b;
    System.out.println(“a=”+a+”,b=”+b+”,sum=”+sum);
```
先不考虑long类型，由于int的正数范围为2的31次方，表示的最大数值约等于2*1000*1000*1000，也就是20亿的大小，所以，要实现一个一百亿的计算器，我们得自己设计一个类可以用于表示很大的整数，并且提供了与另外一个整数进行加减乘除的功能，大概功能如下：

1）这个类内部有两个成员变量，一个表示符号，另一个用字节数组表示数值的二进制数；

2）有一个构造方法，把一个包含有多位数值的字符串转换到内部的符号和字节数组中；

3）提供加减乘除的功能；

```java
public class BigInteger{
		int sign;
		byte[] val;
		public Biginteger(String val)	{
			sign = ;
			val = ;
		}
		public BigInteger add(BigInteger other)	{
			
		}
		public BigInteger subtract(BigInteger other)	{
			
		}
		public BigInteger multiply(BigInteger other){
			
		}
		public BigInteger divide(BigInteger other){
			
		}
}
```
备注：要想写出这个类的完整代码，是非常复杂的，如果有兴趣的话，可以参看jdk中自带的java.math.BigInteger类的源码。面试的人也知道谁都不可能在短时间内写出这个类的完整代码的，他要的是你是否有这方面的概念和意识，他最重要的还是考查你的能力，所以，不要因为自己无法写出完整的最终结果就放弃答这道题，能做的就是你比别人写得多，证明你比别人强，有这方面的思想意识就可以了，毕竟别人可能连题目的意思都看不懂，什么都没写，要敢于答这道题，即使只答了一部分，那也与那些什么都不懂的人区别出来，拉开了距离，算是矮子中的高个，机会当然就得到了。另外，答案中的框架代码也很重要，体现了一些面向对象设计的功底，特别是其中的方法命名很专业，用的英文单词很精准，这也是能力、经验、专业性、英语水平等多个方面的体现，会给人留下很好的印象，在编程能力和其他方面条件差不多的情况下，英语好除了可以获得更多机会外，薪水可以高出一千元。



## 静态变量 VS 实例变量

在语法定义上的区别：静态变量前要加static关键字，而实例变量前则不加。

在程序运行时的区别：实例变量属于某个对象的属性，必须创建对象后才可以通过这个对象来使用；

静态变量是类变量，可以直接使用类名来引用。

**无论创建多少个实例对象，永远都只分配了一个staticVar变量**，并且每创建一个实例对象，这个staticVar就会加1；但是，每创建一个实例对象，就会分配一个instanceVar，即可能分配多个instanceVar，并且每个instanceVar的值都只自加了1次。

## Math.round

```java
System.out.printf("%.0f,%.0f\n", Math.floor(11.6), Math.floor(-11.3)); // floor往x轴左边找
System.out.printf("%.0f,%.0f\n", Math.ceil(11.6), Math.ceil(-11.3)); // ceil往x轴右边找
System.out.printf("%d,%d\n", (int)Math.round(11.5), (int)Math.round(-11.5)); // Math.floor(x+0.5)
```

最难掌握的是round方法，它表示“四舍五入”，算法为Math.floor(x+0.5)，即将原来的数字加上0.5后再向下取整，所以，Math.round(11.5)的结果为12，Math.round(-11.5)的结果为-11。

## 四大作用域

这四个作用域的可见范围如下表所示。

说明：如果在修饰的元素上面没有写任何访问修饰符，则表示friendly。

| 作用域     | 当前类 | 同一package | 子孙类 | 其他package |
|:----------|:------|:-----------|:------|:-----------|
| public    | √     | √          | √    | √          |
| protected | √     | √          | √    | ×          |
| friendly  | √     | √          | ×     | ×          |
| private   | √     | ×         | ×     | ×          |

备注：只要记住了有4种访问权限，4个访问范围，然后将全选和范围在水平和垂直方向上分别按排从小到大或从大到小的顺序排列，就很容易画出上面的图了。

## Overload VS Override

Overload是重载的意思，Override是覆盖的意思，也就是重写。

重载Overload：**方法名必须相同**，**入参必须不同**（参数类型不同、个数不同、顺序不同），返回值、访问修饰符、抛出异常**可以不同**。不能在子类中重载private方法。
重写Override：子类**覆盖**父类中定义的那个完全相同的方法，要求返回值一致，抛出异常一致或者是其子类，不能在子类中重写private方法。重写就是子类对父类方法的重新改造，外部样子不能改变，内部逻辑可以改变。

## 构造器Constructor

构造器作用：完成对象的初始化

**构造器Constructor不能被继承**，因此不能重写Override，但可以被重载Overload。

* 构造器必须与类同名（如果一个源文件中有多个类，那么构造器必须与公共类同名）
* 每个类可以有一个以上的构造器
* 构造器可以有0个、1个或1个以上的参数
* 构造器**没有返回值**
* 构造器总是伴随着**new操作一起调用**
* 不添加任何构造器会**默认有空的构造器**
* java的**构造器并不是函数**，所以他并不能被继承
* 子类构造器的第一句一定是super父类
* 自动执行，不需调用



## 面向对象4特征

面向对象的编程语言有**封装、继承 、抽象、多态**等4个主要的特征。

1）封装：

封装是保证**软件部件优良**，封装的目标就是要实现软件部件的“**高内聚、低耦合**”，**对象是封装的最基本单位**。把对象的状态信息隐藏在对象内部，对同一事物进行操作的方法和相关的方法放在同一个类中。

2）继承：

在一个已经存在的类的基础定义并实现类，**子类自动共享父类数据和方法**，这是类之间的一种关系，提高了软件的**可重用性和可扩展性**。子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是**父类中的私有属性和方法子类是无法访问，只是拥有**。

3）抽象：

抽象类，是不能被实例化的类。在抽象类中，类的所有其它功能都存在，成员变量、方法、构造器都可以用同样的方式访问。我们只是不能创建抽象类的实例。

4）多态（里氏替换原则）：任何基类可以出现的地方，子类一定可以出现。父类的引用指向子类的实例。

**子类继承父类，重写父类方法。**
**调用父类被重写的方法时，不同子类有不同结果。**

静态多态（编译时多态）：对象的不同的方法可以用相同的一个方法名，也就是**重载**来实现。
动态多态（运行时多态）：引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定。如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。 **动态绑定和重写**的机制来实现。

**多态不能调用“只在子类存在但在父类不存在”的方法**

22.java中实现多态的机制是什么？

靠的是**父类或接口定义的引用变量可以指向子类或具体实现类的实例对象**，而程序调用的方法在运行期才动态绑定，就是引用变量所指向的具体实例对象的方法，也就是内存里正在运行的那个对象的方法，而不是引用变量的类型中定义的方法。

```java
public class test {
    public static void main(String[] args) {
        Parent parent = new Child();
        Child child = new Child();
        parent.m1(); // 多态，调用Child的m1，而非Parent的m1
        parent.m2(); // 多态，Child没有重写m2，所以调用自己的m2
        parent.m3(); // 错误，多态不能调用只属于子类，父类没有的方法
        child.m1(); // 子类调用的时候，如果重写了，执行重写方法；否则调用父类方法。
        child.m2();
    }
}
class Parent{
    public void m1(){ System.out.println("Parent m1");}
    public void m2(){ System.out.println("Parent m22222");}
}
class Child extends Parent {
    public void m1(){System.out.println("Child m1");}
    public void m3(){System.out.println("Child m3333");}
}
```

## abstract  VS interface

相同点：都不能被实例化；都可以包含抽象方法；都可以有默认实现的方法。

接口：一种标准、规范，一系列**抽象方法**的集合；定制规则，展现**多态**。一组对象没有共同属性，只有共同行为
抽象类：定义共性的属性和行为（**无法完整描述一个事物**），在代码实现方面发挥作用，可以实现代码的重用。如果一组对象有共同属性和行为

1. *比较一下两者的语法区别：*

1）抽象类可以有**构造方法、普通成员变量、普通方法、静态方法**，接口中都不能有。

2）抽象类中的抽象方法可用是public或protected，接口中的所有方法必须都是public abstract。

3）**抽象类和接口中都可以包含静态成员变量**，接口必须是public static final。

4）类只能单继承抽象类，并且必须实现其所有的抽象方法才能摆脱抽象类；
类可以继承多个接口，接口可以继承多个接口，一个类如果实现了一个接口，则要实现该接口的所有方法，包括该接口继承的接口的所有方法。

5）抽象类与一般类唯二的区别：**允许**含有抽象方法（可以没有）；不能实例化对象。

6）java8下的[区别](#接口增强)

2. *abstract的method是否可同时是static,是否可同时是native，是否可同时是synchronized?*

abstract的method 不可以是static的，因为抽象的方法是要被子类实现的，而static与子类扯不上关系！

native方法表示该方法要用另外一种依赖平台的编程语言实现的，不存在着被子类实现的问题，所以，它也不能是抽象的，不能与abstract混用。例如，FileOutputSteam类要硬件打交道，底层的实现用的是操作系统相关的api实现，例如，在windows用c语言实现的，所以，查看jdk 的源代码，可以发现FileOutputStream的open方法的定义如下：

private native void open(String name) throws FileNotFoundException;

如果我们要用java调用别人写的c语言函数，我们是无法直接调用的，我们需要按照java的要求写一个c语言的函数，又我们的这个c语言函数去调用别人的c语言函数。由于我们的c语言函数是按java的要求来写的，我们这个c语言函数就可以与java对接上，java那边的对接方式就是定义出与我们这个c函数相对应的方法，java中对应的方法不需要写具体的代码，但需要在前面声明native。

关于synchronized与abstract合用的问题，我觉得也不行，因为在我几年的学习和开发中，从来没见到过这种情况，并且我觉得**synchronized应该是作用在一个具体的方法上才有意义**。而且，方法上的synchronized同步所使用的同步锁对象是this，而抽象方法上无法确定this是什么。

## 内部类

内部类就是在一个类的内部定义的类，分为静态内部类、成员内部类、局部内部类、匿名内部类。

静态方法**只能使用静态**的属性/方法；普通方法**可以使用静态/普通**的属性/方法

**不可以从一个static方法内部发出对非static方法的调用**。

| 内部类 | 定义                                                         | 允许访问外部类成员                       | 允许定义static成员 |
| ------ | ------------------------------------------------------------ | ---------------------------------------- | ------------------ |
| 静态   | 不依赖于外围类对象，和普通类几乎没差别                       | 不能访问外部类的普通成员，只能访问静态的 | √                  |
| 成员   | 只有先创建了外围类才能够创建内部类，要new一个对象去调用      | √                                        | ×                  |
| 局部   | 方法中定义的内部类，只能在方法内new一个对象来调用。<br />必须先定义再使用 | √                                        | ×                  |
| 匿名   | **必须**继承其他类或实现其他接口                             | √                                        | ×                  |

```java
public class Outer {
    String outerStr = "outerCommon";
    static String outerStrStatic = "outerStatic";
//    1.静态内部类，可以直接调用到，不需要Outer，和普通类没有大差别
    // 只能访问到包含类的静态成员outerStrStatic，不能访问普通成员outerStr
    static class InnerClass{
String innerString = "nameInner";
static String string = "test";
static void fun1() {
    System.out.println("fun1 in InnerClass");
}
    }
//    2.成员内部类，需要先定义Outer对象，再调用这个类，不能定义static变量
    // 可以访问包含类的静态成员outerStrStatic以及普通成员outerStr
    // Outer.Draw draw = outer.new Draw();
    public class Draw{
protected String string = "Draw";
public void fun1() {
    System.out.println("fun1 in Draw");
}
    }

    public void funOuter() {
       String vary1 = "funOuter";
//3.局部内部类，无法通过Outer调用到，只能在funOuter内new一个对象来调用。必须先定义再使用
//			 不能定义static变量 可以访问包含类的静态成员outerStrStatic以及普通成员outerStr
class InnerFunOuter{
    void innerFun1() {
System.out.println("fun1 in Outer innerFun class");
System.out.println(vary1); // 可以调用到方法体的成员变量
    }
}
InnerFunOuter innerFunOuter = new InnerFunOuter();
innerFunOuter.innerFun1();

//      4.匿名内部类，方法体内部，实现一个接口， 可以访问包含类的静态成员outerStrStatic以及普通成员outerStr
Thread i = new Thread(new Runnable() {
    @Override
    public void run() {
System.out.println("Start running");
    }
});
i.run();
    }

    public static void main(String[] args) {
Outer outer = new Outer();
InnerClass.fun1();
InnerClass innerClass = new InnerClass();
System.out.println(innerClass.innerString);
Outer.Draw draw = outer.new Draw();
draw.fun1();
outer.funOuter();
    }
}
```
## super.getClass()

super：继承父成员的构造函数（该传参传参）；调用父成员的变量和方法

下面程序的输出结果是多少？

```java
import java.util.Date;
public class TestSuper extends Date{
    TestSuper(int year, int month ,int date){
super(year, month,date);
    }
    TestSuper() {
super();
    }
    public static void main(String[] args) {
new TestSuper().get();
Date this_data = new TestSuper(2016,8,20);
System.out.println(this_data.getMonth()); // 8
    }
    public void get() {
System.out.println(super.getClass().getSuperclass().getName()); // java.util.Date
System.out.println(super.getClass().getName()); //输出 base.TestSuper
    }
}
```
很奇怪，结果是TestSuper。

在test方法中，直接调用getClass().getName()方法，返回的是类名。由于getClass()在Object类中定义成了final，子类不能覆盖该方法，所以，在test方法中调用getClass().getName()方法，其实就是在调用从父类继承的getClass()方法，等效于调用super.getClass().getName()方法，所以，super.getClass().getName()方法返回的也应该是Test。

## Collection VS  Collections

Collection是集合类的上级接口，继承与他的接口主要有Set  List Queue。

Collections是针对集合类的一个帮助类，他提供一系列静态方法实现对各种集合的**搜索、排序、线程安全化**等操作。

54.Collection框架中实现比较要实现什么接口

comparable/comparator

## 常用的类，包，接口

要让人家感觉你对java ee开发很熟，所以，不能仅仅只列core java中的那些东西，要多列你在做ssh项目中涉及的那些东西，就写你最近写的那些程序中涉及的那些类。

常用的类：BufferedReader BufferedWriter FileReader FileWirter String Integer HashMap LinkedList

常用的包：java.lang java.io java.util java.sql 

常用的接口：Remote List Map Document NodeList,Servlet,HttpServletRequest,HttpServletResponse,Transaction(Hibernate)、Session(Hibernate),HttpSession

SSH是 [struts](https://so.csdn.net/so/search?q=struts&spm=1001.2101.3001.7020)+spring+hibernate的一个集成框架，这种框架是基于MVC的开发，模型（Model）、视图（View）、控制（Controller）

## assert

assertion(断言)在软件开发中是一种常用的**调试**方式，很多开发语言中都支持这种机制。在实现中，assertion就是在程序中的一条语句，它对**一个boolean表达式进行检查**，一个正确程序必须保证这个boolean表达式的值为true；如果该值为false，说明程序已经处于不正确的状态下，assert将给出警告或退出。一般来说，assertion用于**保证程序最基本、关键的正确性**。

assertion检查通常在**开发和测试时开启**。为了提高性能，在软件发布后，assertion检查通常是关闭的。

```java
package com.huawei.interview;
public class AssertTest {
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int i = 0;
		for(i=0;i<5;i++)
		{
			System.out.println(i);
		}
		//假设程序不小心多了一句--i;
		--i;
		assert i==5;		
	}
}
```
## Java代码查错

0）short s1 = 1; s1 = s1 + 1;有什么错? short s1 = 1; s1 += 1;有什么错?

```java
short s1 = 1;
s1 =(short) (s1 + 1); //正确，s1+1运算时会自动提升表达式的类型，所以结果是int型，再赋值给short类型s1时，编译器将报告需要强制转换类型的错误。
s1 += 1;//正确，由于 += 是java语言规定的运算符，java编译器会对它进行特殊处理，因此可以正确编译。
```



1）

```java
abstract class Name {
    private String name;
    public abstract boolean isStupidName(String name) {}
}
```
这有何错误?
答案: 错。abstract method必须以分号结尾，且不带花括号。

2）

```java
public class Something {
    void doSomething () {
private String s = "";
int l = s.length();
    }
}
```
有错吗?
答案: 错。**局部变量前不能放置任何访问修饰符** (private，public，和protected)。final可以用来修饰局部变量(final如同abstract和strictfp，都是非访问修饰符，strictfp只能修饰class和method而非variable)。

3）

```java
abstract class Something {
    private abstract String doSomething ();
}
```
这好像没什么错吧?
答案: 错。abstract的methods不能以private修饰。abstract的methods就是让子类implement(实现)具体细节的，怎么可以用private把abstract method封锁起来呢? (同理，abstract method前不能加final)。

4）

```java
public class Something {
    public int addOne(final int x) {
return ++x;
    }
}
```
这个比较明显。
答案: 错。int x被修饰成final，意味着x不能在addOne method中被修改。

5）

```java
public class Something {
    public static void main(String[] args) {
	Other o = new Other();
	new Something().addOne(o);
    }
    public void addOne(final Other o) {
		o.i++;
    }
}
class Other {
    public int i;
}
```
和上面的很相似，都是关于final的问题，这有错吗?
答案: 正确。在addOne method中，参数o被修饰成final。如果在addOne method里我们修改了o的reference(比如: o = new Other();)，那么如同上例这题也是错的。但这里修改的是o的member vairable(成员变量)，而o的reference并没有改变。

6）

```java
class Something {
    int i;
    public void doSomething() {
		System.out.println("i = " + i);
    }
}
```
有什么错呢? 看不出来啊。
答案: 正确。输出的是"i = 0"。int i属於instant variable (实例变量，或叫成员变量)。instant variable有default value。int的default value是0。

7）

```java
class Something {
    final int i;
    public void doSomething() {
		System.out.println("i = " + i);
    }
}
```
和上面一题只有一个地方不同，就是多了一个final。这难道就错了吗?
答案: 错。final int i是个final的instant variable (实例变量，或叫成员变量)。**final的instant variable没有default value**，必须在constructor (构造器)结束之前被赋予一个明确的值。可以修改为“final int i = 0;”。

8）

```java
public class Something {
    public static void main(String[] args) {
		Something s = new Something();
		System.out.println("s.doSomething() returns " + doSomething());
    }
    public String doSomething() {
return "Do something ...";
    }
}
```
看上去很完美。
答案: 错。看上去在main里call doSomething没有什么问题，毕竟两个methods都在同一个class里。但仔细看，main是static的。static method不能直接call non-static methods。可改成"System.out.println("s.doSomething() returns " + s.doSomething());"。同理，static method不能访问non-static instant variable。

9）

此处，Something类的文件名叫OtherThing.java

```java
class Something {
    private static void main(String[] something_to_do) {   
System.out.println("Do something ...");
    }
}
```
这个好像很明显。
答案: 正确。从来没有人说过Java的Class名字必须和其文件名相同。但public class的名字必须和文件名相同。

10）

```java
interface A{
    // 默认就是public static final的，可以不加
    public static final int x = 0;
}
class B{
    int x =3;
}
public class TestWrong extends B implements A{
    TestWrong(){
        super();
    }
    public void pX(){
        System.out.println(x); // 错的
        System.out.println(A.x); // 这样才能取到A.x，如果不同名，不需要加A.就可以取到
        System.out.println(super.x); // 这样才能取到B.x
    }
    public static void main(String[] args) {
        new TestWrong().pX();
    }
}
```
答案：错误。在编译时会发生错误(错误描述不同的JVM有不同的信息，意思就是未明确的x调用，两个x都匹配（就象在同时import java.util和java.sql两个包时直接声明Date一样）。对于父类的变量,可以用super.x来明确，而接口的属性默认隐含为 public static final.所以可以通过A.x来明确。
11）

```java
interface Playable {
    void play();
}
interface Bounceable {
    void play();
}
interface Rollable extends Playable, Bounceable {
    Ball ball = new Ball("PingPang");
}
class Ball implements Rollable {
    private String name;
    public String getName() {
        return name;
    }
    public Ball(String name) {
        this.name = name;
    }
    public void play() {
        ball = new Ball("Football"); // 这里有错，ball是接口的变量，默认public static final，是不能改变引用的
        System.out.println(ball.getName());
    }
}

```
这个错误不容易发现。
答案: 错。

"interface Rollable extends Playable, Bounceable"没有问题。interface可继承多个interfaces，所以这里没错。问题出在interface Rollable里的"Ball ball = new Ball("PingPang");"。任何在interface里声明的interface variable (接口变量，也可称成员变量)，默认为public static final。也就是说"Ball ball = new Ball("PingPang");"实际上是"public static final Ball ball = new Ball("PingPang");"。在Ball类的Play()方法中，"ball = new Ball("Football");"改变了ball的reference，而这里的ball来自Rollable interface，Rollable interface里的ball是public static final的，final的object是不能被改变reference的。

因此编译器将在"ball = new Ball("Football");"这里显示有错。

# Java基础

## JVM JDK JRE IDE 字节码

**字节码和JVM**：“一次编译，到处运行”的关键所在。

JVM：运行 Java 字节码的虚拟机。JVM并不是只有一种，只要满足JVM规范，都可以开发专属JVM。hotspot VM是最常用的一种，还有Zing VM、J9Vm。
JDK：是功能齐全的 Java SDK，能够创建和编译程序。它拥有 JRE 所拥有的一切，还有编译器（javac）和工具（如 javadoc 和 jdb）。
JRE 是 Java 运行时环境。可以运行，但不能创建新程序。包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件。
IDE：集成开发环境，Eclipse、Lightly、IntelliJ IDEA、JDeveloper

字节码：.class文件，JVM可以识别的代码。JAVA**编译和解释**并存。

![Java程序转变为机器代码的过程](https://oss.javaguide.cn/github/javaguide/java/basis/java-code-to-machine-code.png)

java通过**字节码**的方式，将字节码对应的**机器码**保存下来，机器码的运行效率是高于解释器的。消耗大部分系统资源的只有那一小部分的代码（热点代码），而这也就是 JIT 所需要编译的部分（hotspot的惰性评估）

编译型语言：通过编译器将源代码**一次性翻译成机器码**，速度块，开发效率低。（c/c++/Go）
解释型语言：通过解释器将源代码**一句一句解释为机器码**，速度慢，可移植性强，开发效率高（python JS）

## Java vs C++，面向过程vs面向对象

都是面向对象的语言，都支持封装、继承和多态

指针：Java 不提供指针来直接访问内存，程序内存更加安全
类多继承：Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
GC：Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
操作符重载：C ++同时支持方法重载和操作符重载，但是 Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）

面向过程：把问题拆解成一个个方法，通过方法解决问题。C语言，Linux Unix
面向对象：先抽象出对象，通过对象调用方法解决问题。更易维护、扩展、复用。
并不一定：面向过程的效率更高，因为面向过程也涉及到分配内存，计算内存偏移量。
java半编译半解释面向对象，C是编译型面向过程。C++编译型面向对象，C ++和C的效率就差不多，**Java比C效率低，所以原因不在面向对象，而在解释器上。**

## Oracle JDK vs Open JDK 

|        | Open JDK               | Oracle JDK                                                   |
| ------ | ---------------------- | ------------------------------------------------------------ |
| 免费   | 完全免费               | 有免费时间限制                                               |
| 开源   | 完全开源，可以修改优化 |                                                              |
| 功能性 |                        | 在前者上添加功能，比如JFR（Java Flight Recorder）、<br />JMC（Java Mission Control）监控工具 |
| 稳定性 | 更新很快，3个月一次    | 6个月一次                                                    |

## 注释与注解

注释：单行注释（//）多行注释（/*  */）文档注释（/ * * */）

注解：@注释名的形式，对程序做出解释， 可以被其他程序(比如:编译器等)读取，是有注解信息的处理流程的。本质是一个继承了Annotation 的特殊**接口**，注解不支持继承。

### 四类元注解

```java
1.@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
2.@Retention(RetentionPolicy.SOURCE)
每个注解都需要有@Target（约束注解可以应用的地方，如方法method、类接口type、参数parameter、包package、构造函数constructor） @Retention（约束注解的生命周期，源码级别source、类文件级别class、运行时级别runtime），默认是class
SOURCE：注解在编译的时候消失（该类型的注解信息只会保留在源码里，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class文件里）
CLASS（默认）：注解在解释的时候消失（该类型的注解信息会保留在源码里和class文件里，在执行的时候，不会加载到虚拟机中），如Java内置注解，@Override、@Deprecated、@SuppressWarnning等
RUNTIME：在JVM中也保留，因此可以通过反射机制读取注解的信息，如SpringMvc中的@Controller、@Autowired、@RequestMapping等。
    
3.@Inherited可以让注解被继承，但不是真正继承，被修饰的类的子类可以通过对象.getClass().getAnnotations获取该注解。
4.@Documented：描述注解是否被抽取到api文档中，会被直接生成到javadoc中
public @interface SuppressWarnings {
    String[] value();
}

@Inherited
@Documented
@Target({ElementType.FIELD,ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface DocumentA {} // 这个注解是有Inherited的

@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface DocumentB{} // 这个注解没有Inherited

@DocumentA class A {}
@DocumentB class B {}

class AClass extends A {} // 继承有继承注解的A
class BClass extends B {} // 继承没有继承注解的B
public class DocumentDemo {
    public static void main(String[] args) {
        A instanceA = new A();
        AClass aClass = new AClass();
        System.out.println("@DocumentA class A：" + Arrays.toString(instanceA.getClass().getAnnotations()));
        System.out.println("A的子类：" + Arrays.toString(aClass.getClass().getAnnotations()));
        B instanceB = new B();
        BClass bClass = new BClass();
        System.out.println("@DocumentB class B:"+Arrays.toString(instanceB.getClass().getAnnotations()));
        System.out.println("B的子类：" + Arrays.toString(bClass.getClass().getAnnotations()));
    }
}
/**
@DocumentA class A：[@base.override.DocumentA()]
A的子类：[@base.override.DocumentA()]
@DocumentB class B:[@base.override.DocumentB()]
B的子类：[]
*/
```



@Test：起标记作用，告诉测试框架该方法为测试方法。
@Override：覆盖方法，重写父类方法
@Deprecated：已过时的方法
@SuppressWarnnings("unchecked")：有选择的关闭警告，传递一个string，包括path（路径不存在警告）、finally（子句不完成警告）、unchecked（未检查的转换警告）、all（以上全部警告）

### SpringMVC注解

bean对象：类的代理或代言人（实际上确实是通过反射、代理来实现的），这样它就能代表类拥有该拥有的东西了

1.*注册bean的注解*（将对象转化为bean对象，交给IOC容器）：

@Mapper：数据层 (Mapper)
@Service ：业务层 (Service)
@Repository ：**持久层 / 数据访问层组件(Dao)**
@Component ：描述各种组件(当组件不好归类时)
@RestController：控制层(Controller)并返回**JSON数据类型**，但不会再执行配置的视图解析器，也不会返回给jsp（java servlet pages）java服务器页面，返回值就是return里的内容。
该注解等同于：@Controller + @ResponseBody
@Controller：控制层 接收用户请求 执行 视图解析器 。
返回一个ModelAndView对象，传给指定的jsp（将传过来的ModelAndView对象解析后展示在页面上），封装后，最后返回给客户端一个Html页面。
@ResponseBody 将Java对象转为JSON。如果用ajax接收请求，必须使用该注解。如果没有加此注解，返回的是一个页面，return里面的内容会被认做是需要跳转页面的地址。
`@PathVariable`用于获取路径参数，`@RequestParam`用于获取查询参数

@Component VS @Bean：
@Component 注解作用于类，而@Bean注解作用于方法，是一个类的实例。

### IOC容器注解

IOC（Inversion of Conctrol）：依赖注入（DI），将设计好的对象的控制权交给spring框架来管理，使用注解获取而不是new对象。控制（对象创建实例化管理的权力）反转（控制权交给外部环境，spring IOC容器）。
需要的时候通过注解来注入(获取)，而不是传统的在你的对象内部直接控制(new 对象)。从而降低了程序的耦合性。

2.*使用Bean的注解*（已经配置好的bean从IOC中拿出来用，完成属性、方法的组装）：
@Autowired 按byType自动注入，获取对应的bean对象。如果注入的类型有多个实现类，则需要注入具体实现类的名称。
@Resource 按byName自动注入，获取对应的bean对象。（在声明一个Dao或者Service的对象之前）
当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称

@Value 注入普通类型属性。（获取application.yml里面的常量时）

### RequestMapping注解

指定控制器可以处理哪些URL请求。
@GetMapping 查询方法
@PostMapping增添保存方法
@DeleteMapping删除方法
@PutMapping更新方法
@PatchMapping更新局部方法

### 其他注解

@author @param @return @exception @link @version // javadoc注解

@Select @Update @Insert @Delete // 数据库操作注解

## 标识符、关键字、可变长参数

标识符就是一个名字，关键字是特殊的标识符（访问控制类型private protected public，类变量修饰符，程序控制，错误处理，基本类型，变量引用super this void）

静态变量可以被类的所有实例共享。无论一个类创建了多少个对象，它们都共享同一份静态变量。通常情况下，静态变量会被 final 关键字修饰成为常量。

可变长参数：可以接受 0 个或者多个参数。**可变参数只能作为函数的最后一个参数**。
遇到方法重载的时候，会优先匹配固定参数的方法，因为匹配度更高。

**破坏了HashSet键值的唯一性**。所以**千万不要用可变类型做HashMap和HashSet键值。**

volatile：多线程可见

## 基本数据类型和包装类型

| 基本数据类型/引用类型        | 字节数                                  | 基本类型默认值 | 取值范围                     |
| ---------------------------- | --------------------------------------- | -------------- | ---------------------------- |
| byte/Byte                    | 1                                       | 0              | -2^7~2 ^ 7-1                 |
| short/Short                  | 2                                       | 0              |                              |
| **int/Integer**              | 4                                       | 0              | 2^31 -1 21亿多 10位数        |
| long/Long                    | 8                                       | 0              |                              |
| float/Float                  | 4                                       | 0              |                              |
| double/Double                | 8                                       | 0              |                              |
| **char/Charactor**           | 2                                       | 'u0000'        |                              |
| boolean/Boolean              | 1                                       | false          | true false                   |
| long型必须加L，否则会当作int |                                         |                |                              |
| 数组长度                     | 具体能放多少与 JVM 内存有关，比理论值小 |                | int理论值是Integer.MAX_VALUE |
|                              |                                         |                |                              |

基本与包装的区别：
1.成员变量包装类型不赋值就是 `null` ，而基本类型有默认值。所以可以区分出已赋值和未赋值的区别。
2.包装类型可用于泛型，而基本类型不可以。
3.基本数据类型的局部变量存放在 Java 虚拟机栈中的局部变量表中，基本数据类型的成员变量（未被 `static` 修饰 ）存放在 Java 虚拟机的堆中。包装类型属于**对象类型**，我们知道几乎所有对象实例都存在于堆中。
4.相比于对象类型， 基本数据类型占用的空间非常小

Integer提供了多个与整数相关的操作方法，例如，将一个字符串转换成整数，Integer中还定义了表示整数的最大值和最小值的常量。

**<font color='red'>基本数据类型存放在栈中是一个常见的误区！</font>**非static的成员变量存放在堆中（属于对象）
**<font color='red'>所有整型包装类对象之间值的比较，全部使用 equals 方法比较</font>。**

包装类型**缓存机制**：
除了float和double之外的6大基本类型都有缓存机制。`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`
通过new Interger()创建Interger对象，每次返回全新的Integer对象
通过Interger.valueOf()创建Interger对象，如果值在-128到127之间，会返回缓存的对象(初始化时)。

装箱和拆箱：装箱：int -> integer,调用Integer.ValueOf(a)；拆箱：Integer -> int，调用A.intValue()

浮点数运算精度丢失：因为计算机存储float的位数为32位，会产生截断。用BigDecimal解决。

解决二进制表示浮点数运算精度丢失的问题（涉及钱）。浮点数之间的等值判断，基本数据类型不能用 == 来比较，包装数据类型不能用 equals 来判断。

```java
System.out.println(c.setScale(5,RoundingMode.HALF_UP)); // 保留5位小数
        System.out.println(a.add(b)); // +
        System.out.println(a.subtract(b)); // -
        System.out.println(a.multiply(b)); // *
        System.out.println(a.divide(b,9, RoundingMode.HALF_UP)); // 除以，保留9位小数
        System.out.println(a.compareTo(c)); // 比较，小于是-1，等于是0，大于是1
```

超过long的整型：用BigInteger

## 深拷贝、浅拷贝

- **浅拷贝**：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），两个引用指向两个对象，两个对象指向同一个地址。
- **深拷贝** ：深拷贝会**完全复制整个对象**，包括这个对象所包含的内部对象。两个引用指向两个对象，两个对象地址不同。
- 引用拷贝：两个引用指向同一个对象

默认的super.clone()是浅复制，写clone时，必须先写这句话，因为首先要把父类中的成员复制到位，然后才是复制自己的成员。

![浅拷贝、深拷贝、引用拷贝示意图](https://oss.javaguide.cn/github/javaguide/java/basis/shallow&deep-copy.png)

## Object类

所有类的父类，但不包括基本数据类型byte short int long float double boolean char。

常用方法11个：clone getClass  equals toString  hashCode wait notify notifyall

### ==和equals

==操作符：两个基本数据类型比较值，两个引用类型比较内存地址（链表是否是同一个）
equals：不能用于判断基本数据类型的变量；
				没有重写Object的equals——等价于前者；
				重写了——用来判断两个对象是否相等，即**内容**是否相同，比较的两个对象是独立的。（String）

==对象相等指的内容，引用相等指地址==

### hashCode和equals

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode() **在散列表中才有用，在其它情况下没用**。

hashCode()和equals都是用来比较两个对象是否相等。
如果两个对象的`hashCode` 值相等，这两个对象不一定相等，需要调用equals方法判断是否真的相等。
		如果不等，说明==哈希冲撞==：开放地址法（线性探测，平方探测），拉链法，再哈希法，建立公共溢出区（将发生冲突的都存放在溢出表中）
		如果equals返回true，我们才认为这两个对象相等。hashset就不加入。
如果两个对象的`hashCode` 值不相等，我们就可以直接认为这两个对象不相等。不需要调用equals，缩小查找成本。

重写equals方法必须重写hashCode方法：
在散列表中，只有a.hashCode() == b.hashCode() && a.equals(b)的时候，两个对象才认为相等。如果重写equals没有重写hashCode，会导致equals的两个值，hashCode不等，那set里面就有问题了。



11.两个对象值相同(x.equals(y) == true)，但却可有不同的hash code？
对。
如果对象要保存在HashSet或HashMap中，它们的equals相等，那么，它们的hashcode值就必须相等。
如果不是要保存在HashSet或HashMap，则与hashcode没有什么关系了，这时候hashcode不等是可以的，例如arrayList存储的对象就不用实现hashcode，当然，我们没有理由不实现，通常都会去实现的。

## String、StringBuffer

String被设计成**不可变(immutable)类**，（对象内容不可变，只能<font color='red'>生成新对象并改变引用</font>）。**String类是final类，属于叶子类，故不可以继承。**

final String是非常严格的，final要求对象引用不可变（对象内容可变），String要求对象内容不可变（引用可变），这样一来整个不能改了。

`StringBuffer` 和StringBuilder每次都对对象本身进行操作，而不是生成新的对象并改变对象引用。Builder线程不安全，比StringBuffer获得 10%~15%的性能提升；StringBuffer有同步锁，线程安全。
StringBuffer、StringBuilder**都没有实现equals方法**，所以，new StringBuffer(“abc”).equals(new StringBuffer(“abc”)的结果为false。

操作少量的数据: 适用 `String`
单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`

### String为什么不可变：

为了**安全**。不只String，很多Java标准类库中的类都是不可变的。在开发一个系统的时候，我们有时候也需要设计不可变类，来传递一组相关的值，这也是面向对象思想的体现。不可变类有一些优点，比如因为它的对象是只读的，所以**多线程并发访问**也不会有任何问题。当然也有一些缺点，比如每个不同的状态都要一个对象来代表，可能造成性能上的问题。作为HashSet和HashMap的键值。**千万不要用可变类型做HashMap和HashSet键值。**

### String 的+ 和+=

Java 语言本身并不支持运算符重载，**“+”和“+=”是专门为 String 类重载过的运算符**，也是 Java 中仅有的两个重载过的运算符。
字符串对象通过“+”的字符串拼接方式，实际上是通过 `StringBuilder` 调用 `append()` 方法实现的，拼接完成之后调用 `toString()` 得到一个 `String` 对象 。对于如下代码：

```java
String s="a"+"b"+"c"+"d"; // 创建一个对象
System.out.println(s == "abcd"); // true
String s1 = "a";
String s2 = s1 + "b";
String s3 = "a" + "b";
System.out.println(s2 == "ab"); // false
System.out.println(s3 == "ab"); // true，javac编译可以对字符串常量直接相加的表达式进行优化
```

### 字符串常量池 && intern

**字符串常量池存在于方法区，不会被GC**

 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了**避免字符串的重复创建**。已经创建的字符串从缓冲区拿。
对于字符串常量，如果内容相同，Java认为它们代表同一个String对象。而用关键字new调用构造器，**总是会创建一个新的对象**，无论内容是否相同。所有不要用new String("aa")

String s = new String("xyz");创建了几个String Object? 二者之间有什么区别？
**两个或一个**，”xyz”对应一个对象，这个对象放在字符串常量缓冲区，常量”xyz”不管出现多少遍，都是缓冲区中的那一个。New String每写一遍，就创建一个新的对象，它一句那个常量”xyz”对象的内容来创建出一个新String对象。如果以前就用过’xyz’，这句代表就不会创建”xyz”自己了，直接从缓冲区拿。

对于直接声明的字符串，形如：String x = ""; 则变量x直接指向常量池中；
对于new出来的字符串，new String(""); 则存储于堆中，是一个新的对象，但存储的是指向常量池的引用（类似于浅拷贝，新对象指向同一个地址）；
**intern**方法：如果字符串常量池中保存了对应的字符串对象的引用，就直接返回该引用。否则，向常量池存储字符串，并返回一个常量池的引用对象。



### 字符串转成数组

1）用正则表达式，代码大概为：

```java
String [] result = orgStr.split(",");
```

2）用 StringTokenizer ,代码为：

```java
StringTokenizer tokener = new StringTokenizer(orgStr,",");
String [] result = new String[tokener .countTokens()];
int i=0;
while(tokener.hasMoreElements())
{result[i++]=tokener.nextToken();}
System.out.println(Arrays.toString(result));
```



## Throwable 异常和错误

### Exception error

![Java 异常类层次结构图](https://oss.javaguide.cn/github/javaguide/java/basis/types-of-exceptions-in-java.png)



exception和error都是继承Throwable类

error 程序本身无法恢复或预防，程序只有死的份了，必须修改程序，比如说内存溢出OutOfMemory，栈溢出StackOver，虚拟机错误VirtualMachineError。

exception 程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。包括RuntimeException（运行时异常，虚拟机的通常操作中可能遇到的异常，软件开发人员考虑不周所导致，java编译器**不要求抛出**）、CheckedException（编译时异常，需要抛出，运行环境的变化或异常所导致，用try catch捕获）

**Unchecked Exception / RuntimeException、运行时异常、系统异常:**
illegalArgumentException：此异常表明向方法传递了一个不合法或不正确的参数。
illegalStateException：在不合理或不正确时间内唤醒一方法时出现的异常信息。换句话说，即 Java 环境或 Java 应用不满足请求操作。
NullpointerException：空指针异常（我目前遇见的最多的）
IndexOutOfBoundsException：索引超出边界异常
ClassCastException：类转换异常

**CheckedException、受检查异常、编译时异常、普通异常、一般异常:**
我们在编写程序过程中try——catch捕获到的一场都是CheckedException。
io包中的IOExecption**、ClassNotFoundException、SQLException、RuntimeException**

### Throwable常用方法

`String getMessage()`: 返回异常发生时的简要描述
`String toString()`: 返回异常发生时的详细信息
`String getLocalizedMessage()`: 返回异常对象的本地化信息。如果没有子类重写，那等同于getMessage()
`void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

### try-with-resources VS try catch finally

**<font color='red'>不要在 finally 语句块中使用return</font>**

finally的语句一定执行吗？finally 之前虚拟机被终止运行/程序所在的线程死亡/关闭 CPU，finally 中的代码就不会被执行。

除此之外，即使try里面return，finally子句也一定会执行。如果return和finally里面都有return，实际返回的是finally的return。return并不是让函数马上返回，而是return语句执行后，将把返回结果放置进函数栈中，此时函数并不是马上返回，它要执行finally语句后才真正开始返回。

面对必须要关闭的资源，我们总是应该优先使用 `try-with-resources` 而不是`try-finally`。随之产生的代码更简短，更清晰，产生的异常对我们也更有用。不需要手动关闭，执行scanner.close()
Java 中类似于`InputStream`、`OutputStream` 、`Scanner` 、`PrintWriter`等的资源都需要我们调用`close()`方法来手动关闭。

```java
    void tryWithResource() {
        try (Scanner scanner = new Scanner(new File("D://read.txt"));
 Scanner scanner1 = new Scanner(new File("D://read2.txt"))) { // 声明多个资源，这些都会在try catch之后关闭
while (scanner.hasNext()) {
    System.out.println(scanner.nextLine());
}
        } catch (FileNotFoundException e) {
e.getLocalizedMessage();
e.getMessage();
        }
    }
    @Override
    public Person clone() {
        try {
Person person = (Person) super.clone();
person.setAddress(person.getAddress().clone());
return person;
        } catch (CloneNotSupportedException e) {
throw new AssertionError(); // 抛出异常new一个变量
        }
    }
```

### 异常注意点

**不要把异常定义为静态变量**，因为这样会导致异常栈信息错乱。每次手动抛出异常，我们都需要手动 new 一个异常对象抛出。

建议抛出**更加具体**的异常：比如字符串转换为数字格式错误的时候应该抛出`NumberFormatException`而不是其父类`IllegalArgumentException`。

使用日志打印异常之后就不要再抛出异常了（两者不要同时存在一段代码逻辑中）

### final, finally, finalize的区别。

final 用于声明属性，方法和类，分别表示**<font color='red'>属性不可变，方法不可覆盖，类不可继承</font>**。

内部类要访问局部变量，局部变量必须定义成final类型。

**finally是异常处理语句结构的一部分，表示总是执行。**

finalize是Object类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的**其他资源回收，例如关闭文件**等。JVM不保证此方法总被调用。

## 泛型

泛型本质上将类型参数化，多种类型进行同样的处理。可以增强代码可读性。
原生 List 返回类型是 Object ，需要手动转换类型才能使用，使用泛型后编译器自动转换。

泛型类、泛型接口、泛型方法

```java
// 1.泛型接口
interface Generator<M> {
    public M methods();
}
// 此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
// 2.泛型类，表示处理该数据类型的对象
public class Generic<E> implements Generator {
    private E key;

    public E getKey() {return key;}
    public void setKey(E key) {this.key = key;}
    public Generic (E key) {this.key = key;}

    @Override
    public E methods() { return getKey();}
// 3.泛型方法，返回类型前一定要写<T>
    public <T> void printArray(T[] arr) {
        for (T t : arr) {
System.out.printf("%s," , t);
        }
        System.out.println();
    }

    public static void main(String[] args) {
        Generic<String> generic = new Generic<>("abcd");
        System.out.println(generic.methods());
        generic.printArray(new Integer[]{1,2,3});
    }
}
```

## 反射机制

对象可以通过**反射获取类**，类可以通过反射拿到所有方法(包括私有)。

spring/Spring Boot、MyBatis 等等框架中都大量使用了反射机制，还使用了**动态代理**，而动态代理也依赖于反射机制。

**注解**也使用了反射机制。基于反射分析类，然后获取到类/属性/方法/方法的参数上的注解。因此，一个`@Component`注解就声明了一个类为 Spring Bean，通过一个 `@Value`注解就读取到配置文件中的值。

反射可以让我们的代码更加灵活、为各种框架提供**开箱即用**的功能提供了便利。
不过，反射让我们在运行时有了分析操作类的能力的同时，也增加了**安全问题**，比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）privateMethod.setAccessible(true)。另外，反射的性能也要稍差点，不过，对于框架来说实际是影响不大的。

类.getDeclaredMethods()——返回方法数组Method[]；
类.getDeclaredMethods("方法名",String.class)——返回Method，获取到该方法
方法名.**invoke**(对象，参数)——调用该方法
类.getDeclaredField(参数名)——获取到类的参数

```java
        /*1. 获取 TargetObject 类的 Class 对象并且创建 TargetObject 类实例  */
        Class<?> targetClass = Class.forName("base.override.TargetObject");
        TargetObject targetObject = (TargetObject) targetClass.newInstance();
        /*2.getDeclaredMethods获取 TargetObject 类中定义的所有方法  */
        Method[] methods = targetClass.getDeclaredMethods();
        for (Method method : methods) {
System.out.println(method.getName());
        }
        /*3.getDeclaredMethods 获取指定方法并调用         */
        Method publicMethod = targetClass.getDeclaredMethod("publicMethod",String.class);
        publicMethod.invoke(targetObject, "JavaGuide"); 
        // 创建的对象：targetObject；调用的方法：publicMethod；传递的参数：JavaGuide

        /*4.getDeclaredField获取指定参数并对参数进行修改         */
        Field field = targetClass.getDeclaredField("value");
        //为了对类中的参数进行修改我们取消安全检查
        field.setAccessible(true);
        field.set(targetObject, "JavaGuide222");
```



### 获取类对象的4方法

```java
        // 1.类名.class
        System.out.println(Base.class);
        // 2.对象.getClass()
        System.out.println(base.getClass());
        try  {
// 3.Class.forName("全路径")
System.out.println(Class.forName("base.Base"));
// 4. 通过类加载器 ClassLoader.getSystemClassLoader().loadClass("全路径")
System.out.println(ClassLoader.getSystemClassLoader().loadClass("base.Base"));
        } catch (ClassNotFoundException a) {
a.printStackTrace();
        }

```

通过类加载器获取 Class 对象不会进行初始化，意味着不进行包括初始化等一系列步骤，静态代码块和静态对象不会得到执行

### SPI VS API

**为某个接口寻找服务实现的机制**，这有点类似 IoC 的思想，**将装配的控制权移交到了程序之外**。SPI 即 Service Provider Interface ，专门提供给服务提供者或者扩展框架功能的开发者去使用的一个接口。即调用方制定规则，服务提供者根据规则去实现。

API：接口和实现都是放在实现方，我们调用实现方的接口从而拥有实现方给我们提供的能力。
SPI：接口存在于调用方，由调用方确定接口规则，然后由不同的厂商去根据这个规则对这个接口进行实现，从而提供服务。

很多框架都使用了 Java 的 SPI 机制，比如：Spring 框架、数据库加载驱动、日志接口、以及 Dubbo 的扩展实现。SPI 机制的具体实现本质上还是通过**反射**完成的。即：**我们按照规定将要暴露对外使用的具体实现类在 `META-INF/services/` 文件下声明。**

例如SLF4J（调用方） 是 Java 的一个日志接口，其具体实现有几种，比如：Logback、Log4j、Log4j2（实现方） 等等，而且还可以切换，在切换日志具体实现的时候我们是不需要更改项目代码的，只需要在 Maven 依赖里面修改一些 pom 依赖就好了。也就是说，不同厂家提供不同方案的实现，但是我们所用到的调用方法是同样的。

![img](https://oss.javaguide.cn/github/javaguide/java/basis/spi/image-20220723213306039-165858318917813.png)

## 代理机制

在目标对象的某个方法执行前后你可以增加一些自定义的操作（封装、过滤等），分为静态代理和动态代理。

静态代理：手动完成对目标对象每个方法的增强（比如A实现了interface接口，ProxyA也实现了interface接口，并且调用了A的重写函数，对该函数进行改善）。在编译时就将接口、实现类、代理类这些都变成了一个个实际的 class 文件。

动态代理：动态代理是在运行时**动态生成类字节码**，并加载到 JVM 中的。
Spring AOP、RPC 框架都依赖了动态代理。动态代理在我们日常开发中使用的相对较少，但是**在框架中的几乎是必用的一门技术**。
JDK动态代理： `InvocationHandler` 接口和 `Proxy` 类是核心。
GLIB 动态代理：JDK 动态代理有一个最致命的问题是其只能代理实现了接口的类。为了解决这个问题，我们可以用 CGLIB 动态代理机制来避免。

## 序列化和反序列化

我们有时候将一个java**对象**变成**字节流**的形式去**网络传输RPC、存储到文件、内存、数据库**（JRE的OutputStream的writeObject方法，序列化），或者从一个字节流中恢复成一个java对象（反序列化）。发生在TCP/IP四层**应用层**（网络接口层，网络层、传输层、应用层）

1.JDK自带的序列化：

不推荐使用：很重效率低，存在安全问题，不支持跨语言
需要被序列化的类必须实现Serializable接口，该接口是一个mini接口，其中没有需要实现的方法，implements Serializable只是为了标注该对象是可被序列化的。
serialVersionUID：是一个static类型的版本号，反序列化时进行版本控制。**static修饰的变量不属于任何对象，因此不会被序列化**。
**transient**：①==**阻止变量被序列化（不能修饰类、方法）**==；②当对象被反序列化时，transient修饰的变量**不会被持久化和恢复**，会成默认值。③用于一些不想被序列化的信息（密码、银行卡等敏感信息），不想在网络中传输，让transient修饰的字段的生命周期仅存于调用者的内存中而不会写到磁盘里持久化。

2.Kryo
高性能的序列化/反序列化工具，由于其**变长存储**特性并使用了**字节码**生成机制，所以体积小速度快。但是其使用也只能限制在基于 JVM 的语言上（Java Scala Kotlin）。其序列化出的结果是二进制的，也即 byte[]，因此像 **Redis** 这样可以存储二进制数据的存储引擎是可以直接将 Kryo 序列化出来的数据存进去。

3.Protobuf
支持多语言，跨平台，但需要自定义文件，繁琐。

4.Hessian
跨语言、轻量级、自定义描述的二进制 RPC 协议。Hessian 是一个比较老的序列化实现。

==Kryo 是专门针对 Java 语言序列化方式并且性能非常好，像 Protobuf、 ProtoStuff、hessian 这类都是跨语言的序列化方式。==

## 语法糖

添加语法对语言的功能并没有影响，但是更方便程序员使用。 **Java 虚拟机并不支持这些语法糖。这些语法糖在编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖。**（编译阶段的desugar()）

1.switch支持string和枚举类型（依然不支持long）： 字符串的 switch 是通过`equals()`和`hashCode()`方法来实现的。

2.<font color='red'>泛型</font>：C++和 C#是使用`Code specialization`的处理机制，而 Java 使用的是`Code sharing`的机制。对于 Java 虚拟机来说，他根本不认识`Map<String, String> map`这样的语法。需要在编译阶段通过**类型擦除**的方式进行解语法糖：将所有的泛型参数用其最左边界（最顶级的父类型）类型替换。 2.移除所有的类型参数。

```
Map<String, String> map => Map map
List<Integer>.class => List.class
A w = xi.next() => Comparable w = (Comparable)xi.next(); // A是Comparable的子类
```

3.<font color='red'>自动装箱和拆箱</font>：`int i = 1;Integer ii = i;`发生自动装箱

4.可变长参数：可以接受 0 个或者多个参数。可变参数只能作为函数的最后一个参数。遇到方法重载的时候，会优先匹配固定参数的方法，因为匹配度更高。

5.枚举类型：关键字`enum`可以将一组具名的值的有限集合创建为一种新的类型，而这些具名的值可以作为常规的程序组件使用。**编译器会自动帮我们创建一个`final`类型的类继承`Enum`类，所以枚举类型不能被继承。**

6.内部类

7.条件编译：通过判断条件为常量的 if 语句实现的，编译器直接把分支为 false 的代码块消除。

8.for-each，**<font color='red'>try-with-resource</font>**，<font color='red'>Lambda 表达式</font>

## 值传递和引用传递

<font color='red'>==**java中只有值传递，没有引用传递！**==</font>

- 基本类型：传递基本类型的**字面量值的拷贝**，会创建副本。
- 引用类型：传递实参所引用的对象在堆中**地址值的拷贝**（浅拷贝），会创建副本

## 魔法类Unsafe

执行低级别、不安全操作的方法，使 Java 语言拥有了类似 C 语言指针一样操作内存空间的能力（直接访问系统内存资源、自主管理内存资源等**底层**功能），这增加了便利，也存在着**指针的安全隐患**，因此要谨慎使用、避免滥用。

`Unsafe` 提供的这些功能的实现需要依赖**本地方法**（Native Method）。你可以将本地方法看作是 Java 中依赖于另一种编程语言的方法。本地方法使用 **`native`** 关键字修饰，Java 代码中只是声明方法头，具体的实现则交给 **本地代码**。

在 JUC （java.util.concurrent）包实现并发机制时，都调用了native方法，打破了 Java 运行时的界限，**借助其他语言对操作系统底层进行控制**。

Unsafe功能：

1. 内存操作： Java 中是不允许直接对内存进行操作的，由 JVM 自己实现的，Unsafe类提供reallocateMemory，setMemory等接口
2. **内存屏障**：编译器和 CPU 会在保证程序输出结果一致的情况下，会对代码进行重排序，优化指令提升性能。而指令重排序可能会导致 CPU 的高速缓存和内存中数据的不一致。内存屏障**阻止指令重排序**。loadFence，storeFence，fullFence接口
3. 对象操作
4. 数据操作
5. **CAS 操作**：Compare and Swap比较并替换，实现**并发**算法。CAS 操作包含三个操作数——内存位置、预期原值及新值。内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，说明有线程改过了，处理器不做任何操作。是一条 CPU 的**原子指令**，不会造成所谓的数据不一致问题
6. 线程调度：`Unsafe` 类中提供了`park`阻塞线程、`unpark`取消阻塞线程、`monitorEnter`获取对象锁（可重入锁）、`monitorExit`（释放对象锁）、`tryMonitorEnter`（尝试获取对象锁）方法进行线程调度。
7. Class 操作
8. 系统信息

# Java集合

分为Collection和Map

![img](https://oss.javaguide.cn/github/javaguide/java/collection/java-collection-hierarchy.png)

- `List`(对付顺序的好帮手): 存储的元素是**有序的**、可重复的。
- `Set`(注重独一无二的性质): 存储的元素是无序的、**不可重复**的。
- `Queue`(实现排队功能的叫号机): 按特定的排队规则来确定**先后顺序**，存储的元素是有序的、可重复的。
- `Map`(用 key 来搜索的专家): 使用**键值对**（key-value）存储，类似于数学上的函数 y=f(x)，"x" 代表 key，"y" 代表 value，key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。

**<font color='red'>线程安全：Vector和HashTable</font>**，全部加上synchronized，效率很低

## List

|                                    | ArrayList                                        | LinkedList                        | Vector                                                       |
| ---------------------------------- | ------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| **是否保证线程安全**               | 不安全，List主要实现类<br />CopyOnWriteArrayList | 不安全<br />ConcurrentLinkedQueue | 安全，List古老实现类                                         |
| 数据增长                           | ArrayList增加原来的0.5倍                         |                                   | Vector增长原来的一倍                                         |
| **底层数据结构**                   | `Object` 数组                                    | **双向链表**                      | `Object` 动态数组                                            |
| **插入和删除是否受元素位置的影响** | 数组存储，受位置影响尾O(1)头O(n)                 | 链表存储，头尾O(1)                |                                                              |
| **是否支持快速随机访问**           | 支持，实现了RandomAccess接口                     | 不支持                            | 支持                                                         |
| 性能                               |                                                  |                                   | 比ArrayList性能差                                            |
|                                    |                                                  | 作为队列和栈                      | 用在事先不知道数组的大小，或者只是需要一个可以改变大小的数组的情况。 |

## Set

无序性不等于随机性 ，无序性是**数据的哈希值**决定顺序，而非添加的索引决定顺序。

不可重复性是指添加的元素按照 `equals()` 判断时 ，返回 false，需要同时重写 `equals()` 方法和 `hashCode()` 方法。

|              | HashSet                        | LinkedHashSet | TreeSet                    |
| ------------ | ------------------------------ | ------------- | -------------------------- |
| 相同         | 都能保证唯一性，但都线程不安全 |               |                            |
| 底层数据结构 | 哈希表                         | 链表+哈希表   | 红黑树(自平衡的排序二叉树) |
| 有序与否     | 无序                           | FIFO，有序    | 自定义set排序，有序        |

## Queue

- `PriorityQueue`: `Object[]` 数组来实现**二叉堆**，控制排序
- `ArrayQueue`: `Object[]` 数组 + 双指针
- Deque:双端队列，可以模拟栈
- Queue:单边队列

|                  | ArrayDeque       | LinkedList                        | PriorityQueue                                                |
| ---------------- | ---------------- | --------------------------------- | ------------------------------------------------------------ |
| 数据结构         | 数组+双指针      | 双向链表                          | `Object[]` 数组来实现二叉堆                                  |
| **NULL类型存储** | 不支持           | 支持                              | 不支持                                                       |
| 扩容             | 数组，是需要扩容 | 不需要                            | 默认是小顶堆，但可以接收一个 Comparator 自定义排序，通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。 |
| 性能             | 更好             |                                   |                                                              |
| 线程安全         | 不安全           | 不安全<br />ConcurrentLinkedQueue | 不安全<br />PriorityBlockingQueue                            |



##  Map

- `HashMap`： JDK1.8 之前 `HashMap` 由数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间
- `LinkedHashMap`（有序）： `LinkedHashMap` 继承自 `HashMap`，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，`LinkedHashMap` 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
- `Hashtable`： 数组+链表组成的，数组是 `Hashtable` 的主体，链表则是主要为了解决哈希冲突而存在的
- `TreeMap`： 红黑树（自平衡的排序二叉树），实现了`NavigableMap`接口（对集合内元素的搜索的能力）和`SortedMap` 接口（排序能力）

|                | HashMap                                                      | ConcurrentHashMap                                            | Hashtable                                                    |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 底层数据结构   | 数组+链表+红黑树，但长度过大时会转为红黑树，长度是2幂次方    | Node 数组 + 链表 / 红黑树                                    | 数组+链表                                                    |
| 有序与否       | 无序                                                         |                                                              | 无序                                                         |
| NULL值存储     | 允许Key value为null                                          |                                                              | 不允许                                                       |
| 线程安全与否   | 不安全                                                       | ==安全==，1.8对Node加锁（之前是segement分段数组），使用 **synchronized 和 CAS** 来操作，只锁定首节点Node（**锁粒度更细**）<br />只要 hash 不冲突，就不会产生并发，不会影响其他 Node 的读写 | ==安全，是Synchronize的==，是同一把锁，效率低下（put的时候其他线程不能put也不能get） |
| 继承           | 实现Map接口                                                  |                                                              | Dictionary类的子类                                           |
| 扩容           | 未指定：初始容量16，每次扩容2倍<br />指定initial：使用initial往上找二进制全1的数 + 1，比如9(1000)->16(1111 + 1)，即HashMap的长度是2^n |                                                              | 未指定：初始容量11，每次扩容2n+1<br />指定initial：直接使用initial<br />**头插法**的方式迁移数组 |
| 计算hash值方式 | 位运算，hashcode&(length-1)                                  |                                                              | 取模运算，数组长度尽量为素数或奇数，可减少hash冲撞           |



57.List 和 Map 区别?

一个是存储单列数据的集合，另一个是存储键和值这样的双列数据的集合，List中存储的数据是有顺序，并且允许重复；Map中存储的数据是没有顺序的，其键是不能重复的，它的值是可以有重复的。

59.List、Map、Set三个接口，存取元素时，各有什么特点？

List与Set具有相似性，它们都是**单列元素的集合**，有一个共同的父接口，叫Collection。
Set里面不允许有重复的元素，Set集合的add方法有一个boolean的返回值。Set取元素时，没法说取第几个，只能以Iterator接口取得所有的元素，再逐一遍历各个元素。

List表示有先后顺序的集合， 允许重复。
**Map是双列元素的集合**，key不可重复，有put方法，可以获得所有的key的结合，还可以获得所有的value的结合，还可以获得key和value组合成的Map.Entry对象的集合。
HashSet按照hashcode值的某种运算方式进行存储，而不是直接按hashCode值的大小进行存储。
同一个对象**可以在Vecto**r中加入多次。往集合里面加元素，相当于集合里用一根绳子连接到了目标对象。往HashSet中却加不了多次的。

61.去掉一个Vector集合中重复的元素

```
HashSet set = new HashSet(vector);
```

## 扩容机制

### ArrayList扩容

`ArrayList` 的底层是**数组队列**，相当于**动态数组**。实现了 List, RandomAccess（随机访问）, Cloneable（克隆）, java.io.Serializable （序列化）这些接口。

默认**minCapacity 是10**，其余情况下minCapacity =size + 1。每当minCapacity  > elementData.length，需要执行**grow方法**，将elementData.length扩容为newCapacity = max{ minCapacity, oldCapacity + (oldCapacity >> 1)}，oldCapacity =elementData.length，相当于**原数组的1.5倍左右**。如果newCapacity >MAX_ARRAY_SIZE (MAXVALUE - 8)，那就进入**hugeCapacity()**，扩容到MAXVAULE或者MAX_ARRAY_SIZE 。
在向 ArrayList 添加大量元素之前用 **ensureCapacity** 方法，以减少增量重新分配的次数

1.add方法：

- 当我们要 add 进第 1 个元素到 ArrayList 时，elementData.length 为 0 （因为还是一个空的 list），因为执行了 `ensureCapacityInternal()` 方法 ，所以 minCapacity 此时为 10。此时，`minCapacity - elementData.length > 0`成立，所以会进入 `grow(minCapacity)` 方法。
- 当 add 第 2 个元素时，minCapacity 为 2，此时 e lementData.length(容量)在添加第一个元素后扩容成 10 了。此时，`minCapacity - elementData.length > 0` 不成立，所以不会进入 （执行）`grow(minCapacity)` 方法。
- 添加第 3、4···到第 10 个元素时，依然不会执行 grow 方法，数组容量都为 10。

* 直到添加第 11 个元素，minCapacity(为 11)比 elementData.length（为 10）要大。进入 grow 方法进行扩容

2.grow方法：

* 当 add 第 1 个元素时，oldCapacity 为 0，经比较后第一个 if 判断成立，newCapacity = minCapacity(为 10)。但是第二个 if 判断不会成立，即 newCapacity 不比 MAX_ARRAY_SIZE 大，则不会进入 `hugeCapacity` 方法。数组容量为 10，add 方法中 return true,size 增为 1。

* 当 add 第 11 个元素进入 grow 方法时，newCapacity 为 15，比 minCapacity（为 11）大，第一个 if 判断不成立。新容量没有大于数组最大 size，不会进入 hugeCapacity 方法。数组容量扩为 15，add 方法中 return true,size 增为 11。

### System.arraycopy()和Arrays.copyOf()

后者调用了前者。arraycopy(5参数)是需要指定目标数组，将原数组拷贝到你自己定义的数组里或者原数组。
copyOf(2参数)内部创建了一个目标数组并返回，内容是原数组的[0,length)

```java
        // 将a[2,2+4)复制到a[3,3+4)
        System.arraycopy(a, 2, a, 3, 4);
        System.out.println(Arrays.toString(a));
        // b中是a[0,5)
        int[] b = Arrays.copyOf(a,5);
        System.out.println(Arrays.toString(b));
```

### HashMap扩容 & 长度 & 死循环 & 遍历方法

1.7之前解决哈希冲突是拉链法（数组+链表，存储哈希值+解决哈希冲撞）。1.8开始，用链表+红黑树解决哈希冲突。当链表长度大于阈值（默认为 8）时，执行treeIfBin方法，如果hash表的长度<=64，先进行数组扩容resize()；否则将链表转化为红黑树，以减少搜索时间。

resize()操作：Hash表的尺寸和容量非常的重要。一般来说，Hash表这个容器当有数据要插入时，都会检查容量有没有超过设定的thredhold。如果超过，需要增大Hash表的尺寸，但是这样一来，整个Hash表里的无素都需要被重算一遍。这叫**rehash**，这个成本相当的大。

为什么长度是2^n：为了**取余计算的高效**。Hash 值的范围值-2147483648 到 2147483647，一个 40 亿长度的数组，内存是放不下的。所以要先做对数组的长度取模运算，得到的余数是存放在hash表的数组下标。 hash%length==**hash&(length-1)**的前提是length= 2^n。比如hash值为100，hash表长度16，取余运算为1100100 & 1111 = 100 = 4，计算快捷。

HashMap如果**多线程并发执行rehash**，可能会对hash冲突的部分形成**环形链表**，那么对于新加进来的冲突元素就死循环了。

迭代器、for、lambda、stream，以及具体的 7 种遍历方法，**`EntrySet` 的性能比 `KeySet` 的性能高**出了一倍，因为 `KeySet` 相当于循环了两遍 Map 集合，而 `EntrySet` 只循环了一遍。 
我们不能在遍历中使用集合 map.remove() 来删除数据，这是非安全的操作方式，但我们可以使用**迭代器的 iterator.remove()**。
我们应该尽量使用==迭代器（Iterator）来遍历 `EntrySet` 的遍历方式来操作 Map 集合==，这样就会既安全又高效了。

```java
        Iterator<Map.Entry<Integer,String>> iterator = map.entrySet().iterator();
        while (iterator.hasNext()) {// 迭代器删除，安全，for删除是不安全的
Map.Entry<Integer,String> entry = iterator.next();
if (entry.getKey() == 1 || entry.getKey() == 3)
{
    iterator.remove();
}
        } 
        map.entrySet().removeIf(entry -> entry.getKey() == 1 || entry.getKey() == 3); // 也是迭代器
        Iterator<Map.Entry<Integer,String>> iterator1 = map.entrySet().iterator();
        while (iterator1.hasNext()) { // 迭代器遍历，使用entrySet比keySet更高效
Map.Entry<Integer,String> entry = iterator1.next();
System.out.println(entry.getKey() + ":" + entry.getValue());
        }
```

### put方法

如果table为空，resize扩容（默认容量16，加载因子loadFactor=0.75）。计算hash值，hash位置没有值，直接插入；hash位置有值，判断是否equals。如果相等则替换，不相等说明有hash冲突，需要解决。
解决冲突：判断是红黑树还是链表。如果是红黑树，那就插入；如果是链表，那就插入尾节点，并判断链表长度>8，如果链表长度>8 && 哈希表长度>64(默认是16)，则将链表转为红黑树。
插入元素完成后，检查此时的++size> threshold(threshold = capacity * loadFactor，初始threshold = 16*0.75=12).如果大于，则需要resize()，capacity = 2 *capacity ，重算所有元素的hash值。

![ ](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019-7/put%E6%96%B9%E6%B3%95.png)



## comparable 和 Comparator 

comparable是compareTo方法，是implement Comparable重写该方法，用于新定义class的排序
comparator是compare方法，在Collections.sort或者Arrays.sort里面可以直接重写，或者在new PriorityQueue<int[]>(new Comparator<int[]>() {});

[跳转查看使用](#lamda表达式)

## 注意事项

集合判空：使用 `isEmpty()` 方法，而不是 `size()==0` 的方式。（时间复杂度总是O1）

集合转map：在使用**Collectors 类的 toMap()** 方法转为 Map 集合时，一定要注意当 value 为 null 时会抛 NPE 异常。

```java
class Person {
    private String name;
    private String phoneNumber;
     // getters and setters
}

List<Person> bookList = new ArrayList<>();
bookList.add(new Person("jack","18163138123"));
bookList.add(new Person("martin",null));
// 空指针异常
bookList.stream().collect(Collectors.toMap(Person::getName, Person::getPhoneNumber));

```

集合增删：不要在 foreach 循环里进行元素的 `remove/add` 操作。remove 元素请使用 `Iterator` 方式，如果并发操作，需要对 `Iterator` 对象加锁。Iterator<Map.Entry<Integer,String>> iterator = map.entrySet().iterator();

集合去重：用set.containsKey O1，避免使用 `List` 的 `contains()` 进行遍历去重或者判断包含操作O(n)

集合转数组，数组转集合

```java
//集合转数组
        String[] ss2 = list.toArray(new String[0]); // List<String>转 String[],指定泛型
        Integer[] test = integerList.toArray(new Integer[0]); // List<Integer>转Integer[]
        int[] tes = integerList.stream().mapToInt(x -> x).toArray(); // List<Integer>转int[],mapToInt
// 数组转集合，stream.collect(Collectors.toList())
List<String> list = Arrays.stream(s).collect(Collectors.toList()); //  String[]转list
List intList = Arrays.stream(ints).boxed().sorted((x,y) -> Integer.compare(x,y)).collect(Collectors.toList() ); // int[]转list<Integer>，boxed封装之后可以用sorted了
List interList = Arrays.stream(ints2).sorted((x,y) -> y.compareTo(x)).collect(Collectors.toList());
// Integer[]转list<Integer>
```

# java并发

## 进程线程概述

1.进程：动态，系统运行程序的基本单位（main函数），资源分配和调度的基本单位
线程：CPU调度的基本单位，Java 程序天生就是多线程程序（main函数中有多个线程）。多个线程共享进程的**堆**和**方法区**、**元空间**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**。线程间<font color='red'>切换开销小，可以很好地并发编程</font>。

2.区别：基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。多线程好处：轻量级进程，减少切换开销，提高并发性，提高资源利用效率（可以用多核CPU）。但是存在<font color='red'>内存泄漏、死锁、线程不安全</font>。

3.为什么私有这三者：
程序计数器：为了线程切换后能**恢复到正确的执行位置**，继续原来的流程控制。
虚拟机栈、本地方法栈：为了保证线程中的**局部变量不被别的线程访问到**。

4.同步和异步：
同步：数据将在线程间共享（例如正在写的数据以后可能被另一个线程读到，或者正在读的数据可能已经被另一个线程写过了，那么这些数据就是**共享数据），必须进行同步存取。**
异步：**不希望让程序等待方法的返回时，就应该使用异步编程**，在很多情况下采用异步途径往往更有效率。

5.线程同步方式：下面是几种常见的线程同步的方式：

**互斥锁(Mutex)**：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。比如 Java 中的 `synchronized` 关键词和各种 `Lock` 都是这种机制。
**读写锁（Read-Write Lock）**：允许多个线程同时读取共享资源，但只有一个线程可以对共享资源进行写操作。
**信号量(Semaphore)** ：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量。
**屏障（Barrier）** ：屏障是一种同步原语，用于等待多个线程到达某个点再一起继续执行。当一个线程到达屏障时，它会停止执行并等待其他线程到达屏障，直到所有线程都到达屏障后，它们才会一起继续执行。比如 Java 中的 `CyclicBarrier` 是这种机制。
**事件(Event)** :Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作



**启动一个线程是调用start()方法**，使线程**就绪**状态，以后可以被调度为运行状态，一个线程必须关联一些具体的执行代码，run()方法是该线程**所关联的执行代码**。调用 start() 方法可启动线程并使线程进入就绪状态，直接执行 run() 方法的话**不会以多线程**的方式执行。

### 线程6状态

NEW: 初始状态，线程被创建出来但没有被调用 `start()` 。
RUNNABLE: 运行状态（分为running和ready），线程被调用了 `start()`等待运行的状态。
BLOCKED ：synchronize阻塞状态，需要等待锁释放。进入 `synchronized` 方法/块或者调用 `wait` 后（被 `notify`）重新进入 `synchronized` 方法/块，但是锁被其它线程占有
WAITING：wait和sleep**挂起等待**状态，表示该线程需要等待其他线程做出一些特定动作（通知或中断）。**wait必须在synchronized内部调用**。
TIME_WAITING：超时等待状态，可以在**指定的时间后自行返回**而不是像 WAITING 那样一直等待。
TERMINATED：终止状态，run()方法后进入。

wait和blocked：等待队列（等待时间），Object.wait,Thread.join,LockSupport.park()进入，等待notify/notifyAll去获取CPU时间片重新运行。
blocked（等待资源）：同步队列，wait的进程被notify之后无法进入同步方法，没有获取到与同步方法相关的锁。
waiting -> blocked才有可能再运行。

6.流程：调用线程的start方法后线程进入就绪状态，线程调度系统将就绪状态的线程转为运行状态，
遇到wait和sleep方法后，释放资源等待notify——waiting（缺时间片，别人执行完之后会唤醒）
notify或者notifyAll的线程，遇到synchronized语句时，需要等待锁释放——blocked（缺资源）
获得锁之后才能重新回到runnable，当线程关联的代码执行完后，线程变为结束状态。

7.上下文切换：
主动**让出 CPU**，比如调用了 `sleep()`, `wait()` 等。
**时间片用完**，因为操作系统要防止一个线程或者进程长时间占用 CPU 导致其他线程或者进程饿死。
调用了阻塞类型的系统中断，比如**请求 IO**，线程被阻塞。
被终止或结束运行

### 进程5状态：

类似于线程，没有timewaiting和blocked

new 进程正在被创建，尚未到就绪状态
ready进程获得了除了处理器之外的一切所需资源
running
waiting等待状态，进程正在等待某一事件而暂停运行如等待某资源为可用或等待 IO 操作完成。即使处理器空闲，该进程也不能运行。
terminated

![进程状态图转换图](https://oss.javaguide.cn/github/javaguide/cs-basics/operating-system/state-transition-of-process.png)

### 死锁

多个线程同一资源上相互占用，并请求锁定对方资源。
四条件：互斥、请求保持、不剥夺、循环等待

如何预防：
一次性申请所有资源（2）；占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源（3）；靠**按序申请**资源来预防（4）

如何避免：银行家算法避免死锁，对资源分配进行计算评估，使其进入安全状态。
根据available[i] [j]，选一个need[i,j] <=available[i] [j]，分配给该进程，执行完之后available[i] [j]+=allocated[i] [j]。然后尝试分配给另一个进程，找到一个安全序列。

如何检测：**进程资源分配图**
定时地运行一个 “死锁检测” ，判断是否出现死锁。有环路未必死锁，因为循环等待时必要条件不是充分条件。
1.如果进程-资源分配图中无环路，则此时系统没有发生死锁
2.如果进程-资源分配图中有环路，且每个资源类仅有一个资源，则系统中已经发生了死锁。
3.如果进程-资源分配图中有环路，且涉及到的资源类有多个资源，此时系统未必会发生死锁。如果能在进程-资源分配图中找出一个 **既不阻塞又非独立的进程** ，该进程能够在有限的时间内归还占有的资源，也就是把边给消除掉了，重复此过程，直到能在有限的时间内 **消除所有的边** ，则不会发生死锁，否则会发生死锁。(消除边的过程类似于 **拓扑排序**)

如何解除：
当死锁检测程序检测到存在死锁发生时，应设法让其解除，让系统从死锁状态中恢复过来，常用的解除死锁的方法有以下四种：
1.**结束所有进程**，重新启动操作系统 ：这种方法简单，但以前所在的工作全部作废，损失很大。
2.**结束涉及死锁的所有进程**，解除死锁后继续运行 ：这种方法能彻底打破**死锁的循环等待**条件，但将付出很大代价，例如有些进程可能已经计算了很长时间，由于被撤销而使产生的部分结果也被消除了，再重新执行时还要再次进行计算。
3.**逐个撤销涉及死锁的进程**，回收其资源直至死锁解除。
4.**抢占资源** ：从涉及死锁的一个或几个进程中抢占资源，把夺得的资源再分配给涉及死锁的进程直至死锁解除。

### sleep和wait对比

sleep()  wait() notify() notifyAll()

wait必须在synchronized内部调用

同步的实现方面有两种，分别是synchronized,wait与notify。

* sleep: 不是Object中的方法，而是Thread类的**静态方法**，让当前线程阻塞，**用于暂停运行**，但是sleep时线程锁住了，**没有释放线程锁**，别人无法进入当前同步的方法。捕捉InterruptedException异常【去忙别的，我这里有锁，进不来了】

- wait: Object的方法，让当前线程阻塞，**用于线程通信**，wait时**当前锁释放**，别的线程可以进入当前方法。阻塞直到被notify或notifyAll唤醒，或者超时，或者线程被中断(InterruptedException)【去忙别的也可以进入我这里】
- notify: 任意选择一个正在这个对象上wait的线程把它唤醒（JVM确定唤醒哪个线程，而且不是按优先级），但是没有释放锁。唤醒之后的线程不是立即执行的，需要执行完notify方法后面的代码才会释放锁，才能轮到唤醒了的线程【随机选一个正在wait的，让他加入等待执行队列】
- notifyAll: 唤醒所有线程，让它们去竞争，不过也只有一个能抢到锁【所有的wait都唤醒，都去等待执行】

```java
public class MultiThread {
    public static void main(String[] args) {
new Thread(new Thread1()).start();
try {
    Thread.sleep(10);
} catch (InterruptedException e) {
    e.printStackTrace();
}
new Thread(new Thread2()).start();
    }

    private static class Thread1 implements Runnable {
@Override
public void run() {
    //由于这里的Thread1和下面的Thread2内部run方法要用同一对象作为监视器，我们这里不能用this，因为在Thread2里面的this和这个Thread1的this不是同一个对象。我们用MultiThread.class这个字节码对象，当前虚拟机里引用这个变量时，指向的都是同一个对象。
    synchronized (MultiThread.class) {
System.out.println("enter thread1...");
System.out.println("thread1 is waiting");
try {
    //释放锁有两种方式，第一种方式是程序自然离开监视器的范围，也就是离开了synchronized关键字管辖的代码范围，另一种方式就是在synchronized关键字管辖的代码内部调用监视器对象的wait方法。这里，使用wait方法释放锁。
    MultiThread.class.wait();
} catch (InterruptedException e) {
    e.printStackTrace();
}
System.out.println("thread1 is going on...");
System.out.println("thread1 is being over!");
    }
}
    }

    private static class Thread2 implements Runnable {
@Override
public void run() {
    synchronized (MultiThread.class) {
System.out.println("enter thread2...");
System.out.println("thread2 notify other thread can release wait status..");
//由于notify方法并不释放锁， 即使thread2调用下面的sleep方法休息了10毫秒，但thread1仍然不会执行，因为thread2没有释放锁，所以Thread1无法得不到锁。
MultiThread.class.notify();
System.out.println("thread2 is sleeping ten millisecond...");
try {
    Thread.sleep(10);
} catch (InterruptedException e) {
    e.printStackTrace();
}
System.out.println("thread2 is going on...");
System.out.println("thread2 is being over!");
    }
}
    }
}
```

## java终止线程

多线程有两种实现方法，分别是继承Thread类与实现Runnable接口。

```java
MyThread t = new MyThread(); // class MyThread extends Thread
Thread tt = new Thread(new MyRunnable2()); // class MyRunnable2 implements Runnable
```

有两种实现方法，分别是继承Thread类与实现Runnable接口。

终止线程：
1.设置退出标志，run()之后停止；
2.使用interupt()中断线程；
3.反对使用stop()，是因为它不安全。它会**解除由线程获取的所有锁定**，不保证资源正确释放，工作状态不确定，难以排查。
4.**suspend()方法容易发生死锁**。调用suspend()的时候，目标线程会停下来，但不释放资源。

## volatile synchronized reentrantLock

### volatile synchronized

volatile：**只能修饰变量，**让JVM每次都到主存中读取，禁用缓存。**保证变量可见性，不保证原子性**。主要用于解决变量在多个线程之间的可见性。
synchronized：可修饰方法和代码块，保证可见性&&原子性。解决多个线程访问资源的同步性。

volatile**禁止指令重排序**，像Unsafe的内存屏障，双重校验锁实现**双重检验锁对象单例**（线程安全） ，构造器私有化。
synchronized不能禁止重排序。
volatile无法实现原子性，例如public volatile static int inc = 0，多线程执行inc++方法时，不能保证原子性（5个线程各加500次结果<2500）。要在inc++的方法上加上synchronized。

```java
public class Singleton {
    // 单例模式，双重校验锁
    public int num;
    public volatile static Singleton unique;
    private Singleton(int a) {
        num = a;
    }
    public static Singleton get(int n) {
        //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (unique == null) {
synchronized (Singleton.class) {
    if (unique == null) {
        unique = new Singleton(n);
    }
}
        }
        return unique;
    }
}
```

synchronized关键字

可修饰**实例/静态方法、代码块**。**不能修饰构造方法，**因为构造方法本来就是线程安全的

底层实现：在JVM字节码中，对对象监视器 monitor 的获取。
代码块中：使用的是 **monitorenter 和 monitorexit** 指令，指向同步代码块的开始位置和结束位置。在执行`monitorenter`时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取后锁计数器=1，否则获取锁失败，需要阻塞等待。
方法中：使用**ACC_SYNCHRONIZED**标识，表明是否是同步方法。实例方法会获取实例对象的锁；静态方法会获取类的锁。

### CAS ABA

CAS 操作包含三个操作数——内存位置、预期原值及新值。内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，说明有线程改过了，处理器不做任何操作。是一条 CPU 的**原子指令**，不会造成所谓的数据不一致问题。ABA问题用版本号或时间戳解决。

如果一个变量 V 初次读取的时候是 A 值，并且在准备赋值的时候检查到它仍然是 A 值，那我们就能说明它的值没有被其他线程修改过了吗？很明显是不能的，因为在这段时间它的值可能被改为其他值，然后又改回 A，那 CAS 操作就会误认为它从来没有被修改过。这个问题被称为 CAS 操作的 "ABA"问题。

### ReentrantLock synchronized

两个是悲观锁、独占锁、互斥锁、==可重入锁==、默认非公平锁。`ReentrantLock` 更灵活、更强大，增加了轮询、超时、中断、公平锁和非公平锁等高级功能。

synchronized 依赖于 JVM ；ReentrantLock 依赖于 API，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成
**synchronized是不可中断锁，ReentrantLock 是可中断锁**

ReetrantLock的高级功能：
等待可中断：lock.lockInterruptibly()可以让进程放弃等待，去做别的
可实现公平锁：`synchronized`只能是非公平锁；Lock lock = new ReentrantLock(true);
可实现选择性通知（锁可以绑定多个条件）：`synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制。`ReentrantLock`类当然也可以实现，但是需要借助于**Condition**接口与`newCondition()`方法。

### ReentrantReadWriteLock stampedLock

`ReentrantReadWriteLock` 实现了 `ReadWriteLock` ，是一个**可重入的读写锁**，既可以保证多个线程同时读的效率，同时又可以保证有写入操作时的线程安全。其实是两把锁，一把是 `WriteLock` (写锁)，一把是 `ReadLock`（读锁） 。

`StampedLock` 是 JDK 1.8 引入的**性能更好的读写锁**，**不可重入且不支持条件变量** `Conditon`。提供了三种模式的读写控制模式：读锁、写锁和乐观读。

写锁：独占锁，类似于 `ReentrantReadWriteLock` 的写锁，不过不可重入。
读锁 （悲观读）：共享锁，类似于 `ReentrantReadWriteLock` 的读锁，不过不可重入。
乐观读 ：允许多个线程获取乐观读以及读锁。同时允许一个写线程获取写锁。

均适合**读多写少**的场景，但stampedLock效率更高（乐观读）

## AQS

ReentrantLock 和ReentrantReadWriteLock 的底层实现机制都是AQS。AbstractQueuedSynchronizer抽象类，即抽象队列同步器

### AQS CLH

如果被请求的共享资源空闲，将资源分配给该线程，并且将共享资源锁定。如果被请求的共享资源被占用，那么就用线程阻塞等待机制——**CLH**锁，是一个虚拟的双向队列（不存在队列实例，仅存在结点关系），一个节点表示一个线程，它保存着线程的引用（thread）、 当前节点在队列中的状态（waitStatus）、前驱节点（prev）、后继节点（next）。

以 `ReentrantLock` 为例，`state` 初始值为 0，表示未锁定状态。A 线程 `lock()` 时，会调用 `tryAcquire()` 独占该锁并将 `state+1` 。此后，其他线程再 `tryAcquire()` 时就会失败，直到 A 线程 `unlock()` 到 `state=`0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A 线程自己是可以重复获取此锁的（`state` 会累加），这就是可重入的概念。但要注意，**获取多少次就要释放多少次**，这样才能保证 state 是能回到零态的。

再以 `CountDownLatch` 以例，任务分为 N 个子线程去执行，`state` 也初始化为 N（注意 N 要与线程个数一致）。这 N 个子线程是并行执行的，每个子线程执行完后`countDown()` 一次，state 会 CAS(Compare and Swap) 减 1。等到所有子线程都执行完后(即 `state=0` )，会 `unpark()` 主调用线程，然后主调用线程就会从 `await()` 函数返回，继续后余动作。



![img](https://oss.javaguide.cn/github/javaguide/CLH.png)

### 资源共享方式

| 资源共享方式          | 定义                                                         | 举例                        |
| --------------------- | ------------------------------------------------------------ | --------------------------- |
| 独占，Exclusive       | 只有一个线程能执行                                           | synchronized  ReentrantLock |
| 共享，Share           | 多个线程可同时执行                                           | Semaphore CountDownLatch    |
| Share——Semaphore      | 信号量，共享资源数据为N，支持N个线程共享，>N的线程阻塞<br />semaphore.acquire()和release() |                             |
| Share——CountDownLatch | 倒计数器，允许count个线程阻塞在一个地方，等全部执行完。实现**线程间彼此等待**，基于AQS<br />是一次性的，**只初始化一次**，之后countDown()不断-1，直到值为0，执行await()后面的语句<br />countDown()和await() |                             |
| Share——CyclicBarrier  | 循环栅栏，类似于CountDownLatch，但基于ReentrantLock<br /> `await()` 方法就像树立起一个栅栏的行为一样，每拦截parties个线程，开启一次栅栏，线程才得以通过执行。 |                             |
| 自定义，独占+共享     |                                                              | ReentrantReadWriteLock      |



## 20把锁

主要根据应用来看：synchronized 

| java锁                | 定义                                                         | 应用                                                         |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1.悲观锁              | 认为每次访问都修改，所以获取资源都上锁，共享资源只给一个线程使用，其它线程阻塞 | synchronized ReentrantLock<br />**HashTable**                |
| 2.乐观锁              | 认为每次访问都不修改，只在提交修改的时候去验证同一性（版本号或CAS） | java.util.concurrent.atomic下面的原子变量类（比如`AtomicInteger`、`LongAdder`） |
| 对比                  | 悲观锁适合写多场景，可以避免乐观锁的频繁失败和重试，但如果诸如LongAdder解决了该问题，也可以使用乐观锁处理写。<br />乐观锁适合读多场景，避免频繁加锁影响性能 |                                                              |
| 3.独占锁/读锁         | 读/写进程独占，只允许一个进程访问，阻塞其他读写进程          | `synchronized`和`java.util.concurrent(JUC)`包中Lock的实现类  |
| 4.共享锁/写锁         | **只有读进程可以加共享锁**，加上之后其他进程可以加共享锁读，阻塞其他写进程，不阻塞读进程 |                                                              |
| 5.互斥锁              | 独占锁的一种常规实现                                         |                                                              |
| 6.读写锁              | 共享锁的实现                                                 |                                                              |
| 7.公平锁              | 多个线程按照申请锁的顺序来获取锁，先到先得                   | 创建一个可重入锁，true 表示公平锁，false 表示非公平锁。**默认非公平锁**<br />Lock lock = new ReentrantLock(true); |
| 8.非公平锁            | 可以插队，高并发环境下，有可能造成优先级翻转，或者饥饿的状态 | synchronized 关键字是非公平锁，ReentrantLock默认也是非公平锁。 |
| **9.可重入锁/递归锁** | 同一个线程在外层方法获取了锁，在进入内层方法会自动获取锁。（对于synchronized嵌套的情况）一定程度**避免死锁** | synchronized ReentrantLock                                   |
| 10.不可重入锁         | 会出现死锁                                                   |                                                              |
| 11.自旋锁             | 因为线程挂起唤醒耗费资源，因此在**没有获得锁时不阻塞**，而是执行一个忙循环，询问是否获得锁。 | CAS 操作如果失败就会一直循环获取当前 value 值然后重试。      |
| 12.分段锁             | 锁粒度细化，只对数组某一段加锁。                             | CurrentHashMap 底层用过Segment                               |
| 锁升级                | 有以下四种，随着竞争，偏向锁->轻量级锁->重量级锁             |                                                              |
| 13.无锁               | 乐观锁                                                       |                                                              |
| 14.偏向锁             | 偏向于第一个访问锁的线程，在只有一个线程访问资源时，给他一个偏向锁，表示不需要重复获取锁 |                                                              |
| 15.轻量级锁           | 轻量级锁认为虽然竞争是存在的，但程度很低，通过**自旋**方式等待上一个线程释放锁。 |                                                              |
| 16.重量级锁           | 线程并发进一步加剧，线程的自旋超过了一定次数，**重量级锁就是互斥锁** |                                                              |
| 17.锁粗化             | 多次上锁、解锁的请求合并为一次同步请求                       |                                                              |
| 18.锁消除             | 检测到了共享数据没有竞争的锁，从而将这些锁进行消除           |                                                              |
| 19.可中断锁           | 获取锁的过程中可以被中断，不需要一直等到获取锁之后 才能进行其他逻辑处理 | ReentrantLock                                                |
| 20.不可中断锁         | 一旦线程申请了锁，就只能等到拿到锁以后才能进行其他的逻辑处理 | synchronized                                                 |

![image-20230416194535681](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230416194535681.png)

## ThreadLocal

让每个线程绑定自己的值，有自己专属的本地变量。让每个线程都会有这个变量的本地副本。

***ThreadLocal是在堆中***；线程的栈中，维护着一个threadLocalMap变量，保存着变量副本

```java
 private static final ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyyMMdd HHmm"));
 formatter.set(new SimpleDateFormat()); // get set方法获取ThreadLocal值或赋值
System.out.println("New ThreadName=" + Thread.currentThread().getName() + ";default formatter=" + formatter.get().toPattern());

```

最终的变量是放在了当前线程的 `ThreadLocalMap` 中，并不是存在 `ThreadLocal` 上，`ThreadLocal` 可以理解为只是`ThreadLocalMap`的封装，传递了变量值。`ThrealLocal` 类中可以通过`Thread.currentThread()`获取到当前线程对象后，直接通过`getMap(Thread t)`可以访问到该线程的`ThreadLocalMap`对象。
每个Thread中都具备一个ThreadLocalMap，而ThreadLocalMap可以存储以ThreadLocal为 key ，Object 对象为 value 的键值对。

### key弱引用

ThreadLocal内存泄漏：`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用，而 value 是强引用。
如果 `ThreadLocal` 没有被外部强引用，在GC时，key 会被清理掉，Map 中就会出现 key 为 null 的 Entry。假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。
在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。

### Hash冲突

每当创建一个`ThreadLocal`对象，这个`ThreadLocal.nextHashCode` 这个值就会增长 `0x61c88647` 。这个值是**斐波那契数** 也叫 **黄金分割数**。`hash`增量为 这个数字，带来的好处就是 `hash` **分布非常均匀**。

冲突解决：`Entry` 不为 `null` 且 `key` 值相等时，冲突。**ThreadLocalMap没有链表，所以用开放定址法**（向后探测）。
如果key为null即key过期了，则进行一次探测式清理。

hash得到的Entry数据为空：直接放入；
									不为空，，两个key相等：更新；
									不为空，向后遍历，先找到一个Entry为null：放入；
									不为空，向后遍历，先找到一个过期的key，key== null，探测式清理或启发式清理。

### 扩容机制

set之后，如果Entry数量达到列表的2/3，即size>=threshold=len*2/3，执行rehash()
先探测式清理如果清理之后Entry数量size >= threshold * 3/4，进行扩容到2*len

## 线程池

线程池、数据库连接池、Http 连接池等等都是对这个思想的应用。池化技术的思想主要是为了减少每次**获取资源的消耗**（通过重复利用已创建的线程降低线程创建和销毁造成的消耗）；**提高响应速度**（当任务到达时，任务可以不需要等到线程创建就能立即执行）；**提高线程的可管理性**（线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控）。

### 创建线程池

**推荐使用ThreadPoolExecutor，不推荐用Executor 框架的工具类Executors**，因为可能会OOM。
线程池最重要的三个参数：corePoolSize，maximumPoolSize，workQueue

```
public ThreadPoolExecutor(
int corePoolSize,//任务队列未达到队列容量时，线程池最大可以同时运行的核心线程数量
int maximumPoolSize,//任务队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数=核心数+非核心数。
long keepAliveTime,//当线程数大于**核心线程数**时，多余的空闲线程存活的最长时间，不是立刻销毁的
TimeUnit unit,//时间单位
BlockingQueue<Runnable> workQueue,//新任务来的时候会先判断当前运行的线程数量是否达到**核心线程数**，如果达到的话，新任务就会被存放在队列中等待
ThreadFactory threadFactory,//线程工厂，用来创建线程，一般默认即可
RejectedExecutionHandler handler//饱和策略/拒绝策略，当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时，我们可以定制策略来处理任务。
 )
 
```

饱和策略：默认AbortPolicy（拒绝处理新任务）；也可以CallerRunsPolicy，不抛弃任务，不抛出异常，将任务退回给调用者，让调用者的线程去执行

下表这些线程池最好别用，都容易OOM，用自定义的ThreadPoolExecutor，还可以自己命名

|                                        | 线程池                        | 定义                                                         | 容量                                                         |
| -------------------------------------- | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 可重用固定线程数的线程池               | FixedThreadPool               | `corePoolSize` 和 `maximumPoolSize` 都被设置为 `nThreads`    | 任务队列使用的是无界的 `LinkedBlockingQueue`，永远放不满。maximumPoolSize是无效参数。<br />keepAliveTime 是无效参数。<br />任务过多时 OOM |
| 只有一个线程的线程池                   | SingleThreadExector           | `corePoolSize` 和 `maximumPoolSize` 都被设置为 1             | 同上无界队列，**任务队列过长会OOM**                          |
| 会根据需要创建新线程的线程池           | CachedThreadPool              | `corePoolSize` 被设置为空（0），`maximumPoolSize`被设置为 `Integer.MAX.VALUE | 同步队列<br />没有容量，不存储元素，目的是保证对于提交的任务，如果有空闲线程，则使用空闲线程来处理；否则新建一个线程来处理任务。**线程创建过多OOM** |
| 在给定的延迟后运行任务或者定期执行任务 | ScheduledThreadPool           | 按照延迟的时间长短对任务进行排序，内部采用的是“堆”，保证**等待最久的先执行** | 延时队列，可能堆积大量的请求，从而导致 OOM。                 |
|                                        | SingleThreadScheduledExecutor |                                                              |                                                              |

![线程池各个参数的关系](https://javaguide.cn/assets/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%90%84%E4%B8%AA%E5%8F%82%E6%95%B0%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%B3%BB.d65f3309.png)



![图解线程池实现原理](https://oss.javaguide.cn/javaguide/%E5%9B%BE%E8%A7%A3%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86.png)

如果当前运行的线程数小于核心线程数，那么就会新建一个线程来执行任务。
如果当前运行的线程数等于或大于核心线程数，但是小于最大线程数，那么就把该任务放入到任务队列里等待执行。
如果向任务队列投放任务失败（任务队列已经满了），但是当前运行的线程数是小于最大线程数的，就新建一个线程来执行任务。
如果当前运行的线程数已经等同于最大线程数了，新建线程将会使当前运行的线程超出最大线程数，那么当前任务会被拒绝，饱和策略会调用`RejectedExecutionHandler.rejectedExecution()`方法。

### 线程池实践

1.线程池必须手动通过 ThreadPoolExecutor 的构造函数来声明，避免使用Executors 类创建线程池，会有 OOM 风险。

2.线程池命名：ThreadFactoryBuilder.setNameFormat()，自定义ThreadFactor

3.线程池大小：
**CPU 密集型任务(N+1)：** 将线程数设置为 N（CPU 核心数）+1。比 CPU 核心数多出来的一个线程是为了防止线程偶发的缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，CPU 就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间。
**I/O 密集型任务(2N)：**系统会用大部分的时间来处理 I/O 交互，CPU空闲时候多，可以多配置一些线程，具体的计算方法是 2N。

4.建议**不同类别的业务用不同的线程池**，否则如果业务之间又相互关系，容易死锁。

### Future接口 Callable Runnable

有一个任务，提交给了 `Future` 来处理。任务执行期间我自己可以去做别的，并且可以进行4个功能：随时取消任务cancel()、判断任务是否已经执行完成isDone()、判断任务是否被取消isCancel()、获取任务执行结果get()。一段时间之后，我就可以 `Future` 那里直接取出**==异步运算结果==**。

Future实现类：
1.FutureTask不光实现了 `Future`接口，还实现了`Runnable` 接口，因此可以作为任务直接被线程执行。`FutureTask` 有两个构造函数，可传入 `Callable` 或者 `Runnable` 对象。实际上，传入 `Runnable` 对象也会在方法内部转换为`Callable` 对象。
`FutureTask`相当于对`Callable` 进行了封装，管理着任务执行的情况，存储了 `Callable` 的 `call` 方法的任务执行结果。

2.**CompletableFuture**：实现了Future和CompletionStage接口，提供了函数式编程、异步计算、流式调用。

3.Runnable(execute) VS Callable(submit)
返回值：Runnable没有返回值（void），Callable有（Object），能返回执行结果。
异常处理：Runnable必须内部try catch，Callable可以try catch也可以向上抛出工具类 
execute没有返回值，无法判断任务成功与否；**submit有返回值，返回一个feture对象**，可以进行cancel，get，isDone
`Executors` 可以实现将 `Runnable` 对象转换成 `Callable` 对象。（`Executors.callable(Runnable task)` 或 `Executors.callable(Runnable task, Object result)`）。

4.shutdown和shutdownNow
**shutdown**（） :关闭线程池，线程池的状态变为 `SHUTDOWN`。不再接受新任务，但会将**队列里的任务执行完毕**。
shutdownNow（）:关闭线程池，线程的状态变为 `STOP`。现在正在运行的和队列里的也终止。

5.isShutDown VS isTerminated
isShutDown ：调用shutdown方法后，即为true
**isTerminated**：调用shutdown方法并且队列任务全部执行完毕后，方为true

### Executor框架

Executor框架是由**任务**，**任务的执行**ThreadPoolExecutor，**异步计算的结果**Future组成。

![在这里插入图片描述](https://img-blog.csdnimg.cn/984a0b68fb664ad2aed99baaa013ace8.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBA5LiA5Liq5Yid57qn57uH5qKm6ICF,size_23,color_FFFFFF,t_70,g_se,x_16)

1、 主线程首先要创建实现Runnable或者Callable接口的任务对象。工具类Executors可以把一个Runnable对象封装为一个Callable对象（Executors.callable（Runnable task）或Executors.callable（Runnable task，Object resule））。
2、把Runnable对象直接交给ExecutorService执行（execute），或封装为Callable对象提交给ExecutorService执行（submit）。
3、如果执行ExecutorService.submit（…），ExecutorService将返回一个实现Future接口的对象FutureTask。
4、主线程可以执行FutureTask.get()方法来等待任务执行完成，或FutureTask.cancel来取消此任务的执行。

### 当一个线程进入一个对象的一个synchronized方法后，其它线程是否可进入此对象的其它方法?

分几种情况：

一个线程在访问一个对象的同步方法时，另一个线程可以同时访问这个对象的**非同步**方法。

一个线程在访问一个对象的同步方法时，另一个线程**不能同时访问该同步方法**和其他同步方法。

但是如果该同步方法里面有wait，那另一个线程**<font color='red'>可以</font>同时访问该同步方法**和其他同步方法。

如果线程1调用非静态的synchronized方法，线程2调用静态的synchronized方法，两者互不影响，没有同步，各跑各的

### 简述synchronized和java.util.concurrent.locks.Lock的异同 ？

主要相同点：Lock能完成synchronized所实现的所有功能。

主要不同点：Lock有比synchronized更精确的线程语义和更好的性能。**synchronized会自动释放锁**，而Lock一定要求程序员手工释放，并且**必须在finally从句中手动释放**。Lock还有更强大的功能，例如，它的**tryLock方法可以非阻塞方式去拿锁**。

## java并发容器:star:

JDK 提供的这些容器大部分在 `java.util.concurrent` 包中。

| JUC容器               | 定义                                                         | 性能                                                         |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ConcurrentHashMap     | 线程安全的HashMap，性能远高hashTable                         | 读操作时(几乎)不需要加锁，而在写操作时通过**锁分段技术**只对所操作的段加锁 |
| CopyOnWriteArrayList  | 线程安全的 List，性能远高Vector                              | 读操作没有任何锁，读不阻塞写（内部数组 `array` 不会发生修改，只会被另外一个 `array` 替换），<br />写不阻塞读。修改时时是修改副本，而非原内存。<br />写阻塞写，避免多个副本。 |
| ConcurrentLinkedQueue | 线程安全的 LinkedList，**非阻塞队列**，==高并发环境下性能最好的队列== | 内部实现复杂，使用 CAS 非阻塞算法来实现线程安全              |
| BlockingQueue         | **阻塞**队列，适合用于作为数据共享的通道。                   | 加锁实现线程安全，被广泛使用在“生产者-消费者”问题中，提供了可阻塞的插入和移除的方法：**当队列满时，阻塞生产；队列空时，阻塞消费。** |
| ArrayBlockingQueue    | 是 `BlockingQueue` 接口的有界队列实现类，底层采用数组来实现  | 有界队列，创建后容量不可改变；<br />使用可重入锁reentrantLock实现并发，因此默认不公平锁 |
| LinkedBlockingQueue   | 底层基于**单向链表**实现                                     | 可以有界可以无界，如果指定capacity就是有界                   |
| PriorityBlockingQueue | 线程安全的PriorityQueue，支持优先级的无界阻塞队列            | 自动扩容，无界队列，所以只阻塞消费（空），不会阻塞生产（不会满）<br />使用可重入锁reentrantLock实现并发 |
| ConcurrentSkipListMap | 跳表，是个Map，快速查找                                      |                                                              |

类似于平衡树，但平衡树的修改可能会全局调整，需要全局锁；调表是局部调整，只需要部分锁。<br />本质是同时维护了多个链表，并且链表是**分层**的。
**可以实现二分查找的有序链表O(logn)**。查找时，可以从顶级链表开始找。一旦发现被查找的元素大于当前链表中的取值，就会转入下一层链表继续找。这也就是说在查找过程中，搜索是跳跃式的。

## Atomic原子类

java.util.concurrent.atomic下有：基本类型（AtomicInteger/Long/Boolean）;数组类型（AtomicIntegerArray/LongArray/Reference
Array）;引用类型（AtomicStampedReference可以解决ABA问题）；对象的属性修改类型原子类（AtomicIntegerFieldUpdater）

1. 基本类型原子类AtomicInteger

 利用CAS (compare and swap) + volatile 和 native 方法，保证原子操作

2. 数组类型原子类AtomicIntegerArray

还是7种方法，只不过每一种都加了一个参数index

```java
public final int get() //获取当前的值
public final int getAndSet(int newValue)//获取当前的值，并设置新的值
public final int getAndIncrement()//获取当前的值，并自增
public final int getAndDecrement() //获取当前的值，并自减
public final int getAndAdd(int delta) //获取当前的值，并加上预期的值
boolean compareAndSet(int expect, int update) //如果输入的数值等于预期值，则以原子方式将该值设置为输入值（update）
public final void lazySet(int newValue)//最终设置为newValue,使用 lazySet 设置之后可能导致其他线程在之后的一小段时间内还是可以读到旧的值。

/**
 * volatile不能保证原子性，count++不是原子操作，需要加上synchronized；
 * 但是AtomicInteger不需要synchronized关键字，调用incrementAndGet，getAndAdd，getAndSet方法就可以原子操作
 * */
public class atomicIntegerTT {
    private static AtomicInteger count2 = new AtomicInteger();
    private static volatile int count = 0;
    private  void inc() {count ++;}
    private void incAto() {count2.incrementAndGet();}
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        atomicIntegerTT tt = new atomicIntegerTT();
        for (int i = 0; i <5 ; i++) {
executorService.execute(() -> {
    for (int j = 0; j < 500; j++)
    {tt.inc();tt.incAto();}
});}
        try {Thread.sleep(1500);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println(count);
        System.out.println(count2.get());
        executorService.shutdown();
    }
}

```

3. 引用类型原子类

数组类型原子类只能更新一个变量，但AtomicReference一次可以更新一个引用对象。set,compareAndSet

4. 对象的属性修改类型原子类

更新某个类里的某个字段的值，需要指定修改的字段

```java
AtomicReference < Person > ar = new AtomicReference < Person > ();
Person person = new Person("SnailClimb", 22);
ar.set(person);
Person updatePerson = new Person("Daisy", 20);
ar.compareAndSet(person, updatePerson);

AtomicIntegerFieldUpdater<User> updater = AtomicIntegerFieldUpdater.newUpdater(User.class, "age");
User user = new User("java", 22);
System.out.println(updater.getAndAdd(user,5));
System.out.println(updater.get(user));
```

## 线程设计题

1. 设计4个线程，其中两个线程每次对j增加1，另外两个线程对j每次减少1。写出程序。

以下程序使用内部类实现线程，对j增减的时候没有考虑顺序问题。

```java
public class ThreadTest1
{
private int j;
public static void main(String[] args){
    ThreadTest1 tt=new ThreadTest1();
    Inc inc=tt.new Inc();
    Dec dec=tt.new Dec();
    for(int i=0;i<2;i++){
Thread t=new Thread(inc);
t.start();
t=new Thread(dec);
t.start();
}
    }
private synchronized void inc(){
    j++;
    System.out.println(Thread.currentThread().getName()+"-inc:"+j);
    }
private synchronized void dec(){
    j--;
    System.out.println(Thread.currentThread().getName()+"-dec:"+j);
    }
class Inc implements Runnable{
    public void run(){
for(int i=0;i<100;i++){
inc();
}
    }
}
class Dec implements Runnable{
    public void run(){
for(int i=0;i<100;i++){
dec();
}
    }
}
}
```

----------随手再写的一个-------------

```java
class A
{
JManger j =new JManager();
main()
{
	new A().call();
}
void call()
{
	for(int i=0;i<2;i++)
	{
		new Thread(
			new Runnable(){ public void run(){while(true){j.accumulate();}}}
		).start();
		new Thread(new Runnable(){ public void run(){while(true){j.sub();}}}).start();
	}
}
}
class JManager
{
	private int j = 0;
	
	public synchronized void subtract()
	{
		j--;
	}
	
	public synchronized void accumulate()
	{
		j++;
	}
	
}
```



2. 子线程循环10次，接着主线程循环100，接着又回到子线程循环10次，接着再回到主线程又循环100，如此循环50次，请写出程序。

最终的程序代码如下：

```java
/**
 * 子线程循环10次，接着主线程循环5，接着又回到子线程循环10次，接着再回到主线程又循环5，如此循环50次，请写出程序。
 */
public class ThreadTest2 {
    public static void main(String[] args) {
        new ThreadTest2().init();
    }

    public void init() {
        final Business business = new Business();
        new Thread(new Runnable() {
public void run() {
    for (int i = 0; i < 50; i++) {
        business.SubThread(i);
    }
}
        }
        ).start();
        for (int i = 0; i < 50; i++) {
business.MainThread(i);
        }
    }

    private class Business {
        boolean bShouldSub = true;//这里相当于定义了控制该谁执行的一个信号灯
        // 主线程在false时执行
        public synchronized void MainThread(int i) {
if (bShouldSub) // 这时候Main等待，cpu在执行子线程
    try {
        System.out.println("Main主线程等待，此时执行Sub线程");
        this.wait();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
//          主线程执行5次
for (int j = 0; j < 5; j++) {
    System.out.println(Thread.currentThread().getName() + ":i=" + i + ",j=" + j);
}
bShouldSub = true;
this.notify();

        }

        public synchronized void SubThread(int i) {
if (!bShouldSub)
    try {
        System.out.println("sub子线程等待，此时执行Main线程");
        this.wait();
    } catch (InterruptedException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
//子线程在true时执行10次
for (int j = 0; j < 10; j++) {
    System.out.println(Thread.currentThread().getName() + ":i=" + i + ",j=" + j);
}
bShouldSub = false;
this.notify();
        }
    }
}

```

备注：不可能一上来就写出上面的完整代码，最初写出来的代码如下，问题在于两个线程的代码要参照同一个变量，即这两个线程的代码要共享数据，所以，把这两个线程的执行代码搬到同一个类中去：

```java
package com.huawei.interview.lym;
public class ThreadTest {
	private static boolean bShouldMain = false;
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		/*new Thread(){
		public void run()
		{
			for(int i=0;i<50;i++)
			{
				for(int j=0;j<10;j++)
				{
					System.out.println("i=" + i + ",j=" + j);
				}
			}				
		}
		
	}.start();*/		
				
		//final String str = new String("");
		new Thread(
				new Runnable()
				{
					public void run()
					{
						for(int i=0;i<50;i++)
						{
							synchronized (ThreadTest.class) {
								if(bShouldMain)
								{
									try {
										ThreadTest.class.wait();}
									catch (InterruptedException e) {
										e.printStackTrace();
									}
								}
								for(int j=0;j<10;j++)
								{
									System.out.println(
											Thread.currentThread().getName() +
											"i=" + i + ",j=" + j);
								}
								bShouldMain = true;
								ThreadTest.class.notify();
							}							
						}						
					}
				}
		).start();
		
		for(int i=0;i<50;i++)
		{
			synchronized (ThreadTest.class) {
				if(!bShouldMain)
				{
					try {
						ThreadTest.class.wait();}
					catch (InterruptedException e) {
						e.printStackTrace();
					}
				}				
				for(int j=0;j<5;j++)
				{
					System.out.println(
							Thread.currentThread().getName() + 						
							"i=" + i + ",j=" + j);
				}
				bShouldMain = false;
				ThreadTest.class.notify();				
			}			
		}
	}
}
```

## Java内存模型JMM

JVM 内存结构与JVM有关，Java 内存模型和**并发编程**相关，比如说线程之间的共享变量必须存储在主内存中，规定了从 Java 源代码到 CPU 可执行指令的这个转化过程要遵守哪些和并发相关的原则和规范，其主要目的是为了简化多线程编程，增强程序可移植性。

### CPU缓存模型 & 指令重排序

解决CPU与内存速度不一致问题:arrow_right:增加Cache:arrow_right:引发内存缓存数据不一致问题:arrow_right:制定缓存一致协议MESI

编译器优化重排（编译器重新排序指令顺序）:arrow_right: 指令并行重排（数据不依赖情况下，指令并行执行） :arrow_right:内存系统重排

指令重排序可以保证**串行语义一致**，但是没有义务保证多线程间的语义也一致 ，所以在多线程下，指令重排序可能会导致数据不一致:arrow_right:内存屏障

### happens-before

不是指前一个操作在后一个操作**前执行**，而是指前一个操作的结果对于后一个操作**是可见的**。为了平衡程序员与编译器，程序员希望易于理解的强内存模型，编译器处理器希望较少约束、性能优化的弱内存模型。

![img](https://oss.javaguide.cn/github/javaguide/java/concurrent/image-20220731155332375.png)

**程序顺序规则** ：一个线程内，按照代码顺序，书写在前面的操作 happens-before 于书写在后面的操作；
**解锁规则** ：解锁 happens-before 于加锁；
**volatile 变量规则** ：对一个 volatile 变量的写操作 happens-before 于后面对这个 volatile 变量的读操作。对 volatile 变量的写操作的结果对于其后的任何操作都可见。
**传递规则** ：如果 A happens-before B，且 B happens-before C，那么 A happens-before C；
**线程启动规则** ：Thread 对象的 `start()`方法 happens-before 于此线程的每一个动作。

### 并发编程特性:star:

1.原子性：一次操作或者多次操作，不可中断，必须一次性执行完。`synchronized` 、各种 `Lock` 以及各种原子类（CAS）实现原子性。

2.可见性：当一个线程对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值。`synchronized` 、各种 `Lock` 、`volatile` 

3.有序性：由于指令重排序问题，代码的执行顺序未必就是编写代码时候的顺序。在 Java 中，Unsafe**内存屏障、`volatile`** 可以禁止指令重排序。

# JVM

JVM包含字节码指令集、寄存器、堆、栈、GC，与硬件不直接关联。

运行过程：java源文件->编译器->字节码文件->JVM->机器码

## java内存区域/结构

jvm把内存分为4大块，分别是堆，方法区，程序计数器，栈。

栈是每个线程独有的；
程序计数器里存的是当前线程的字节码指令执行到的位置

方法区，方法区为线程共享的一块内存区域，如静态变量，常量，类的信息。
堆是jvm内存分配重要的一块，**堆中存放的主要是对象**。最主要的就是gc机制。

jdk1.8以后：

**1.线程私有区域**：程序计数器、虚拟机栈、本地方法栈（随着线程的创建而创建，随着线程的死亡而死亡）

* 程序计数器：

字节码解释器通过改变程序计数器来**依次读取指令**，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
在多线程的情况下，程序计数器用于**记录当前线程执行的位置**，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。
如果执行的是 native 方法，那么程序计数器记录的是 undefined 地址，**只有执行的是 Java 代码**时程序计数器记录的才是下一条指令的地址。

* 虚拟机栈（方法的栈帧）：

栈绝对算的上是 JVM 运行时数据区域的**核心**，除了一些 Native 方法调用是通过本地方法栈实现的，其他所有的 Java 方法调用都是通过栈来实现的（也需要和其他运行时数据区域比如程序计数器配合）
栈由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法返回地址。
Java 方法有两种返回方式，一种是 return 语句正常返回，一种是抛出异常。不管哪种返回方式，都会导致栈帧被弹出。也就是说，**栈帧随着方法调用而创建，随着方法结束而销毁。**无论方法正常完成还是异常完成都算作方法结束。
StackOverFlowError（不可动态扩展的栈报错）和OutOfMemoryError（可动态扩展的栈报错）

* 本地方法栈（native方法）

为虚拟机使用到的 **Native 方法**（非java代码的接口，与非java环境交互）服务。其他类似虚拟机栈

**2.线程共享区：**java堆（GC主要管理）、方法区、直接内存

* 堆（对象）

存放对象实例，所有的对象实例以及数组都在这里分配内存。容易出现OutOfMemoryError错误

* 方法区（类信息、常量、静态变量）

存放已被加载的**类信息、常量、静态变量**、即时编译器编译后的代码等数据。虚拟机不同则方法区的实现不同，**永久代**是 JDK 1.8 之前的方法区实现，JDK 1.8 及以后方法区的实现变成了**元空间**。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210528143337584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2MxNTE1ODAzMjMxOQ==,size_16,color_FFFFFF,t_70#pic_center,width='40%25')

## 内存泄漏

所谓内存泄露就是指**一个不再被程序使用的对象或变量一直占据在内存中**。

Java中的内存泄露的情况（**<font color='red'>无用，无法回收</font>**）：**长生命周期的对象持有短生命周期对象的引用**就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是Java中内存泄露的发生场景，通俗地说，就是程序员可能创建了一个对象，以后一直不再使用这个对象，这个对象却一直被引用，即这个对象无用但是却无法被垃圾回收器回收的，这就是Java中可能出现内存泄露的情况，例如，**缓存系统**，我们加载了一个对象放在缓存中(例如放在一个全局map对象中)，然后一直不再使用它，这个对象一直被缓存引用，但却不再被使用。

检查Java中的内存泄露，一定要**让程序将各种分支情况都完整执行到程序结束**，然后看某个对象是否被使用过，如果没有，则才能判定这个对象属于内存泄露。

* 如果一个外部类的实例对象的方法返回了一个内部类的实例对象，这个内部类对象被长期引用了，即使那个外部类实例对象不再被使用，但由于内部类持久外部类的实例对象，这个外部类对象将不会被垃圾回收，这也会造成内存泄露。

主要特点就是清空堆栈中的某个元素，并不是彻底把它从数组中拿掉，而是把存储的总数减少，在拿掉某个元素时，顺便也让它从数组中消失，将那个元素所在的位置的值设置为null即可：

例：

```java
public class Bad{
    public static Stack s=Stack();
    static{
s.push(new Object());
s.pop(); //这里有一个对象发生内存泄露
s.push(new Object()); //上面的对象可以被回收了，等于是自愈了
    }
}
```

* 因为是static，就一直存在到程序退出，但是我们也可以看到它有自愈功能，就是说如果你的Stack最多有100个对象，那么最多也就只有100个对象无法被回收其实这个应该很容易理解，Stack内部持有100个引用，最坏的情况就是他们都是无用的，因为我们一旦放新的进去，以前的引用自然消失！

* 当一个对象被存储进HashSet集合中以后，就不能修改这个对象中的那些参与计算哈希值的字段了，否则，对象修改后的哈希值与最初存储进HashSet集合中时的哈希值就不同了，在这种情况下，即使在contains方法使用该对象的当前引用作为的参数去HashSet集合中检索对象，也将返回找不到对象的结果，这也会导致无法从HashSet集合中单独删除当前对象，造成内存泄露。

## GC是什么? 为什么要有GC?

GC是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的**内存回收**会导致程序或系统的不稳定甚至崩溃，Java提供的GC功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的，Java语言没有提供释放已分配内存的显示操作方法。

74.垃圾回收的优点和原理。并考虑2种回收机制。

Java语言中一个显著的特点就是引入了垃圾回收机制，使c++程序员最头疼的内存管理的问题迎刃而解，它使得Java程序员在编写程序的时候**不再需要考虑内存管理**。由于有个垃圾回收机制，Java中的对象不再有"作用域"的概念，只有对象的引用才有"作用域"。垃圾回收可以有效的防止内存泄露，有效的使用可以使用的内存。垃圾回收器通常是作为**一个单独的低级别的线程**运行，不可预知的情况下对内存堆中已经死亡的或者长时间没有使用的对象进行清楚和回收，程序员不能实时的调用垃圾回收器对某个对象或所有对象进行垃圾回收。回收机制有分**代复制垃圾回收和标记垃圾回收，增量垃圾回收**。

75.垃圾回收器的基本原理是什么？垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收？

对于GC来说，当程序员创建对象时，GC就开始监控这个对象的地址、大小以及使用情况。通常，GC采用**有向图**的方式记录和管理堆(heap)中的所有对象。通过这种方式确定哪些对象是“可达的”，哪些对象是“不可达的”。**当GC确定一些对象为“不可达”不再被引用孤儿节点时，GC就有责任回收这些内存空间**。GC还可以**消除引用循环**问题，例如有两个对象，相互引用，只要它们和根进程不可达的，那么GC也是可以回收它们的

可以。程序员可以手动执行**System.gc()**，通知GC运行，但是Java语言规范并不保证GC一定会执行。

### GC分类

部分收集 (Partial GC) 并不收集整个GC堆的模式：

- 新生代收集（Minor GC / Young GC）：只对新生代young gen进行垃圾收集；
- 老年代收集（Major GC / Old GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集；
- 混合收集（Mixed GC）：收集整个young gen以及部分old gen的GC。只有**G1**有这个模式

整堆收集 (Full GC)：收集整个堆和方法区，包括young gen、old gen、perm gen（如果存在的话）等所有部分的模式。

**内存分配回收原则：**

进入full gc：直接调用System.gc，旧生代空间不足，Permanet Generation空间满

进入MinorGC:对象优先在 Eden 区分配，Eden不够，发起一次MinorGC。

进入MajorGC：

大对象(需要大量连续内存空间的对象)直接进入老年代 ：为了避免为大对象分配内存时由于分配担保机制带来的复制而降低效率
长期存活的对象**将进入老年代**：默认15岁

对象都会首先在 Eden 区域分配，如果Eden不够，触发MinorGC，同时将Eden存活对象转入S0 S1，年龄为1（S0 S1的对象没熬过一次MinorGC年龄都+1）。如果S0和S1也不够，先通过**空间分配担保机制**（确保OldGen有空间），如果有空间，就提前将youngGen的对象转移到OldGen；如果OldGen不够，那就触发Full FC。如果Minor GC后eden有空间，后续在Eden上分配。



![hotspot-heap-structure](https://javaguide.cn/assets/hotspot-heap-structure.41533631.png)

![image-20230403182522934](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230403182522934.png)



### 死亡对象判别方法：

引用计数法

给对象中添加一个引用计数器：

- 每当有一个地方引用它，计数器就加 1；
- 当引用失效，计数器就减 1；
- 任何时候计数器为 0 的对象就是不可能再被使用的。

可达性分析算法

 GC Roots 不可达的对象，是需要被回收的对象。

GC roots有：

- 虚拟机栈(栈帧中的本地变量表)中引用的对象
- 本地方法栈(Native 方法)中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 所有被同步锁持有的对象

### 引用类型介绍

无论是通过引用计数法判断对象引用数量，还是通过可达性分析法判断对象的引用链是否可达，判定对象的存活都与“引用”有关。

将引用分为强引用、软引用、弱引用、虚引用四种（引用强度逐渐减弱），在程序设计中一般很少使用弱引用与虚引用，使用软引用的情况较多，这是因为软引用可以加速 JVM 对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生

**1．强引用（StrongReference）**

new出来的。以前我们使用的大部分引用实际上都是强引用，这是使用**最普遍的引用**。如果一个对象具有强引用，那就类似于**必不可少的生活用品**，垃圾回收器**绝不会回收它**。当内存空间不足，Java 虚拟机宁愿抛出 OutOfMemoryError 错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。

**2．软引用（SoftReference）**

SoftReference 修饰的对象。如果一个对象只具有软引用，那就类似于**可有可无的生活用品**。如果内存空间足够，垃圾回收器就不会回收它，如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。**软引用可用来实现内存敏感的高速缓存**。

**3．弱引用（WeakReference）**

WeakReference 修饰的对象。如果一个对象只具有弱引用，那就类似于**可有可无的生活用品**。弱引用与软引用的区别在于：**只具有弱引用**的对象拥有**更短暂**的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程， 因此不一定会很快发现那些只具有弱引用的对象。

**4．虚引用（PhantomReference）**

"虚引用"顾名思义，就是**形同虚设**，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，**在任何时候都可能被垃圾回收**。虚引用主要用来**跟踪对象被垃圾回收的活动**。

**虚引用与软引用和弱引用的一个区别在于：** 虚引用必须和引用队列（ReferenceQueue）联合使用。软引用和弱引用都是可以和引用队列联用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。程序如果发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的行动。

### 判断废弃常量和无用类

**没有任何对象引用的常量**，就说明常量 "abc" 就是废弃常量，如果这时发生内存回收的话而且有必要的话，"abc" 就会被系统清理出常量池了。

该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。
加载该类的 `ClassLoader` 已经被回收。
该类对应的 `java.lang.Class` 对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

### 垃圾收集算法

标记清除算法（老生代）：首先标记出要回收的对象，标记完成，统一回收被标记的对象

标记—复制算法（适用于新生代，因为新生代有大量的对象死去）：内存空间分成两块空间，将存活的对象复制到另一块空间，假如说大量的对象被回收，只需要复制一小部分即可，所以适用于新生代。（速度快，效率高）

标记—整理算法（适合老年代，因为老生代存活几率比较高）：将存活的对象标记后，没有进行直接清除而是将存活的对象向一端整理到一起，这样就解决了空间碎片化问题，因为老年代的特点所以只有很少一部分被清除。

### 垃圾收集器：内存回收的具体体现

并行（用户等待）并发（gc和用户线程都运行）

young标记复制+old标记整理

1. Serial收集器：单线程，只会使用一条gc线程区收集垃圾，暂停其他所有的工作线程（ "Stop The World" ），直到它收集结束。Stop the world体验太差，但是简单高效，适合运行在 Client 模式下的虚拟机。

2. ParNew收集器：多线程并行的Serial，适合Server模式下的虚拟机，只有Serial和ParNew可以与CMS配合工作。

3. Parallel Scavenge 收集器（jdk1.8默认）：类似于ParNew，更关注吞吐量（高效利用CPU）

#### CMS收集器 标记清除

（HotSpot 虚拟机第一款真正意义上的并发收集器）：更关注用户体验（用户线程的停顿时间）

初始标记： STW，并记录下直接与 root 相连的对象，速度很快 
并发标记： 同时开启 GC 和用户线程，用一个闭包结构去记录**可达对象**。这个算法里会跟踪记录这些发生**引用更新**的地方。
重新标记：STW，对引用更新的地方重新标记，这个阶段的停顿时间一般会比初始标记阶段的时间稍长，远远比并发标记阶段时间短
并发清除： 开启用户线程，同时 GC 线程开始对**未标记**的区域做清扫。

从它的名字就可以看出它是一款优秀的垃圾收集器，主要优点：并发收集、低停顿。但是它有下面三个明显的缺点：
对 CPU 资源敏感；
无法处理浮动垃圾；
它使用的回收算法-“标记-清除”算法会导致收集结束时会有大**量空间碎片**产生

#### 三色标记法

5. 并发标记一共会有两个问题：错标（浮动垃圾），标记过不是垃圾的，变成了垃圾；错杀：认为是垃圾的，有了新的引用。

白色：没有检查（或者检查过了，确实没有引用指向它了）
灰色：自身被检查了，成员没被检查完（可以认为访问到了，但是正在被检查，就是图的遍历里那些在队列中的节点）
黑色：自身和成员都被检查完了

初始时，所有对象都在 【白色集合】中；
将GC Roots 直接引用到的对象 挪到 【灰色集合】中；
从灰色集合中获取对象： ①将本对象 引用到的 其他对象 全部挪到 【灰色集合】中；②将本对象 挪到 【黑色集合】里面。
直至【灰色集合】为空时结束。
结束后，仍在【白色集合】的对象即为GC Roots 不可达，可以进行回收。

错标：这个浮动垃圾的问题影响不是很大，可能就是暂时的浪费一点内存，它肯定抗不过下一轮GC。
错杀：一定要解决。写屏障+ 增量更新（CMS）写屏障 + SATB（G1）读屏障（ZGC）

#### G1收集器（标记复制+标记整理）

**面向服务器**的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. 以极高概率满足 GC 停顿时间要求的同时,还具备**高吞吐量**性能特征。

- 可以像CMS收集器一样可以和应用**并发**运行(多CPU**并行**减少STW时间)
- 压缩空闲的内存碎片，却不需要冗长的GC停顿（标记整理+标记复制）
- **可预测GC停顿**：可控的STW
- 不牺牲大量的吞吐量性能
- 不需要更大的Java Heap

* **分代收集**：虽然 G1 可以不需要其他收集器配合就能独立管理整个 GC 堆，但是还是保留了分代的概念。

G1 收集器的运作大致分为以下几个步骤：

初始标记；并发标记；最终标记；筛选回收
**G1中没有Full GC**，G1中的Full GC是采用serial old Full GC。==基于Region的堆内存布局==
通过把Java堆分成大**小相等的多个独立区域**，回收时计算出每个区域回收的经验值，**判断哪个区域最具有回收价值**，优先选择回收价值最大的 Region。

![image-20230403182539687](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230403182539687.png)

7. ZGC收集器（标记整理）

类似G1，也是基于region的堆内存布局，ZGC中没有引入分代，也就没有新生代和老年代的概念，以page为单位进行对象的分配回收。

## 类加载器

JVM中类的装载是**由ClassLoader和它的子类来实现的**，Java ClassLoader 是一个重要的Java运行时系统组件。它负责在**运行时查找和装入类文件**的类。

类加载过程：加载->连接->初始化。
连接过程又可分为三步：验证->准备->解析。

![类加载过程](https://oss.javaguide.cn/github/javaguide/java/jvm/class-loading-procedure.png)

”加载“过程：类加载器是一个负责加载类的对象，用于实现类加载过程中的加载这一步。主要作用就是加载 Java 类的字节码（ .class 文件）到 JVM 中（在内存中生成一个代表该类的 Class 对象）。

每个 Java 类都有一个引用指向加载它的 `ClassLoader`。**数组类不是通过 `ClassLoader` 创建的**（数组类没有对应的二进制字节流），是由 JVM 直接生成的。

加载原则：不会一次性加载所有的类，而是根据需要去**动态加载**。

JVM 中内置了三个重要的 `ClassLoader`：

1. `BootstrapClassLoader`(启动类加载器) ：**最顶层的加载类**，由 C++实现，通常表示为 null，并且没有父级，主要用来加载 JDK 内部的核心类库（ `%JAVA_HOME%/lib`目录下的 `rt.jar` 、`resources.jar` 、`charsets.jar`等 jar 包和类）以及被 `-Xbootclasspath`参数指定的路径下的所有类。
2. `ExtensionClassLoader`(扩展类加载器) ：主要负责加载 `%JRE_HOME%/lib/ext` 目录下的 jar 包和类以及被 `java.ext.dirs` 系统变量所指定的路径下的所有类。
3. `AppClassLoader`(应用程序类加载器) ：面向我们用户的加载器，负责加载当前应用 classpath 下的所有 jar 包和类



### 能不能自己写个类，也叫java.lang.String？

可以，但在应用的时候，需要用自己的类加载器去加载，否则，系统的类加载器永远只是去加载jre.jar包中的那个java.lang.String。由于在tomcat的web应用程序中，都是由webapp自己的类加载器先自己加载WEB-INF/classess目录中的类，然后才委托上级的类加载器加载，如果我们在tomcat的web应用程序中写一个java.lang.String，这时候Servlet程序加载的就是我们自己写的java.lang.String，但是这么干就会出很多潜在的问题，原来所有用了java.lang.String类的都将出现问题。

虽然java提供了endorsed技术，可以覆盖jdk中的某些类，具体做法是….。但是，能够被覆盖的类是有限制范围，反正不包括java.lang这样的包中的类。例如，运行下面的程序：

```java
package java.lang;
public class String {
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.out.println("string");
	}
}
```

报告的错误如下：
java.lang.NoSuchMethodError: main

Exception in thread "main"

这是因为加载了jre自带的java.lang.String，而该类中没有main方法。

### 双亲委派模型

类加载器有很多种，当我们想要加载一个类的时候，具体是哪个类加载器加载呢？这就需要提到双亲委派模型了。

`ClassLoader` 类使用**委派模型来搜索类和资源**。
双亲委派模型要求除了顶层的启动类加载器外，**其余的类加载器都应有自己的父类加载器**。
双亲委派模型并不是一种强制性的约束，只是 JDK 官方推荐的一种方式。
类加载器之间的父子关系一般不是以继承的关系来实现的，而是通常使用组合关系来复用父加载器的代码。**<font color='red'>组合优于继承，多用组合少用继承</font>**

1.双亲委派模型流程：
在类加载的时候，系统会**首先判断当前类是否被加载过**。已经被加载的类会直接返回，否则才会尝试加载。

尝试加载流程：首先不会自己去尝试加载，而是把这个请求委派给父类加载器去完成（调用父加载器 `loadClass()`方法来加载类）。所有的请求最终都会传送到顶层的启动类加载器 `BootstrapClassLoader` 中。
只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载（调用自己的 `findClass()` 方法来加载类）。

2.**JVM 判定两个 Java 类是否相同的具体规则** ：JVM 不仅要看类的全名是否相同，还要看加载此类的类加载器是否一样。只有两者都相同的情况，才认为两个类是相同的。即使两个类来源于同一个 `Class` 文件，被同一个虚拟机加载，只要加载它们的类加载器不同，那这两个类就必定不相同。

3.双亲委派模型的好处：双亲委派模型保证了 Java 程序的稳定运行，可以避免类的重复加载（JVM 区分不同类的方式不仅仅根据类名，相同的类文件被不同的类加载器加载产生的是两个不同的类），也保证了 Java 的**核心 API 不被篡改**。

# 新特性

## java 8新特性

### 接口增强

java8之前，只能包含常量（public static final）、抽象方法（public abstract）。接口修改的时候，实现类也要跟着改。
java8：做了接口增强，多了**默认方法**（public default，也叫扩展方法，有方法具体实现，允许实现类不重写）和**静态方法**（public static，不必重写）。因此接口升级时，可以提供便利。

java8下的abstract VS interface：
interface 是一个扩展插件； abstract class 的方法是要继承的。
interface 新增`default`和`static`修饰的方法，为了解决接口升级问题，并不是为了要替代`abstract class`。在使用上，该用 abstract class 的地方还是要用 abstract class，不要因为 interface 的新特性而将之替换。

### lamda表达式

函数式接口：SAM （Single Abstract Method interfaces），**有且只有一个抽象方法**，但可以有多个非抽象方法的接口。

Lambda 表达式是一个匿名函数，java 8 允许把函数作为参数传递进方法中。

```
(parameter) -> {expression} 或者 (parameter) -> {statements; statements; }
左边：实现的这个接口中的抽象方法中的形参列表 -> 右边：抽象方法的处理
new Thread( () -> System.out.println("lamda")).start();
```

```java
 MyInterface2 myInterface21 = (a, b) -> a - b; // 相当于MyInterface2唯一一个抽象方法执行的是a-b的操作
 myInterface21.show(20,40);// 调用MyInterface2的抽象方法show
```

compare的lambda表达式：Comparator和Comparable实现对包装类型的数组排序是很直接的，但是int一维数组不能放进去，二维的可以，Integer的一维可以

```java
 String[] str = new String[]{"aaaa", "aa", "c", "qwertyu"};
 Arrays.sort(str, (a,b) -> (b.length() - a.length())); // String类型长度排序
 Arrays.sort(str, (a,b) -> a.compareTo(b)); // String类型字典排序
 int[][] points = {{10,2}, {1,3}, {9,5},{1,10}, {9,100}};
 Arrays.sort(points, (a,b) -> (a[0] == b[0] ? b[1] - a[1] : a[0] - b[0])); // 双排序
 Integer[] pp = {1,10,30,15,100,50};
 Arrays.sort(pp, (a, b) -> (b - a)); 
 Arrays.sort(pp, (a,b) -> Integer.compare(b,a));// 一维Integer降序排序

Arrays.sort(points, (a,b) -> Integer.compare(a[0], b[0])); // 对于二维数组points，按照第一个元素小到大排序
Arrays.sort(points, (a,b) -> (a[0] == b[0] ? b[1] - a[1] : a[0] - b[0])); // 对于二维数组，双排序
Collections.sort(listList, (a,b) -> a.get(1).compareTo(b.get(1)));// 对于List<List<string>>，按照str[1]升序排序
Collections.sort(aaa, (a,b) -> a.compareTo(b)); // 对于List<String>每个元素字典序正序
Collections.sort(aaa, (a,b) -> b.compareTo(a)); // 对于List<String>每个元素字典序倒序
Collections.sort(aaa, Integer.compare(y.length(),x.length())) // 长度的倒序排序
```

### stream流

Stream 类 **SQL 语句**，不存储数据，可以做筛选、排序、过滤、统计。源数据可以是Collection和Array
一个 Stream 只能操作一次，操作完就关闭了，继续使用这个 stream 会报错。
Stream 不保存数据，不改变数据源

**流的操作类型分为两种:**
**Intermediate**：中间操作，打开流，做数据映射/过滤，惰性化的（lazy），transformer算子，只是定义，没有真正开始流的遍历。
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted

**Terminal**：终结操作，一个流只能有一个`terminal`操作，action算子。
forEach、 toArray、  collect、 min、 max

```java
List<String> strings = Arrays.asList("abc", "", "bcd","bc","bca", "efg", "abcd","", "akly","jksda","cdf", "098","jk");
// filter sort，迭代器类型，传进去lamda表达式
List<String> filters = strings.stream().filter(string -> !string.isEmpty()).sorted((x,y) -> y.compareTo(x)).sorted((x,y) -> Integer.compare(y.length(),x.length())).collect(Collectors.toList());
// mapToInt  将list转为array
int[] arrInt = integerList.stream().mapToInt(x -> x).toArray();
// anyMatch return boolean ， findFirst return Optional
boolean angMatch = strings.stream().anyMatch(s -> s.startsWith("a") || s.startsWith("A"));
Optional<String> aa = strings.stream().filter(s -> s.length() == 8).findFirst();
String bb = aa.orElse("Not Found");
// terminal操作，reduce,min,max,collect
int resultMax = Arrays.stream(integers).reduce(0,Integer::max);
int resultMIn = Arrays.stream(integers).reduce(0,Integer::min);
// map 
String rr = Arrays.stream(arrRes).boxed().map(i -> i.toString()).collect(Collectors.joining(":"));
```



让书写更简便，与lamda表达式联用

- 静态方法引用，通过类名::静态方法名， 如 Integer::parseInt
- 实例方法引用，通过实例对象::实例方法，如 str::substring
- 构造方法引用，通过类名::new， 如 User::new

### Optional

**Optional：将可选值作为一种数据类型，地位和基本类型平起平做，地位非常高。**

Optional解决空指针

## java9新特性

|            | java8          | java9          |
| ---------- | -------------- | -------------- |
| GC         | parallel收集器 | G1为默认收集器 |
| String存储 | char[]         | byte[]         |
| 接口升级   | static default | 增加private    |

## java17新特性

|                | java17之前                     | java17                                       |
| -------------- | ------------------------------ | -------------------------------------------- |
| 伪随机数生成器 | Random生成，缺少伪随机算法支持 | RPNG，支持切换各种算法                       |
| switch         |                                | 增加了类型匹配自动转换，instanceof和null判断 |

```java
static String formatterPatternSwitch(Object o) {
    return switch (o) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> o.toString();
    };
}
switch (s) {
        case null         -> System.out.println("Oops");
        case "Foo", "Bar" -> System.out.println("Great");
        default           -> System.out.println("Ok");
}
```



# I/O

## java流类型

Java IO 流的 40 多个类都是从如下 4 个抽象类基类中派生出来的。

- `InputStream`/`Reader`: 所有的输入流的基类，字节/字符输入流。
- `OutputStream`/`Writer`: 所有输出流的基类，字节/字符输出流

字节流：不管是文件读写还是网络发送接收，信息的最小存储单元都是字节。
字符流：字符流是由 Java 虚拟机将字节转换得到的，这个过程还算是比较耗时。如果我们不知道编码类型就很容易出现乱码问题。默认Unicode编码
底层设备永远只接受字节数据

**音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好（中文乱码）。**

## 字节和字符流示例

bufferedInputStream bufferedOutputStream
read读取的是字节或者int类型，读取字节流；
bufferedReader里面的readLine()读取的是string，字符流
BufferedInputStream比FileInputStream速度快

```java
// 记录开始时间
long start = System.currentTimeMillis();
try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("resources/软件使用说明.pdf"));
 BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("resources/软件使用说明-副本.pdf"))) {
int content;
byte[] bytes = new byte[4 * 1024];
// 一组一组读，更快
while ((content = bis.read(bytes)) != -1) { // 写入的是bytes数组，打印出来不认识
    bos.write(bytes, 0, content);
}
// 一个字节一个字节读，慢
//while ((content = bis.read()) != -1) {
//    System.out.println((char)content); // 写入的是content，可以打印出来，content是int类型
//    bos.write(content);
//}
        } catch (IOException e) {
e.printStackTrace();
        }
        // 记录结束时间
        long end = System.currentTimeMillis();
        System.out.println("使用缓冲流复制PDF文件总耗时:" + (end - start) + " 毫秒");
        // 使用缓冲流复制PDF文件总耗时:1274 毫秒，read() write(content)
        // 使用缓冲流复制PDF文件总耗时:24 毫秒，read(bytes) write(bytes,0,length)
    }
```

字符流

FileReader OutputStreamWriter

```java
    public void fileReaderWrite() {
        StringBuffer sb = new StringBuffer();
        try(BufferedReader fr = new BufferedReader(new FileReader(path2));
BufferedWriter fw = new BufferedWriter(new FileWriter(outpath2))) {
String content;
fr.skip(2);
while ((content = fr.readLine()) != null) { // 写入的content是string，字符流，可以看懂
    sb.append(content).append(";;;\n");
}
System.out.println(sb.toString());
fw.write(sb.toString());
        } catch (IOException e) {
e.printStackTrace();
        }

    }
```

## 随机访问流

支持随意跳转到文件的任意位置进行读写的 `RandomAccessFile` 

seek(int n) // 从偷开始移动偏移量，getFilePointer()得到当前游标的偏移量；支持r（只读）rw（读写）

```java
    public void random(){
        try(RandomAccessFile randomAccessFile = new RandomAccessFile(new File(path), "rw")) {
System.out.println("读取之前的偏移量：" + randomAccessFile.getFilePointer() + ",当前读取到的字符" +
        (char) randomAccessFile.read() + "，读取之后的偏移量：" + randomAccessFile.getFilePointer());
randomAccessFile.seek(6);// 指针当前偏移量为 6
System.out.println("读取之前的偏移量：" + randomAccessFile.getFilePointer() + ",当前读取到的字符" +
        (char) randomAccessFile.read() + "，读取之后的偏移量：" + randomAccessFile.getFilePointer());
randomAccessFile.write(new byte[]{'H', 'I', 'J', 'K'});// 从偏移量 7 的位置开始往后写入字节数据
        } catch (IOException e) {
e.printStackTrace();
        }
    }
```

大文件上传问题：分片上传

## 设计模式23种

设计模式的六大原则

开闭原则（Open Close Principle）对扩展开放，对修改关闭
里氏代换原则：任何基类可以出现的地方，子类一定可以出现
依赖倒转原则：面向接口编程，尽量依赖于抽象而不依赖于具体
接口隔离原则：使用多个隔离的接口，比使用单个接口要好
迪米特法则（最少知道原则）：一个实体应当尽量少的与其他实体之间发生相互作用
合成复用原则：尽量使用合成/聚合的方式，而不是使用继承

### 创建型5种——单例 & 工厂 & 建造者 & 原型

保证整个应用中有且只有一个实例。(1) 不允许其他程序用new对象（私有化该类的构造函数）；(2) 在该类中创建对象；(3) 对外提供一个可以让其他程序获取该对象的方法。[单例模式双重锁](#volatile synchronized)

工厂模式：用于创建对象。

简单工厂模式：将对象的创建过程交由专门的对象（工厂）去做，专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。（店里买肉夹馍，**父类调用子类**生成肉夹馍）；
抽象工厂方法（开分店）：不用父类子类，而用接口和实现类。定义创建对象的接口，**将创建实例的活推迟到实现类**，实现类生成肉夹馍。

建造者模式：工厂是创建单个类，建造者可用来创建**复合对象**，每个类具有不同的属性，比如一个list里面有arrayList对象和LinkedList对象。

原型模式：用原型实例指定创建对象种类，**深拷贝原始对象**，无需知道对象创建的细节

### 装饰器 & 适配器 & 代理 & 桥接

1.装饰器：组合替代继承，接口或抽象类的实现类的组合。在不改变原有对象的情况下拓展其功能。装饰器类**需要**跟原始类继承相同的抽象类或者实现相同的接口。

通过 `BufferedInputStream`（字节缓冲输入流）来增强 `FileInputStream` 的功能，即前者的构造参数中需要后者。

```java
 BufferedInputStream fis = new BufferedInputStream(new FileInputStream(path)
```

2.适配器：让原本接口**不兼容不能交互**的类可以相互合作。OutputStreamWriter、InputStreamReader就是字节流和字符流的适配器。适配器和适配者两者**不需要**继承相同的抽象类或者实现相同的接口。`FutureTask` 使用配器模式，将 `Runnable` 适配成 `Callable`。

```java
try( BufferedWriter fw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(outpath2)));
    BufferedReader fr2 = new BufferedReader(new InputStreamReader(new FileInputStream(path2)))) {}
```

3.代理：代理类（proxy class）和真实处理的类（real class）都实现同一个接口，但是让代理去帮忙处理一些事务。

| 装饰器                           | 代理                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| 强调增强自身                     | 强调让代理帮自己处理一部分业务无关的任务                     |
| 对客户端透明的方式扩展对象，组合 | 给一个对象提供一个代理对象，并由代理对象来控制对原有对象的引用 |
| 为装饰的对象增强功能             | 对代理的对象施加控制                                         |

4.桥接：把抽象化与实现化**解耦**，使得二者可以独立变化。通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。

### 观察者 解释器 策略 迭代器

观察者：当一个对象变化时，**其它依赖该对象的对象都会收到通知**，并且随之变化。如：NIO 中的文件目录监听服务：`WatchService` 用于监听文件目录的变化，同一个 `WatchService` 对象能够监听多个文件目录Wachable（被观察者）；spring事件驱动模型

解释器：实现了一个表达式接口，该接口解释一个特定的上下文。这种模式被用在 SQL 解析、符号处理引擎等。（::）

策略：定义了算法族，分别封装起来，让它们之间可相互替换，此模式让算法的变化独立于使用算法的客户。

迭代器：顺序访问聚集中的对象，集合中非常常见

## IO模型

I/O 描述了计算机系统与外部设备（输入输出设备）之间通信的过程。用户空间的用户进程想要执行 IO 操作的话，必须通过 **系统调用** 来间接访问内核空间，即用户程序发起IO调用，操作系统内核执行IO操作。最多的是 **磁盘 IO**（读写文件） 和 **网络 IO**（网络请求和响应）。

| IO模型      | 定义                                                         | 优缺点                                                       |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BIO同步阻塞 | 发起 read 调用后，应用程序会一直阻塞，直到内核把数据拷贝到用户空间 | 无法应对高并发。                                             |
| 同步非阻塞  | 应用程序会一直发起 read 调用，没有阻塞，**轮询**内核是否准备好数据。<br />拷贝数据的期间线程是阻塞的，直到在内核把数据拷贝到用户空间。 | 通过轮询操作，避免了一直阻塞。<br />但轮询也很耗费资源。     |
| NIO多路复用 | 非阻塞。线程首先发起 select 调用，通过**选择器（多路复用器）监听多个通道**，询问内核数据是否准备就绪，就绪之后再发起 read 调用。read 调用的过程（数据从内核空间 -> 用户空间）还是阻塞的。 | 减少无效的系统调用，减少了对 CPU 资源的消耗。<br />有 select，epoll。广泛使用 |
| AIO异步     | 基于事件和**回调**机制。read之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行回调数据。 | 用的少                                                       |

Java 中的 NIO ，有一个非常重要的选择器 ( Selector )，即多路复用器。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0f483f2437ce4ecdb180134270a00144~tplv-k3u1fbpfcp-watermark.image" alt="img" style="zoom:33%;" />



<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a9e704af49b4380bb686f0c96d33b81~tplv-k3u1fbpfcp-watermark.image" alt="图源：《深入拆解Tomcat & Jetty》" style="zoom: 50%;" />



<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb174e22dbe04bb79fe3fc126aed0c61~tplv-k3u1fbpfcp-watermark.image" alt="图源：《深入拆解Tomcat & Jetty》" style="zoom:50%;" />

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88ff862764024c3b8567367df11df6ab~tplv-k3u1fbpfcp-watermark.image" alt="img" style="zoom:50%;" />

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3077e72a1af049559e81d18205b56fd7~tplv-k3u1fbpfcp-watermark.image" alt="img" style="zoom:50%;" />
