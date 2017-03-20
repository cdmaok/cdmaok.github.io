---
layout: post
title : Effect Java
category: Java
---

## 使用静态工厂方法代替构造器  
  * 优势在于  
    * 区别与设计模式中的工厂模式  
    * 优势在于静态方法有名称，可以更加准确的描述正被返回的对象。  
    * 可以返回旧的实例，而不是每次都新建一个对象  
    * 可以返回原类型的任何子类型的对象  
  * 可能的缺点  
    * confused with other static function  


## 使用Builder代替构造器  
```Java  
    public class Object{  
      int a;  
      int b;  
      int c;  
      int d;  
      int e;  

      // 如果使用构造器，需要部分默认构造器  
      public Object(int a){  
        this.a = a;  
        b = DEFAULT;  
        c = DEFAULT;  
        d = DEFAULT;  
        e = DEFAULT;  
      }
      //...  
      public Object(int a, int b, int c, int d, int e){  
        this.a = a;  
        this.b = b;  
        this.c = c;  
        this.d = d;  
        this.e = e;  
      }  
      // 如此一来构造器太多，而且容易因为参数的位置而出错。

      // 一种简单的方法是使用bean的思想
      // 空构造器及set方法
      {
        Object c = new Object();
        c.seta(a);
        c.setb(b);
        c.setc(c);
        c.setd(d);
        c.sete(e);
      }
      public void setc(int c){
        this.c = c;
      }


      // 这样还是有机会出错，因为有可能漏掉某些set方法
      // 一种简单方法是修改set方法
      public Object setc(int c){
        this.c = c;
        return this; // this is important to make it like chain.
      }
      // 新的构造方案
      {
        Object c = new Object().seta(a).set(b).setc(c).setd(d).sete(e);
      }

      // 至此代码已经开始清晰，但是还是可能会引起误解，第三方不一定知道这样写。

    }
```
  * 引用builder

```Java  
    public Object{  
      public static class Builder{  
        public Builder(int a, int b){  
          // 这样表示a，b是必须的参数
          this.a = a;
          this.b = b;
        }

        public Builder setc(int c){
          this.c = c;
          return this; // this is important
        }

        // build function
        // this is important
        public Object build(){
          // 在返回之前这里可以添加参数检查，如果有不满足的参数就抛出
          return new Object(this);
        }
      }

      private Object(Builder builder){
        // Object 仅仅接受以builder为参数的构造器
        // 仅仅传递参数
        this.a = builder.a;
        this.b = builder.b;
      }

      // 构造方法  
      {
        Object o = new Object.Builder(a,b).setc(c).setd(d).build();
      }

    }
```
      * 现在有接口可以引入  
```Java
  public interface Builder<T>{
    public T build();
  }
```
      * 如果这个类参数不多，或者不太可能拓展，还不如直接用原来的构造方法解决。  


## 单例模式
  * **Method 1**

```Java  
  public class Elvis{  
    public static final Elvis INstance = new Elivs();  
    private Elivs(){  
      // 私有构造器  
      ...  
    }
```  
  }  
    * 私有构造器有可能通过反射机制获取 by AccessibleObject.setAccessible(),通过修改构造器，让构建第二个实例时抛出异常

  * **Method 2 更加常见的方法**  

```Java
  public class Elvis{  
    public static final Elvis INstance = new Elivs();
    // 更加急切的初始化，未必会用到这个类。
    public static Elvis getInstance(){
      return INstance;
    }
  }
```  
  * **Method 3 单个元素的枚举**  

```Java
  public enum Elivs{
    INstance;
    //function....
  }
```
    * 简洁，提供了序列化机制。


## 避免实例化工具类
  * 如工具类xxUtility.class 没有实例化的必要
  * like this

```Java
    public class Utility{
      private Utility(){
        // 私有化构造器
        throw Exception
      }
    }
```

## 对象创建
```Java
 public class foo{
   public void bar(Object str){
     return new Gson().toString(str);
   }
 }
```
 此时如果需要频繁调用bar（）方法时，new Gson()带来的创建对象的时间无法忍受，合适的方法是需要变成静态成员对象
```Java
 public class foo{
   public static Gson gson = new Gson();
   public void bar(Object str){
     return gson.toString(str);
   }
 }
```
 此处并非强调说尽量避免创建对象，如果创建对象能节省能提升功能和简洁性，鼓励创建对象


## 重写hashCode
* 重写equals方法时，必须重写hashCode()方法，但同时需要保持一定的散列度，否则把hashMap的性能丢失
* 以下是一些tips:
  * 常量，直接保存在int result 中
  * boolean, c = (f?1:0)
  * byte/char/short/int, c= (int)f
  * long, c = (int)(f ^ ( f>>>32 ))
  * float, c=Float.floatToIntBits(f)
  * double, c = Double.doubleToIntBits(f),然后按long处理
  * Object，递归调用以上的情形
  * Algorithm  

```Java
    result = 0
    For field in Object:
      c = hashCode(field)
      result = 31 * result + c
    return result
```

## 不可变类的好处
```Java  
public final Class Complex{  
  // 使用 final 表示不可继承，保证不被子类修改  
  // 如果不能接受这种设置，就提供静态工厂方法，参考第一条。  
  ** private ** final double re;
  private final double rm;

  public Complex(double re, double rm){
    this.re = re;
    this.rm = rm;
  }

  public Complex add(Complex c){
    // 不返回原来的对象，而是新建一个对象。
    return new Complex(re + c.re, rm + c.rm);
  }

}
```
* 比较简单，不易出错，本质上是线程安全的。
* 考虑到不断的新建实例，一个解决方案是对提供公有的静态final成员  
```Java  
public static final Complex ZERO = new Complex(0,0)
```
; 另外一种方案就是如同String建立一个可变类StringBuffer

## 抽象类与接口  
* 抽象类提供某些方法的指导实现，可是受限于单继承的设置原则，而接口不能提供实现，接口的作用在于定义类型和方法，不适合添加成员变量，为了综合这两者，可以提供骨架实现类。**每设计一个接口都应该实现一个骨架实现类，帮助指导实现接口**  

```Java  
public abstract class abstractInterface implements interface{
  @override
  public boolean f(){
    //...do sth.
  }
  // ...
}
```

## 泛型
```Java
// API interface
public class Favourite{
  private Map<Class<?>, Object> favorites = new HashMap<Class<?>,Object>;

  public <T> void putFavorite(Class<T> type, T instance){
    favorites.put(type,instance)
  };
  public <T> T getFavorite(Class<T> type){
    // type cast here
    return type.cast(favorites.get(type))
  };
}

public static void main(String[] args){
  Favourite f  = new Favourite();
  f.put(String.class, "Java");
  f.put(Integer.class, 0xcafebabe);
  // String.class match Class<T>  
  f.put(Class.class, Favorite.class);

  String favoriteStr = f.getFavorite(String.class);
  int favoriteInt = f.getFavorite(Integer.class);
  Class<?> favouriteClass = f.getFavorite(Class.class);

}
```

## 枚举
  * 使用EnumSet代替位域，将许多枚举类型使用OR运算符合并到一个集合当中去。  
  * 使用EnumMap代替序数索引

## 方法的编写  
  * 检查参数有效性
    * 在JavaDoc中注明要抛出的异常
    * 添加断言assert
    * 如果检查代价昂贵，并且计算中会抛出异常，可以不作检查。  

    ```Java
    /*
    * @param a,b
    * @return ....
    * @throws blaException
    */
    public void f(int a,int b){
      // this is a function.
      assert a != null;
      assert b > 0;
      throws new blaException("...");
    }
    ```
  * 保护性拷贝

  ```Java  
  public final class Period{
    private final Date start;
    private final Date end;

    /*...
    * 要求end要比start晚  
    * throws IllegalArgumentException("")  
    */  
    public Period(Date start,Date end){
      if(start.compareTo(end) > 0 ){
        // 抛出异常
        throws new IllegalArgumentException("wrong")
        this.start = start;
        this.end   = end;
        // 这个地方是有问题的，由于直接用引用进行赋值，如果end在构造函数之后在外面有修改，那有可能突破上面的检查，因此最好做拷贝  
        this.start = new Date(start);
        this.end = new Date(end);
      }
    }  

    public Date start(){
      // 此处同理，外面可以对拿到的引用做修改。
      return start;
      // better be this.
      return new Date(start);
    }

    public Date end(){
      return end;
      // same reason, better be this.
      return new Date(end);
    }
  }
  ```

# 常用的异常
  * IllegalArgumentException 传递的参数不合适
  * IllegalStateException 某个对象未正常初始化之前，调用者企图使用这个对象
  * NullPointerException 传递null参数
  * IndexOutofBoundsException 调用者在表示序列下标的参数中传递了越界的值
  * ConcurrentModificationException 某个对象被并发的修改
  * UnsupportedOperationException 对象不支持所请求的操作

#简单并发编程

```Java
  synchronized(obj){
    while(<condtion does not hold>){
      obj.wait();
      // release lock, and reacquires on wakeup
      // always use wait in a loop.
    }
    ...
    // perform action appropriate to condition
  }
```
