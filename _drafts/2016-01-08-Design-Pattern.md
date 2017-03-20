---
layout: post
title: Design-Pattern
category: Java
---

### Strategy Pattern
动态组合接口而不是采用2^N的类组合继承。
![UML-stragety][UML-stragety]
ObjectAid UML


### 观察者模式
UML 图![UML-Observer][UML-Observer]

```Java
package test;

import java.util.Observable;
import java.util.Observer;

class GeneralDisplay implements Observer{
	Observable observable;

	public GeneralDisplay(Observable o) {
		this.observable = o;
		o.addObserver(this);
	}


	@Override
	public void update(Observable o, Object arg) {
		WeatherData wd = (WeatherData) o;
		System.out.println(wd.getHumid()+","+wd.getPressure());
	}

}

class WeatherData extends Observable{
	private float temperature;
	private float humid;
	private float pressure;

	public WeatherData(float temp,float humid,float pressure) {
		this.temperature = temp;
		this.humid = humid;
		this.pressure = pressure;
	}

	public void initiat(){
		this.temperature /= 2;
		this.humid *=2;
		this.pressure += 1;
		setChanged();
//		notifyObservers();
	}

	public float getTemperature() {
		return temperature;
	}

	public float getHumid() {
		return humid;
	}

	public float getPressure() {
		return pressure;
	}

}

public class Console {
	public static void main(String[] args) {
		WeatherData wd = new WeatherData(12.2f, 12.4f, 24.2f);
		GeneralDisplay g = new GeneralDisplay(wd);
		wd.initiat();
	}
}
```

### 装饰器模式
IO使用的装饰器模式![UML-IO-Decorator][UML-IO-Decorator]

```Java
abstract class Beverage{  
	String description;  
	Double cost;  
	public void setDescription(String description) {  
		this.description = description;  
	}  
	public void setCost(Double cost) {  
		this.cost = cost;  
	}  
	public double getCost(){  
		return cost;  
	}  
}  

class HouseBlend extends Beverage{  
	public HouseBlend() {  
		cost = 123d;  
	}  
}  

class Espresso extends Beverage{  
	public Espresso(){  
		cost = 234d;  
	}  
}  

abstract class CondimentDecorator extends Beverage{	  
}  

class Mocha extends CondimentDecorator{  
	Beverage beverage;  
	public Mocha(Beverage beverage) {  
		this.beverage = beverage;  
	}  
	@Override  
	public double getCost() {  
		return 123d + beverage.getCost();  
	}  
}  

class Whip extends CondimentDecorator{  
	Beverage beverage;  
	public Whip(Beverage beverage) {  
		this.beverage = beverage;  
	}  
	@Override  
	public double getCost() {  
		return 345 + beverage.getCost();  
	}  
}  

public class DecoratorApp {  
	public static void main(String[] args){  
		Beverage  beverage = new HouseBlend();  
		beverage = new Mocha(beverage);  
		beverage = new Whip(beverage);  
		System.out.println(beverage.getCost());  
	}  
}  

```

### 迭代器模式
对于同一类对象，有人用了数组进行封装，有人用了ArrayList之类的东西进行分装，使用迭代器模式进行统一起来，显得更加简洁。
需要实现iterator和iterable两个接口

```Java
package test;
import java.util.Iterator;

class Element{
	int a;
	public Element(int a) {
		this.a = a;
	}
}

class Data1 implements Iterable<Element>{
	Element[] elements;

	public Data1() {
		elements = new Element[10];
		for(int i = 0; i < 10 ; i++){
			Element e = new Element(i);
			elements[i] = e;
		}
	}

	@Override
	public Iterator<Element> iterator() {
		//这个地方可以剥出去写成一个内部类可能好一点把？
		Iterator<Element> iterator = new Iterator<Element>() {
			int position = 0;
			@Override
			public boolean hasNext() {
				return position < elements.length;
			}
			@Override
			public Element next() {
				return elements[position++];
			}
			//通常remove()方法默认是抛出异常的，如果你确认说支持remove方法的话再自行覆盖
		};
		return iterator;
	}
}  

public class IterAPP {  
	public static void main(String[] args) {  
		Data1 data = new Data1();  
		for(Element e : data){  
			System.out.println(e.a);  
		}
		Iterator<Element> i = data.iterator();  
		while(i.hasNext()){  
			System.out.println(i.next().a);  
		}  
	}  
}  
```

### 组合模式（如何构造一棵树,使用iterator打印一棵树）

```Java
// sth. is wrong. tocheck.
package test;

import java.util.ArrayList;
import java.util.Iterator;

abstract class Node{
	abstract void print();
}

class Leaf extends Node{
	int a;
	public Leaf(int a) {
		this.a = a;
	}

	@Override
	void print() {
		System.out.println(a);
	}
}

class Nonleaf extends Node{
	ArrayList<Node> childs;
	public Nonleaf() {
		// construct a tree
		childs = new ArrayList<>();
		childs.add(new Leaf(1));
		childs.add(new Nonleaf(new int[]{22,23}));
	}  
	public Nonleaf(int[] as){  
		childs = new ArrayList<>();  
		for(int a: as){  
			Node leaf = new Leaf(a);  
			childs.add(leaf);  
		}  
	}  
	@Override  
	void print() {  
		Iterator<Node> iterator = childs.iterator();  
		while(iterator.hasNext()){  
			iterator.next().print();  
		}  
	}  
}  

public class TreeAPP {  
	public static void main(String[] args){  
		// use node
		Node nonleaf = new Nonleaf();  
		nonleaf.print();  
	}  
}  

```

### 状态模式
假设有三种状态A,B,C,三种状态之间可能可以通过f1,f2进行转换。现在有一向量机，使用该模式利用扩展。  
**回忆一下，其实跟策略模式是一个东西。只是应用的对象不同，没有切换的说法而已。**

```Java
package test;
// 将状态设置成一个接口，每个接口都包括f1,f2等方法
interface State {
	void f1();
	void f2();
}
// 三个状态都实现了State接口，每个接口中实现具体的状态转换逻辑
class A implements State{
	Machine m;
	public A(Machine m){
		this.m = m;
	}

	@Override
	public void f1() {
		// 状态转换
		m.setState(m.b);  
	}  

	@Override  
	public void f2() {  
	}  
}

class B implements State{  

	Machine m;  
	public B(Machine m){
		this.m = m;
	}

	@Override
	public void f1() {
		m.setState(m.a);
	}

	@Override
	public void f2() {
		m.setState(m.c);
	}

}

class C implements State{
	// .../
}

public class Machine implements State{
	// 包含三种状态，以及一个当前状态
	State a;
	State b;
	State c;

	State s;

	public Machine(){  
		a = new A(this);  
		b = new B(this);  
		c = new C(this);  
		// initialize  
		s = a;  
	}  

	public static void main(String[] args){  
		Machine m = new Machine();  
		m.f1();  
		m.f2();  
	}  

	@Override
	public void f1() {  
		//直接调用当前状态的对应方法  
		s.f1();  
		System.out.println(s);  
	}  

	@Override
	public void f2() {  
		s.f2();  
		System.out.println(s);  
	}  

	public void setState(State s){  
		this.s = s;  
	}  
}  
```

### 代理模式
包括RMI，RPC之类的东西，RPC会另外有一文负责描述。  
用户获取在另外一台jvm上面的运行的某个对象。  
![RMI-intro][RMI-intro]

```Java
package test;
import java.rmi.Remote;
import java.rmi.RemoteException;
// 这里必须用一个接口，因为客户端调用的时候使用一个interface进行类型转换。
public interface myRemote extends Remote{
	// 需要变量和返回值都是可序列化的，另外，一定要加上throws RemoteException 的签名  
	public String sayHello() throws RemoteException;
}

//---------Server------------
import java.net.MalformedURLException;
import java.rmi.Naming;
import java.rmi.RemoteException;
import java.rmi.registry.LocateRegistry;
import java.rmi.server.UnicastRemoteObject;

public class RMIServer extends UnicastRemoteObject implements myRemote{

	protected RMIServer() throws RemoteException {
		super();
	}

	private static final long serialVersionUID = 1L;

	// 跑这段代码之前需要运行 rmiregistry 进程， 需要cd到工程的 /bin, /build/, /build/classes 目录下面运行
	public static void main(String[] args) {
		try {
			LocateRegistry.createRegistry(2020);
			RMIServer service = new RMIServer();
			Naming.rebind("sayhi", service);
		} catch (RemoteException e) {
			e.printStackTrace();
		} catch (MalformedURLException e) {
			e.printStackTrace();
		}
	}
	@Override
	public String sayHello() throws RemoteException {
		return "Hello world";
	}
}

//--------Client---------
import java.net.MalformedURLException;  
import java.rmi.Naming;  
import java.rmi.NotBoundException;  
import java.rmi.RemoteException;  

public class RMIclient {  
	public static void main(String[] args) throws MalformedURLException, RemoteException, NotBoundException{  
		// 这里用接口进行cast，有可能客户端上面并没有这个类，可以通过动态类加载(dynamic class downlaoding )的方法用HTTP Get的方法获得。
		myRemote service = (myRemote) Naming.lookup("rmi://127.0.0.1/sayhi");  
		service.sayHello();  
		System.out.println(service.sayHello());
	}
}
```

```Java
// 设置一个具有权限控制的代理 -- 保护代理

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// 这里必须是接口，否则会抛出castexception
interface Person{
	public String getFirstName();
	public int getAge();
}

class Personimpl implements Person{
	String firstName;
	int age;

	public Personimpl(String firstName,int age) {
		this.firstName  = firstName;
		this.age = age;
	}

	@Override
	public String getFirstName() {
		return firstName;
	}

	@Override
	public int getAge() {
		return age;
	}

}

class PersonInvokeHandler implements InvocationHandler{

	Person p;

	public PersonInvokeHandler(Person p) {
		this.p = p;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		// 允许调用某些接口，设置权限
		if(method.getName().equals("getFirstName")){
			return method.invoke(p, args);
		}else if(method.getName().equals("getAge")){
			throw new Exception("unknown method");
//			return method.invoke(p, args);
		}
		throw new Exception("unknown method");
	}

}


public class ProxyApp {
	public static void main(String[] args){
		Person one = new Personimpl("cdmaok", 99);
		one = getProxy(one);
		System.out.println(one.getFirstName());
	}

	public static Person getProxy(Person p){
		Object o  = Proxy.newProxyInstance(p.getClass().getClassLoader(), p.getClass().getInterfaces(),new PersonInvokeHandler(p));
		// 使用接口类型做转化
		return (Person) o;
	}
}

```
TBC：  
桥接模式：不同的电视有不同的遥控器实现  
生成器：封装一个类的生成过程(ClassBuilder)  
责任链：让一个以上的对象有机会处理某一个请求  
![chain-of-res][chain-of-res]  
蝇量：让某个类的实例提供许多虚拟实例  
![fly-weight][fly-weight]  
解释器： 简单的来说，就是一个语法分析器  
中介者： 解耦合多个对象之间的复杂逻辑  
备忘录： 提供存储的功能，可以将对象回退到上一个状态  
原型(Protype Pattern): 创建实例的代价很高，使用这个模式进行clone  
访问者,类似于sql parser的东西。



**不要过度使用设计模式**
From Head First Design Pattern
[UML-stragety]:http://7xpv97.com1.z0.glb.clouddn.com/uml-strategy.png
[UML-Observer]:http://7xpv97.com1.z0.glb.clouddn.com/uml-observer.png
[UML-IO-Decorator]:http://7xpv97.com1.z0.glb.clouddn.com/uml-io-decorator.png
[RMI-intro]:xx
[chain-of-res]:xx
[fly-weight]:xx
