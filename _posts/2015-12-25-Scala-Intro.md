---
layout: post
title : Scala Intro
category: Scala
---

读取自[Scala编程](https://book.douban.com/subject/5377415/)

### Scala 是函数式的语言
  * 函数是一类值，可以作为参数进行传递，如Python，这里需要区别于C/C++的函数指针。
  * 程序数据是不可变的，程序操作符应该把输入映射到输出值而不是就地修改数据,简单的举例
  就是如同Java中的String类，本质是不可变的，仅仅是做了引用的修改，在Scala上这样的思想
  应该更加明显。

### 简单函数  
```Scala
def max(x:Int, y:Int): Int = {  
  /* def为函数关键字  
   * 参数列表中每个函数先声明名称，然后是变量类型  
   * 返回函数中首先声明返回类型  
   * 如果是空返回，可以填Unit  
   */  
  if(x > y)  
    x  
  else  
    y  
}  
```

### val 和 var，前者表示**常量**，后者表示**变量**

### 简单的函数式编程思想，假设假设想打印一个String数组args  
```Scala
// java 风格的scala代码  
var i = 0  
while( i < args.length ){  
  println(args(i)) // scala中用()而不是[]  
  i += 1  
}  
//====这里是分割线，以下是更加scala风味的代码---  
args.foreach((arg: String) => println(arg))  
args.foreach( arg => println(arg)) // 此行是简写版本，忽略类型声明  
args.foreach(println) // 极简风格  
/* 函数文本语法  
 *(x : Int, y : Int) => x + y  
 *以'=>'为界，左边是输入声明，右边是函数体  
 */  
//---- 不想用foreach这种东西，更加通用的For----
for(arg <- args){  
  println(arg) //类似于Java中的for(String str: strings),better  
}  
```

###  从 apply() 方法讲起  
```Scala
假设我们要初始化一个String数组  
val strings = new Array[String](3) // 类型推断+ 省略  
string(0) = "Hello world"  
// 听起来像是说val是常量，不能改变，但是这个strings里面存的只是引用而已  
// 这里改的是引用所指向的对象。  
// 这里的实际操作是string.update(0,"Hello world")  
// 更加好的方式是：  
val strings = Array("Hello world","xx","xx")  
// 其背后操作是这样的  
val strings = Array.apply("Hello world","xx","xx")
再如取值操作string(i) 相当于string.apply(i)
```

### List  
  * Scala中的list也是不可变的对象，和String一样，当对list进行操作时，本质上是重新生成一个
list，然后返回引用  
  * 基本操作  

  Method | explanation
  ---------|------------
  val thrill = "Will" :: "Fill" :: "util" :: Nil |  使用::进行拼接，最后必须是Nil
  val thrill = new List("Will","Fill","util") | 另外的初始化，无需Nil
  thrill.count(s => s.length == 4) | 统计长度为4的字符串
  thrill.filter(s => s.length == 4) | 过滤操作
  thrill.map(s => s+'y') | 映射操作，每个元素加'y'
  thrill.mkString(",") | join操作
  thrill.sort((s,t) => s.length > t.length) | 排序操作，类似定义comparable接口
  thrill.head | 返回第一个元素
  thrill.last | 返回最后一个元素
  thrill.tail | 返回除第一个元素外其余元素
  thrill.init | 返回除最后一个元素的其余元素列表
  * 注意到没有append操作，因为是不可变的，解决方案是可以用ListBuffer等东西

### Tuple
```Scala
  val pair = (99,"Hello World") // 不同类型数据混编  
  print(pair._1)  
  print(pair._2)  
  // 取值方式，以及索引从**1** 开始  
```

### 读写文件  
```Scala
// 读文件
import scala.io.Source  
if(args.length > 0){  
  for(line <- Source.fromFile(args(0)).getLines)  
      print(line.length + " " + line)
}  
```

### 类和对象  
```Scala
// 一个简单的类  
class ChecksumAccumulator{  
  private var sum = 0  
  def add(b: Byte){ sum += b}  
  def checksum() Int = ~(sum & 0xFF) + 1  
}  
// Scala中没有静态成员，对应的是单例对象来解决问题  
object ChecksumAccumulator{  
// 这里要和对应的class同名，作为伴生对象  
  private val cache = Map[String, Int]()  
  def calcuate(s:String) Int = {  
    if cache.contain(s){  
      cache(s)  
    }else{  
      val acc = new ChecksumAccumulator  
      for( c <- s)  
        acc.add(c)  
      val cs = acc.checksum()  
      cache += (s,cs)  
      cs  
    }  
  }  
}  
// 总的调用方法  
ChecksumAccumulator.calcuate("hello world")  
```

### Application  
```Scala
//一个程序的开始如同java一样，要有一个main程序  
object Summer{  
  def main(args: Array[String]){  
    for(arg <- args){  
      print(arg)  
    }  
  }  
}  
// 一种代替main函数的解决方案就是继承application类  
object Summer extends Application{  
  // 直接写main函数里面的内容,这样的坏处是不能从命令行中传参数进来  
  for(arg <- new Array("Hello","world")){  
    print(arg)  
  }  
}  
```

### 尝试写一个类  
```Scala
//我们尝试构造一个分数类，存贮分子，分母，以避免浮点数的精度丢失  
class Rational(n : Int, d: Int){  
  // 首先覆盖toString方法，那么print的时候不会打印出一个引用的地址  
  override def toString = n + "/" + d  
  // 然后应该是补全条件，防止分母为0的情况,如果不满足条件会抛出异常    
  require( d != 0 )  
  // 接下来添加运算函数，以加为例
  def add(that : Rational): Rational = {  
    new Rational(n * that.d + that.n * d, d * that.d)
    // 这里是不对的，因为that.d无法得到，Scala 无法识别
  }  
  -------正确的做法---------  
  val numer: Int = n  
  val demon: Int = d  
  def add(that : Rational): Rational = {  
    new Rational(numer * that.demon + that.numer * demon,
    demon * that.demon)  
  }  

  //构造默认参数构造器  
  def this(n: Int) = this(n,1)  
  // 假设有最大公约数不让外界访问，使用private  
  private val g = gcd(n,d)  

  // 为了直观的表示函数的含义，add函数可以用+符号进行重载，这是符合Scala操作符  
  即函数的思想的。  
  def +(that: Rational): Rational = {  
    new Rational(numer * that.demon + that.numer * demon,  
    demon * that.demon)  
  }
  // 重载的更加彻底一些
  def +(that: Int): Rational = {  
    new Rational(number + that * demon, demon)  
  }
}
```

### for-yield  
```Scala
//用来生成生成器  
//for {子句} yield {子句}  
val forLineLength = for {
  file <- filesHere  
  if file.getName.endsWith(".Scala")  
  line <- fileLines(file)  
  trimmed = line.trimm  
  if trimmed.matches(".*for*")  
} yield trimmed.length  
```

### 占位符  
```Scala
val someNumber = List(-1,2,3,4,5)  
//下面做过滤操作  
someNumber.filter((x:Int) => x > 3)  
// better for type inferencing  
someNumber.filter((x) => x > 3)  
// delete parenthesis  
someNumber.filter( x => x > 3)  
// 占位符, 能够推断出类型  
someNumber.filter( _ > 0)  
// 假设定义函数  
val f = _ + _ // 这样不行，因为无法做类型推断  
val f = (_ : Int) + (_ : Int) // call f(3,5)   
// 可用于重载  
val b = sum(1,_:Int,3) // b(2) => sum(1,2,3)  
```

### 重复参数  
```Scala
def echo(args: String*) =  
  for(arg <- args) println(arg)  
// 如上定义，调用时可以传入不定的参数  
echo("hello")  
echo("foo")  
echo("bar")
// 这个时候你恰巧有个数组  
val arr = Array("foo","bar")  
//echo(arr) 是不对的  
echo(arr:_*) // 告诉编译器将每个元素传进去  
```

### 重复控制模式
```Scala
// 打开一个文件对其进行操作，然后关闭资源  
def withPrintWriter(file:File, op: PrintWriter => Unit){  
  val writer = new PrintWriter(file)  
  try{  
    op(writer)  
  }finally{  
    writer.close()  
  }
}
// 调用方法  
withPrintWriter(new File("test.text"),writer=> writer.println(new Java.util.Date))  

// curry 化函数,修改定义参数的地方  
def withPrintWriter(file:File)(op: PrintWriter => Unit){  
  val writer = new PrintWriter(file)  
  try{  
    op(writer)  
  }finally{  
    writer.close()  
  }  
}
// 此处调用  
withPrintWriter(new File("test.txt")){  
  writer => writer.println(new Java.util.Date)  
}  
//NOTE: 单个参数使用{}，curry化的好处在于此。  
```

### object 工厂模式  
```Scala
// 抽象类  
abstract class Element{  
  def contents: Array[String] // 定义了方法，但是没有实现  
}  
// 实现这个抽象类  
class ArrayElement(conts: Arrray[String]) extends Element{  
  def contents: Array[String] = conts  // 具体实现了这个方法  
  // 用val重载def，将contents从方法改变成变量  
  val contents: Array[String] = conts  
}
// call  
val ae = new ArrayElement(Array("Hello","World"))  
// 继承这个超类,这里调用了超类的构造器  
clas LineElement(s:String) extends ArrayElement(Array(s)){  
  override def width = s.length  
  override def height = 1  
}
// 一种实现Element的新实现  
class UniformElement{  
  ch:Char,  
  override val width: Int,  
  override val height: Int  
}extends Element{  
  // 这个放到体内是不是更好  
  private val line = ch.toString *width  
  def contents = Array.make(height,line)  
}  
// 现在有Element的三种实现，需要用工厂封装起来，方法就是使用element的伴生对象  
// 建立object  
object Element{  
  def elem(contents: Array[String]): Element={  
  new ArrayElement(contents)  
  }  
  def elem(chr: Char, width: Int, height: Int): Element={  
  new UniformElement(char,width,height)  
  }  
  def elem(line:String): Element={  
  new LineElement(line)  
  }
  // 如此一来可以把ArrayElement,UniformElement,LineElement等等全部变成私有类塞进来
  private class ArrayElement ...  
  private class UniformElement ...  
  private class LineElement ...  
}  
//call => elem.elem(...)  
```

### 特质 trait
```Scala
/*  
 *看上去像是带有具体实现的java接口  
 */  
 // 定义一个接口，不能参数！！  
 trait Philosophical{  
  def philosophize(){  
    println("....")  
  }  
 }  
 // 继承这个特质，使用关键字extends或with   如果已经继承了什么类，那就用with  
 class Frog extends Philosophical{  
  // Frog's logic  
 }   
 // 重载trait里面的方法  
 class Frog extends Animal with philosophize{  
  // Frog 's logic'  
  override def philosophize(){  
    println(“override here”)  
  }
 }  
 // exampe 继承ordered特质
 class Rational(n: Int, d: Int) extends Ordered[Rational]{  
  // ...  
  // override this function from Ordered trait  
  def compare(that: Rational) = {  
    (this.numer * that.demon) - (that.numer * this.demon)  
  }  
 }  
 // 堆叠特质  
 abstract class IntQueue{  
  def get(): Int  
  def put(x:Int)  
 }  
 //令加入的东西翻倍  
 class BasicIntQueue extends IntQueue{  
  // ...  
 }  
 trait Doubling extends IntQueue{ // 定义了超类，所有拓展了IntQueue的类都将
 得到这个特性  
  abstract override def put(x: Int){ super.put(x * 2)}  
  //值得注意的是super是动态绑定，另必须组合使用abstract 和 override  
 }  
 // 看看具体效果  
 val queue = new BasicIntQueue with Doubling  
 queue.put(20)  
```
8d3202c017047c0cfdgong01
