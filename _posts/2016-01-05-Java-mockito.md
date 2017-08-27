---
layout: post
title: Java Mockito
category: Java
---

[Mockito](http://mockito.org/)是一种Java的测试框架，在现实环境中，通常有些代码是用来连接其他组件用的，如连接Mongo，Kafka等操作，而这些操作会影响到某些函数无法做单元测试，可能在offline的环境下或者生产环境中没有可供连接的组件。一种解决方案就是用mock来模拟这些操作，当执行数据库数据库的连接时，指定返回某些内容来取代真实的连接，用于测试接下来的其他操作。还有一些其他的mock测试框架比如easyMock之类的，按下不表。

### Import via Maven
```
<dependency>
  <groupId>org.mockito</groupId>
  <artifactId>mockito-core</artifactId>
  <version>1.10.19</version>
</dependency>
```

### 简单例子
比较常用的几个feature是mock，spy，injectmock  

```Java  
import static org.mockito.Mockito.*;  

// mock creation  
List mockedList = mock(List.class);  
// 还有一种优雅的做法，使用注解  
@Mock  
List mockedList;  

// using mock object - it does not throw any "unexpected interaction" exception  
mockedList.add("one");  
mockedList.clear();  

// selective, explicit, highly readable verification  
// verify 的用处在于说明mockedList 确实执行过这两个操作  
verify(mockedList).add("one");  
verify(mockedList).clear();  
```  
注意到这样生成的Mock对象里面是干净的，所有的成员对象都是空的，包括所有的成员函数都是空的，这样可能太麻烦，不满足我们的需求。  
![mockito-mock][mockito-mock-png]  
这是因为Object.class的构造方法中没有任何参数，所以成员对象为空也是正常的。于是要用到另外一种东西Spy(partially Mock)  

```Java
//使用注解，这里是用的静态工厂的构造方法，用带参数的构造方法也ok
@Spy  
AbstractJoiner joiner = JoinerFactory.createJoiner(String);  
// 不适用注解的方法
AbstractJoiner joiner = spy(JoinerFactory.createJoiner(Sring))  
```
根据调试结果可以看到成员变量非空，如果没有覆盖掉原来的成员方法的话，会按照原本定义的函数执行。  
![mock-spy][mockito-spy-png]  
定义函数

```Java
// you can mock concrete classes, not only interfaces  
LinkedList mockedList = mock(LinkedList.class);  
// stubbing appears before the actual execution  
when(mockedList.get(0)).thenReturn("first");  
//遵照定义，private的方法是不能这样玩的，不过其实不影响正常的test，因为外部根本access不到这个方法。
// 这里的问题是修改的方法可能签名比这个复杂得多（带对象参数等）。
// 以为例引用matcher，这里的参数是一个objectNode，要想定义两个objectNode相等不容易，最好的方法是忽略参数是否相等，只要class相同就好  
public Collection<ParsedDataMessage> query(ObjectNode node)
// 通过matchers匹配，matchers有简单的anyInt之类的方法
import org.mockito.Matchers;  
when(joiner.query(Matchers.any(ObjectNode.class))).thenReturn(resultMsgs);  
```
接下来描述一个复杂的例子,情景如下，Class A 中有一个私有的成员Class B，B是用来连接mongo的，A的Function f 是用B连接Mongo获得的数据进行下一步操作。那么如何对Class A 的Function F做测试呢。

```Java
class B{  
  public String query(){  
    // connect to db  
    return string  
  }  
}  
class A{  
  private class B;  
  public Function f(){  
    b.query()  
    // more thing  
    return string  
  }  
}  

//由于无法acess到A内部的B，所以要在外面mock一个B，然后注入到A内部完成这件事情
@RunWith(MockitoJUnitRunner.class)  
class Atest{
  @Mock
  B b;

  @InjectMocks
  A a;

  @Before  
  public void setup(){  
    // to do setup
    // 加上这个就完成注入，据说@RunWith(MockitoJUnitRunner.class) 这句话也是这个作用，但是我亲测不行。不晓得为啥
    MockitoAnnotations.initMocks(this);
    when(b.query()).thenReturn(some str);  
  }  
  @Test  
  public void testf(){  
    // to do something
    A.f();  
    // assertEquals ..
  }  
}
```

看到A(StatJoinProcessor)中的B(joiner)确实是mockito加持过的。B的方法也已经重新被定义过了。这样就可以测了。然而，不知道为什么spy和InjectMocks不能一起用，如果可以的话就更加方便了。  
![mockito-inject][mockito-inject]

以上是mockito的入门小结。

[mockito-inject]: http://7xpv97.com1.z0.glb.clouddn.com/mockito-mock.png
[mockito-mock-png]: http://7xpv97.com1.z0.glb.clouddn.com/mockito-mock.png
[mockito-spy-png]: http://7xpv97.com1.z0.glb.clouddn.com/mockito-inject.png
