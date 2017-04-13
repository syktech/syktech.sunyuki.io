---
layout: post
title: Java程序设计
tags: java
author: zengyilun
---

# Java程序设计环境

Jre(java runtime environment): 是一个虚拟机，类似.net的clr，里面有Java内存模型，基于自动内存托管堆，集成了各种分代GC回收算法，以及类子节码解析器以及各种JIT等等；

<!-- more -->

Jdk(java development kit): 包括Jre，并且提供了Java代码到类子节码文件的编译器，以及线程/内存/虚拟机的诊断工具等等；

SWING/AWT: Java桌面端程序开发组件，可以理解为.net的WPF一套的技术，这套技术没有WPF普及，这个原因还是因为客户机基本都是windows系统，.net和windows集成更好都是一家公司，另外WPF的表现力更好；

Applet: 可以运行在网页上面的Java小程序，可以理解为.net的Silverlight那套的技术，由于Flash以及Html5，该技术宿命也和SL一样走向了衰亡；

Jdk 的安装：

下载jdk [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

安装

如果是使用的压缩包的安装方式， 需要配置环境变量JAVA_HOME=/jdk/path/, PATH=.;%PATH%;%JAVA_HOME%/bin;

win+R -> cmd 输入java

# Java基本的程序设计

## Hello world

![hello]({{ site.assets }}/img/hello.png)

保存为HelloWorld.java    
编译javac HelloWorld.java  -- 生成 HelloWorld.class  
运行java HelloWorld  
打印输出  
Hello, World  

## 数据类型

#### 基础数据类型：

int/short/long/byte/float/double/char/boolean/pointer

int 4 bytes =  2 ^ 8 ^ 4 / 2 ~ 2 ^ 8 ^ 4 / 2 - 1 = 2 ^ 32 / 2 ~ 0 ~ 2 ^ 32 / 2 - 1 = 2,147,483, 647

|类型	|大小	|范围	|备注|
|:-----:|:-----:|:-----:|:-----:|
|int	|4 bytes|-2,147,483,648 to 2,147,483, 647 (just over 2 billion)|The wrapper type is Integer. Use BigInteger for arbitrary precision integers|
|short	|2 bytes|	-32,768 to 32,767||
|long	|8 bytes|	-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807	|Literals end with L (e.g. 1L)|
|byte|	1 byte|	-128 to 127	|Note that the range is not 0 ... 255|
|float|	4 bytes|	approximately -3.40282347E+38F (6-7 significant decimal digits)|	Literals end with F (e.g. 0.5F)|
|double|	8 bytes|	approximately -1.79769313486231570E+308 (15 significant decimal digits)	|Use BigDecimal for arbitrary precision floating-point numbers|
|char|	2 bytes|	\u0000 to \uFFFF|	The wrapper type is Character. Unicode characters > U+FFFF require two char values|
|boolean||		true or false	|

java 中没有无符号类型的数值类型。

#### 类型转换 ：

*虚线代表精度丢失*。  
![type-conversion]({{ site.assets }}/img/type-conversion.png)

#### 包装数据类型：

Integer/Long/Short/Byte/Float/Double/Character/Number

	Integer a = 5; -> Integer a = new Integer(5);
	int b = Integer.parseInt("5"); -> Integer b = Integer.parseInt("5");

包装类可以为空，包装类有一些工具可以将其他类型的变量转换。 

#### 原子数据类型：

AtomicInteger/AtomicLong/AtomicBoolean 线程安全版的基础包装数据类型，实现方式并非同步锁，机器语言级别的实现方式，比同步锁有更高的效率。

比如AtomicInteger 的 .incrementAndGet() 可以安全的自增长 而省去了 同步。

#### 特殊数据类型：

BigDecimal/BigInteger

	double d = 29.0 * 0.01;
	System.out.println(d);
	System.out.println((int) (d * 100));
	输出:
		0.29
		28

	double a = 0.2 + 0.4;
    System.out.println(a);
	输出：
		0.6000000000000001

IEEE 754

IEEE二进制浮点数算术标准（IEEE 754）是20世纪80年代以来最广泛使用的浮点数运算标准，为许多CPU与浮点运算器所采用。

![float]({{ site.assets }}/img/float.gif)

符号位 指数 尾数

BigDecimal 比float, double 有更大的精度（无限精度）.
BigInterger 当整数超过Long大小时(2^64/2-1)，可以用这个。

如浮点类型一样， BigDecimal 也有一些令人奇怪的行为。尤其在使用 equals() 方法来检测数值之间是否相等时要小心。 equals() 方法认为，两个表示同一个数但换算值不同（例如， 100.00 和 100.000 ）的 BigDecimal 值是不相等的。然而， compareTo() 方法会认为这两个数是相等的，所以在从数值上比较两个 BigDecimal 值时，应该使用 compareTo() 而不是 equals() 。

Java中的浮点数参考文献：[https://www.ibm.com/developerworks/cn/java/j-jtp0114/](https://www.ibm.com/developerworks/cn/java/j-jtp0114/)


## 命名规范

类 - 首字母大小驼峰  class CamelCase{}     
接口 - 与类相同 interface USBInterface {}  
变量 - 首字母小写驼峰  int camelCase = 10    
常量 - 全大写，下划线分割 public static final String CONSTANT_STRING = "VALUE"    
方法 - 首字母小写驼峰 void doSomething(){}  
包 - 全小写，公司域名倒写 com.sunyuki.Test   

官方文档 [http://www.oracle.com/technetwork/java/codeconventions-135099.html](http://www.oracle.com/technetwork/java/codeconventions-135099.html)


## 变量与常量

变量:

	double salary;
	int vocationDays;
	long earthPopluation;

	salary = 20;

	int middle；
	for(int i = 0;i < 10; i++){}

常量:

	final double cannotChange;

数组：
	int a[] = {1, 2, 3};
	int[] a = new int[3];
	String[] b = new String[]{"a", "b", "c"};
	
JAVA可以将变量的声明放任意地方。 

## 运算符（Operator)
	
基础运算符 \+ - \* / % =  
条件运算符 && ||   
位运算符 & | ^ ~ >> << >>> <<< 
自增运算符 ++, --  
三元运算符 ?:  
关系运算符 == != > <= < 
类型比较运算符 instanceof
   
数学函数与常量
+ Math.sin
+ Math.cos
+ Math.tan
+ Math.atan
+ Math.log
+ Math.log10

+ Math.PI
+ Math.E

优先级：  

+ [] . () (method call)	Left to right	  
+ ! ~ ++ -- + (unary) - (unary) () (cast) new     
+ * / %		  
+ << >> >>>	  
+ < <= > >= instanceof	  
+ == !=	  
+ &	  
+ ^	        
+ |	      
+ &&	 	    
+ ||	    
+ ?:	   
+ = += -= *= /= %= &= |= ^= <<= >>= >>>=	
  
## 字符串

字符: 'A'   转ascii: （int)'A'  
字符串: String a = "hello, world"  
字符串拼接: "A" + "B"  

字符串的一些方法:

+ charAt()  
+ length()
+ substring(0, length) 左闭右开
+ startsWith()
+ endsWith()
+ indexOf()
+ concat()
+ replace()
+ replaceAll() 正则
+ trim()
+ toUpperCase();


String 是只读的，String 类的方法都是创建了一个新的String, 并没有改变原来的。

	String upper = "test".toUpperCase();

再比如

	String s = "abcd";
	s = s.concat("ef");

![readonly]({{ site.assets }}/img/string-readonly.jpeg)

#### ==和equals的区别

	String a = "test";
	String b = "test";
	String c = new String("test");
	a == b -> true
	b == c -> false
	a.equals(b) -> true
	b.equals(c) -> true

== ： 测试内存地址相等
.equals : 逻辑相等

a和b都是从常量池中取的数据， 而new String是新分配一个内存地址

	// These two have the same value
	new String("test").equals("test") // --> true 
	
	// ... but they are not the same object
	new String("test") == "test" // --> false 
	
	// ... neither are these
	new String("test") == new String("test") // --> false 
	
	// ... but these are because literals are interned by 
	// the compiler and thus refer to the same object
	"test" == "test" // --> true 
	
	String#equals:
	@Override
	public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }



equals 可以使用equals方法检测两个字符串是否相等。

#### StringBuffer和StringBuilder

普通拼接

	String str = "";
    str += "abc"; -> new StringBuider().append("abc");
    System.out.println(str);

循环时

	String res = "";
	for(int i = 0; i < fields.length; i++){
		res += fields[i];  -> res = new StringBuilder().append(fields[i]);
	}
	return res;

	StringBuilder sb = new StringBuilder();
	for(int i = 0; i < fields.length; i++){
		sb.append(fields[i]);
	}
	return sb.toString();

StringBuilder 中不正确的用法
	
	new StringBuilder().append("a" + "b"); -> new StringBuilder(new StringBuilder("a").append("b").toString());

在循环中， 使用StringBuilder 而不是字符串的拼接更有效率、更节省内存。

StringBuffer 是 StringBuilder 的线程安全版。

String的Format(这里可以顺便提一下Java的可变参数)

格式化输出：
	
	String str = String.format("there is  only %s %d minutes", "last", 10);
	System.out.format("there is  only %s %d minutes", "last", 10)
	System.out.printf("there is  only %s %d minutes", "last", 10)

格式化说明符  
  
常见转换符 d、x、o、f、s、c、b、h（哈希）、%、n（平台独立换行符）

没有ld、lld、lf 这些。

Java函数可变参数：

	public void printf(String format, String... specification){
		String arg0 = specification[0];
		...
		System.out.print(arg0);
	}



正则表达式:

	String[] words = str.split("\\s+"); 切割
    ---
	String newWords = str.replaceAll("[0-9]+","#"); 替换
	--
	Pattern pattern = Pattern.compile("[0-9]+");
	Matcher matcher = pattern.matcher(str); 
	while (matcher.find()) { 
	 process(str.substring(matcher.start(), matcher.end())); 
	} 获取
    ---
	Pattern pattern = Pattern.compile("[0-9]+(\\w)");
	Matcher matcher = pattern.matcher(str); 
	while (matcher.find()) { 
	 process(str.group(1)); 
	} 获取（组）
	

## 控制流程

#### 块作用域

一对花括号就是一个块，块决定了变量的作用域。

局部变量与全局变量：

	public static void main(String args[]){
		int n;
		..
		{
			int b;
			int k;
		}
	}

Java 中局部变量不允许覆盖全局变量！

#### 默认值

	public class Test{
		int	a; //默认值0
		boolean c;//false
		char d; //null

		public void test(){
			int b;//是一个垃圾数，但Java强制要求必须初始化，不必担心。
			System.out.println(b); // -- 报错， 因为没有初始化
		}
	}



#### 条件语句

	if(a >= b){
		..
	}else if(a >= c){
		..
	}else{
		..
	}

    switch(choice){
		case 1:
			..
			break;
		case 2:
			..
			break;
		default:
			//other input
			break;
	}
#### 循环

do while/while do/for/foreach

	int i = 0;
	while(i < 10){
	...
	i++
	}

	do{
		...
		i++;
	}while(i < 10);

	for(int i = 0;i < 10;i++){
		...
	}
	
	String[] strs = new String()[];
	
	List list = new ArrayList(); 
	for(Object item : list){ -> for each 其实是 使用了实现了Iterable接口的类的iterator方法。
	}

	Iterator it = list.iterator();
	while(it.hasNext){
		process(it.next());
	}

#### 中断循环
	
	while(it.hasNext()){
		if(it.next() == accuary){
			...
			break;
		}
	}

	while(it.hasNext()){
		if(it.next() == badResult){
			...
			countinue;
		}
		...
	}
	
	A:
	while(it.hasNext()){
		...
		for(int i = 0;i < size;i++){
			if(isFind){
				break A;	
			}
		}
	}





## 类与对象

#### 类

	public class A {
		private int i; -- 成员变量(member field)
		private static int j; -- 静态成员变量
		public static final CONSTANT_A = "HELLO" -- 类常量
		public static void main(){String[] args[]){} -- 类函数、静态函数
		public A(){} -- 默认构造函数，如果没有任何构造函数 就有这个
		public A(int i){ -- 带参数的构造函数
			this.i = i;
		}
		class B{
			private int x;		
			...
		}
	}

#### 接口与抽象类

	public interface A {
		int a = 0; -> public final int a;   -- 接口只能是常量 
		void method1(); -> public abstract void method1(); -- 默认就是抽象方法
	}
	public abstract class A {
		public abstract void method1();
	}

接口与抽象类并不能实例化。

#### 类的实例化
	
	A a = new A();

#### 继承已有一个类

	public class B(){

		public B(){
			System.out.println("B 默认构造函数");
		}

		public B(String b){
			System.out.println("b 带参构造函数");
		}
	}

	class A extends B {
		
		public A(){
			//super(); -- 如果没写， 将默认添加
		}

		public A(String a){
			//super(); -- 如果没写， 将默认添加
			super(a); -- 只能调用其中一个超类的构造函数
			System.out.println(a + " 带参构造函数");
		}
	}

	A a = new A();
	A a2 = new A("a");

	打印结果：
		B 默认构造函数
		b 带参构造函数
		a 带参构造函数

#### Object 类 - 所有类的超类

	class Employee -> class Employee extends Object

Object 类的方法

	class Object{
		
		public native int hashCode();
		public final native Class<?> getClass();
		public boolean equals(Object obj) { return (this == obj);}
		public String toString() {
        	return getClass().getName() + "@" + Integer.toHexString(hashCode());
    	}

	}

**equals 方法**

用于检测一个对象是否等于另一个对象。

**hashCode 方法**

散列码。由对象导出的整型值。

+ 如果两个对象相等(equal)，那么他们一定有相同的哈希值。
+ 如果两个对象的哈希值相同，但他们未必相等(equal)。

**toString 方法**

对象序列化为字符串。默认为 类名+@+哈希值

方法覆盖与方法重载

	public class B(){ public void method1(){} }
	public class A(){

		//覆盖
		@Override
		public void method1(){}

		//重载
		public void method1(String a){}
	}

子类构造函数始终要调用父类构造函数。

#### 实现抽象类和接口
	
	public class A extends B {} -- 继承抽象类
	public class A implements B {} -- 实现接口

#### 初始化块/静态初始化块

	public class A{
		
		{
			System.out.println("初始化块");
		}

		static{
			System.out.println("静态初始化块");
		}

		public A(){
			System.out.println("构造函数");
		}
		
		..
	}

	打印结果：
		静态初始化块
		初始化块
		构造函数


#### final在Java语法中的各种作用

	final public class A{ -- 不能被继承
		public static final String FIELD_NAME = "HELLO"; --不能被改变
		
		public final void method1(){
			final int a = 0; --不能被改变
		}
	}

#### Java包

Java允许使用包将类组织起来，类似于命名空间。

类的导入，

	java.util.Date today = new java.util.Date();

显然这很麻烦，可以使用import语句导入一个特定的类或者完成的包：

	import java.util.Date;

	import some.package.*;


#### Java中各种访问级别区别(default/private/public/protected)

![access-level]({{ site.assets }}/img/access-level.png)


#### 内部类

	public class A{
		private int i = 0;
		public class B{
			public void test(){
				int i = A.this.i; --使用外部类的实例的成员变量
				...
			}
		}
	}

#### 静态内部类
	
	public class A{
		private int i = 0;
		public static class B{
			public void test(){
				
			}
		}
	}

#### 内部类的实例化
	
	A a = new A();
	A.B b = a.new B();

	A.B ab = new A.B();

#### 匿名内部类

	A.B ab = a.new B(){
		
		@Override
		public void test(){
			supper.test();
		}

	}

#### C# 中委托在JAVA中的实现方法 - Deletegate, Callback，Hook

	public class A{
		public interface Listener{
			void onClick();
		}

		public void invoked(Listener listener){
			...
			listener.onClick();
			...
		}
	}

	A a = new A();
	a.invoked(new Listener(){

		@Override
		public void onClick(){
			System.out.println("onClick");
		}

	});

#### 枚举类型

	public enum WEEK{
		MONDAY, TUESDAY
	} 

	public enum WEEK{
		MONDAY("MON"), TUESDAY("TUE")

		private String value;

		public WEEK(String value){
			this.value = value;
		}

		public String getValue(){
			return this.value
		}
	}

枚举类型其实是 继承于Enum的类。

	public enum WEEK{
		MONDAY, TUESDAY
	} 
	
	类似于 -- （当然，并不允许直接继承Enum)

	public class WEEK extends Enum{
		public static final Enum MONDAY = new Enum("MONDAY", 0);
		public static final Enum TUESDAY = new Enum("TUESDAY", 1);

		Enum(String name, int ordinal) {
        	super(name, ordinal);
    	}
	}
		

## 常用的包与类

	java.applet	Applets (Java programs that run inside a web page)  
	java.awt	Graphics and graphical user interfaces  
	java.beans	Support for JavaBeans components (classes wi th properties and event listeners)  
	java.io	Input and output  
	java.lang	Language support 
	java.math	Arbitrary-precision numbers
	java.net	Networking
	java.nio	"New" (memory-mapped) I/O
	java.rmi	Remote method invocations
	java.security	Security support
	java.sql	Database support
	java.text	Internationalized formatting of text and numbers
	java.time	Dates, time, duration, time zones, etc.
	java.util	Utilities (including data structures, concurrency, regular expressions, and logging)

## 反射

*这里简单介绍一下如何利用反射动态创建对象，并写利用反射动态获取私有域，静态域，方法调用等*

	package com.sunyuki;
	public class A {
		private String a = "a";
		public static final String B = "B";
		
		public void method1(){ System.out.println("method1"); }

		public String setA(String a){ return this.a = a; }
	}

	Class aClass = Class.forName("com.sunyuki.A");
	A a = (A)aClass.newInstance();
	a.setA("aaa");
	Fields[] fs = a.getDeclaredFields();
	for(Field f : fs){
		f.setAccessible(true);
		Object o = f.get(a);
		System.out.println(o);
	}

    Method method1 = aClass.getMethod("method1");
    System.out.println(method1.invoke(a, null));
	
	打印出：
		aaa
		B
		method1
	
## 异常处理

异常

![exceptions]({{ site.assets }}/img/exception.jpeg)

	Throwable
	\--				
		\-- Exception 

						\-- RuntimeException

异常 -- 如果遇到了无法处理的情况，那么Java的方法可以抛出一个异常，例如删除一个文件，文件不存在，试图处理删除文件的代码会抛出IOException异常。

Java中异常分为两类 -- 声明式异常和运行时异常，顾名思义，声明式异常需要在代码中显式的处理（抛出或捕获），而运行时异常需要程序在运行时才会知道。

声明异常
	
	public method1 throws IOException {
		//delete files...
		new File("c:\\abc.txt").delete();
	}

运行异常
	
	继承RuntimeException就不需要在方法上显式声明。（如NullPointerException)

异常处理(异常捕获)

		BufferedReader br = null;
		try{
			br = new BufferdReader(..);
			...
		}catch(Excpetion e){
			...
		}finally{
			...
			if(br != null){
				br.close();
			}
		}

自定义异常
	
	public class ApiException extends Exception{
		
		public ApiException(){}
		public ApiException(String message){
			super(message);
		}

	}

	public class ApiRuntimeException extends RuntimeException{
			
		public ApiException(){}
		public ApiException(String message){
			super(message);
		}
	}

抛出异常 - 处理异常 完整流程

	Class Biz{
		private Dao dao;
		
		public void save(Model model) throws ApiException(){
			if(dao.findExisted(model.getId())){
				throw new ApiException("已存在");
			}
			dao.insert(model);
		}
	}

	Class FrontEndController{

		public ResultModel save(Model model){
			ResultModel res = new ResultModel();
			res.setSuccess(true);
			res.setMessage("成功");

			try{
				biz.save(model);
			}catch(ApiException e){
				logger.error(e);
				res.setSuccess(false);
				res.setMessage(e.getMessage());
			}

			return res;
		}
	}

## 集合

数据结构：hash/array/tree/link

	Collection
		\_ Set					\_ List						
			\_HashSet \_TreeSet		\_LinkedList \_ArrayList
 
#### List 接口 (队列 - 先进先出）。

ArrayList - 内部由固定数组实现，所以请的内存是连续的，遍历这个集合就很快，如果添加元素后，元素个数大于数组大小，就会新申请一个更大的数组，新增就特别慢，删除是用后一个元素覆盖前一个元素。
	
	Object o = new Object();
	List list = new ArrayList();
	list.add(o);
	for(int i = 0;i < list.size();i++){
		proccess(list.get(i));
	}
	list.remove(i-1);

LinkedList - 链表，由于是用一个一个的不连续的内存 通过前后指针链接起来的，遍历这个集合或者获取其中某一个值就很慢，但是删除和新增特别快，因为只需要把前一个节点的指针指向后一个节点的指针即可。
	
	Object o = new Object();
	List list = new LinkedList();
	list.add(o);
	for(int i = 0;i < list.size();i++){
		process(list.get(i));
	}
	list.remove(i-1);

#### Set 集(不在意顺序（没有下标），不允许有相同的元素（如何判断是不是相同：调用equals方法...）)
	
HashSet 无序的
	
	Set set = new HashSet();
	set.add(new Object());
	for(Object o : set){
		process(o);			
	}

	Iterator iterator = set.iterator();
	while(iterator.hasNext()){
		process(set.next());
	}

TreeSet 有序的

	Set set = new TreeSet(new Comparator() {
        public int compare(Object o1, Object o2) {
            return ((A)o1).getA() - ((A)o2).getA();
        }
    });
	
	set.add(new Object());
	for(Object o : set){
		process(o);			
	}

	Iterator iterator = set.iterator();
	while(iterator.hasNext()){
		process(set.next());
	}

#### Comparable 与 Comparator

Comparable 

	class Student implements Comparable<Student>{
		private String number;
		private String name;
	
		@Override public int compareTo(Student stu){
			return number.compareTo(stu.number);  
		}
	} 

什么时候用Comparable与Comparator, Comparator只是作为临时的比较策略， 最好使用Comparable，这样的话不论在哪里使用到了排序，都可以不用再写Comparator，不过已经封装好的类，没有实现Comparable接口，这时可以使用Comparator。


集合类型：list/set/map

	Map
		\_HashMap \_TreeMap

Map（字典，图）
	
HashMap 无序， 同一个键 只能有一个值

  	Map map = new HashMap();
    Set keySet = map.keySet();
    for(Object key : keySet){
        Object value = map.get(key);
    }
    map.put("key", "value");

TreeMap 有序

	Map map = new TreeMap(new Comparator() {
        public int compare(Object o1, Object o2) {
            return ((A)o1).getA() - ((A)o2).getA();
        }
    });
 	Set keySet = map.keySet();
    for(Object key : keySet){
        Object value = map.get(key);
    }
    map.put("key", "value");

collection 与 collections 的区别

![coll]({{ site.assets }}/img/collections.jpeg)

线程安全与不安全

ArrayList - Vector

Stack 先进后出

HashMap - HashTable

Iterator - Enumeration

*基于这3个维度来介绍Java中不同集合*

## 泛型

如果应用泛型

*泛型 - 旨在减少错误和增加可读性*

	List<String> list = new ArrayList<String>();
	for(String str : list){
		process(str);
	}

	List list = new ArrayList();
	for(Object o : list){
		process((String)o);
	}

定义泛型
	
	public class Pair<T>{
		private T first;
		public Pair(T first, T second){}
		public T getFirst(){return first};
	}

泛型方法

	public class Pair{
		public static <T> T getMiddle(T... a){
			return a[a.length / 2];
			}
		}
	} 

	String middle = Pair.getMiddle("a", "b", "c")

如果应用通配符

	public static void print(Pair<? extends Employee> pair){
        
    }

	public static void print(Pair<? super Employee> pair){
        
    }

	public static void print(Pair<?> pair){

    }


为什么说Java相对于C#是假泛型

Java 中的泛型在编译后实际上还是使用的强制转换。

## 多线程

定义线程与运行

	public class ThreadA implements Runnable{  -- 或者继承Thread
	
	    public void run() {
	        while(true) {
	            System.out.println("run a");
	            try {
	                Thread.sleep(1000);
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            }
	        }
	    }
	}

	public class Main {

	    public static void main(String[] args) {
	        ThreadA threadA = new ThreadA();
	        new Thread(threadA).start();
	    }

	}

中断线程  - 线程中断后 只是给线程一个警告，并不一定要结束线程。  
Thread.currentThread().intercept();  
 
线程中断测试  
Thread.intercepted(); -- 会清除中断状态(Thread.sleep也会清除中断)  
Thread.currentThread().isIntercepted(); -- 不会清除中断状态  

线程状态

6大状态

+ New 新建
+ Runnable 可运行
+ Blocked 被阻塞
+ Waiting 等待
+ Time waiting 计时等待
+ Terminated 被终止

`new Thread` ，这时他的状态为 *新建*， 还没有运行， 当然还有一些准备工作要做。  
`.start()`， 线程处于 *可运行* 状态。 一个在 *可运行* 状态的线程也可能还没运行，这取决于操作系统给线程提供运行的时间。  
当线程处于*被阻塞* 或者 *等待*，它不运行任何代码且占用最少的资源，等待线程调度器重新激活它。

+ 当线程试图获取一个内部的对象锁，而该锁由其他线程使用，则该线程处于*被阻塞*状态，当其他线程释放锁，并且线程调度器允许本线程持有他的时候，该线程变成非阻塞状态

+ 当线程等待另一个线程通知线程调度器一个条件时，它自己进入*等待*状态。在Java中调用`Object.wait`或者`Object.join`方法，或是等待`java.util.concurrent`库中的Lock或Condition时，就会进入这种情况。*等待*和*被阻塞*是不同的。

+ 有几个方法有一个超时参数，调用他们导致线程进入`计时等待`状态。这个状态将一直保存到超时期满或者接收到适当的通知。带有超时参数的方法有Thread.sleep或Object.wait，Thread.join,Lock.tryLock以及Condition.await的计时版。
 
状态的转换 - 当一个线程被阻塞或等待（或终止），另一个线程被调度为运行状态。当一个线程被重新激活（例如超时期满或者成功获得了一个锁），调度器检查是否有比当前运行线程更高的优先级，如果是这样，调度器从当前运行线程中挑选一个，剥夺其运行权，选择一个新的线程运行。

被终止的线程

线程有两个原因死亡

+ run方法正常退出而死亡
+ 因为一个没有捕获的异常终止了run方法而意外死亡

特别是，可以调用线程的stop方法，该方法抛出TreadDeath错误对象，由此杀死线程。但是这个方法已过时。


![thread]({{ site.assets }}/img/thread.png)

Thread

+ void join() 等待终止指定的线程
+ void join(long millis) 等待指定的线程死亡或者经过的毫秒数
+ Thread.State getState() (6大状态之一）
+ void stop() **已过时的方法**，停止线程
+ void suspend() 暂停线程的运行（挂起），**已过时**。
+ void resume() 恢复挂起的线程，**已过时**。

线程优先级

通过`.setPriority`来设置线程的优先级。

+ void setPriorty(int newPriorty) 设置优先级
+ static int MIN_PRIORITY 最小优先级1
+ static int MAX_PRIORITY 最大优先级10
+ static int NORM_PRIORITY 默认优先级5
+ static void yield() 使当前线程处于让步状态，如果有其他可运行线程与此线程有同样高的优先级，那么另外一个线程会先调度。 

用户线程与守护线程

t.setDaemon(true)

守护线程的唯一用途就是为其他线程服务。当只剩下守护线程时，他就退出了。守护线程应该永远不去访问固有资源，如文件，数据库，因为可能随时在操作的时候中断。

线程异常处理

线程run()方法是不运行抛出申明式异常的，如果有RuntimeException或Error抛出，不必使用try catch 捕获，可以使用Thread.UncaughtExceptionHandler来处理线程结束前没有捕获到的异常
	
	public class ThreadB extends Thread implements Thread.UncaughtExceptionHandler {

    public void run() {
        while (true) {
			System.out.println("rush b");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            this.stop(); //这个方法已经过时，不要使用。这里只是抛出异常的作用。
        }
    }}

	ThreadB threadB = new ThreadB();
    threadB.start();
    threadB.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
        public void uncaughtException(Thread t, Throwable e) {
            e.printStackTrace();
            //log.error("thread exit");
        }
    });

	输出
		rush b
		java.lang.ThreadDeath
			at java.lang.Thread.stop(Thread.java:850)
			at course.thread.ThreadB.run(ThreadB.java:17)

	
也可以使用Thread.setDefaultUncaughtExceptionHandler来为所有线程指定线程异常处理器。

线程安全（同步）

	public class Ticket {
    public static int total = 100;

    static class Employee extends Thread {

        @Override
        public void run() {
            while (true) {
                try {
                    if (total > 0) {
                        Thread.sleep(10);
                        total -= 1;
                        System.out.printf("%s sold %d, remain %d %n", Thread.currentThread().getName(), 1, total);
                    } else {
                        return;
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

    }

    public static void main(String[] args) {
	
	        for (int i = 0; i < 10; i++) {
	            new Employee().start();
	        }
	    }
	}

卖票程序.

线程安全的一些基本概念：

> 线程的安全性的定义：当多个线程访问某个类时，这个类都始终表现出正确的行为，那么这个类就被认为是线程安全的。  

>无状态对象：既不包含任何域，也不包含任何对其他类中域的引用（也就是不共享变量）。无状态对象一定是线程安全的。


>原子性：不可分割。

>竞争（竞态）条件：由于不恰当的执行顺序导致不正确的结果。

竞争条件的一些情况
+ 先检查后执行
+ 读取-修改-写入

在无状态的类中添加一个状态，该状态由线程安全的对象来管理，那么这个类仍然是线程安全的。

线程池

	ExecutorService pool = Executors.newCachedThreadPool();
    Future<Integer> res = pool.submit(new Callable<Integer>() {
            public Integer call() throws Exception {
                return 0;
            }
        });
	
    pool.shutdown();

线程之间通信

wait, notify、管道

Callable与Runnable区别

	 	FutureTask<Integer> futureTask = new FutureTask<Integer>(new Callable<Integer>() {
	                    public Integer call() throws Exception {
	                        return 0;
	                    }
        });
	    new Thread(futureTask).start();
		futureTask.get();

Callable 与 Runnable的区别就是，Callable可以获得一个返回值。通过Future#get方法来获取（此方法是一个阻塞方法）。

Synchronized与ReentrantLock区别

`ReetrantLock` 显式锁，可以直接获取条件对象 lock.newConditon()。通过Condition的await()和signal()/signalAll()方法来使线程处于释放锁并处于等待状态， 解除等待状态。比 Synchronized 更多的用处。 

`Synchronized` 每个对象都内置了一个隐式锁， 通过Object的wait()和notify()/notifiyAll() 来添加到条件的等待集， 解除等待状态。 更简单，	可直接放在方法上，避免出错。但相比显式锁更有局限性，不能中断一个正在试图获取锁的进程（阻塞状态），试图获取锁不能设定超时，每个锁只有一个条件，可能不够。

volatile有什么用

保证共享变量的可见性(立即刷新到主内存)，有序性(防止重排序)，但不能保证原子性。

同步器：信号量/倒计时门栓/栅栏/交换器/同步队列

#### 信号量

	public class SThread {
	
	    public static void main(String[] args) {
	        ExecutorService service = Executors.newCachedThreadPool();
	        final Semaphore semp = new Semaphore(5);
	        for(int i = 0;i < 20;i++){
	            final  int NO = i;
	            service.execute(new Runnable() {
	                @Override
	                public void run() {
	                    try {
	                        semp.acquire();
	                        System.out.println("access :"  + NO);
	                        semp.release();
	                        System.out.println("--------" + semp.availablePermits());
	                    } catch (InterruptedException e) {
	                        e.printStackTrace();
	                    }
	                }
	            });
	        }
	        service.shutdown();
	    }
	}

#### 倒计时门栓

	public class CThread {
    public static void main(String[] args) {
        final CountDownLatch latch = new CountDownLatch(2);

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    System.out.println("子线程" + Thread.currentThread().getName() + "正在执行");
                    Thread.sleep(3000);
                    System.out.println("子线程" + Thread.currentThread().getName() + "执行完毕");
                    latch.countDown();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    System.out.println("子线程" + Thread.currentThread().getName() + "正在执行122222222");
                    Thread.sleep(3000);
                    System.out.println("子线程" + Thread.currentThread().getName() + "执行完毕");
                    latch.countDown();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        try {
            System.out.println("等待2个子线程执行完毕...");
            latch.await();
            System.out.println("2个子线程已经执行完毕");
            System.out.println("继续执行主线程");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
	}

#### 循环栅栏(回环屏障)

	public class BThread {
	
	    public static void main(String[] args) {
	        int N = 4;
	        CyclicBarrier barrier  = new CyclicBarrier(N, new Runnable() {
	            @Override
	            public void run() {
	                System.out.println("do something");
	            }
	        });
		
	        for(int i=0;i<N;i++)
	            new Writer(barrier).start();
	    }
	
	    static class Writer extends Thread{
	        private CyclicBarrier cyclicBarrier;
	        public Writer(CyclicBarrier cyclicBarrier) {
	            this.cyclicBarrier = cyclicBarrier;
	        }
	
	        @Override
	        public void run() {
	            System.out.println("线程"+Thread.currentThread().getName()+"正在写入数据...");
	            try {
	                Thread.sleep(5000);      //以睡眠来模拟写入数据操作
	                System.out.println("线程"+Thread.currentThread().getName()+"写入数据完毕，等待其他线程写入完毕");
	                cyclicBarrier.await();
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            }catch(BrokenBarrierException e){
	                e.printStackTrace();
	            }
	            System.out.println("所有线程写入完毕，继续处理其他任务...");
	        }
	    }
	}

#### 阻塞队列

**BlockingQueue** 是 java.util.concurrent 包中 放入和拿出形式的线程安全的队列。

|	    |Throws Exception	|Special Value	|Blocks	|Times Out|
|:------|:-----:|:-----:|:--------:|:-------:|
|Insert	|add(o)	|offer(o)|	put(o)|	offer(o, timeout, timeunit)|
|Remove	|remove(o)	|poll()|	take()|	poll(timeout, timeunit)|
|Examine	|element()|	peek()	 	| 

1. Throws Exception: 
If the attempted operation is not possible immediately, an exception is thrown.
2. Special Value: 
If the attempted operation is not possible immediately, a special value is returned (often true / false).
3. Blocks: 
If the attempted operation is not possible immedidately, the method call blocks until it is.
4. Times Out: 
If the attempted operation is not possible immedidately, the method call blocks until it is, but waits no longer than the given timeout. Returns a special value telling whether the operation succeeded or not (typically true / false).

阻塞队列的实现：

+ ArrayBlockingQueue  固定容量的队列
+ DelayQueue 阻塞直到延迟过期
+ LinkedBlockingQueue 动态容量队列
+ PriorityBlockingQueue 优先级容量队列 - 元素需实现Comparable， 但不一定是按优先级排序的，但第一个一定是优先级最高的元素 
+ SynchronousQueue 同步队列， 没有容量（或者说只能存一个）， 只能拿一个和取一个

![blockingQueue]({{ site.assets }}/img/blocking-queue.png)

	public class BlockingQueueExample {
	
	    public static void main(String[] args) throws Exception {
	
	        BlockingQueue queue = new ArrayBlockingQueue(1024);
	
	        Producer producer = new Producer(queue);
	        Consumer consumer = new Consumer(queue);
	
	        new Thread(producer).start();
	        new Thread(consumer).start();
	
	        Thread.sleep(4000);
	    }
	}

	public class Producer implements Runnable{

	    protected BlockingQueue queue = null;
	
	    public Producer(BlockingQueue queue) {
	        this.queue = queue;
	    }
	
	    public void run() {
	        try {
	            queue.put("1");
	            Thread.sleep(1000);
	            queue.put("2");
	            Thread.sleep(1000);
	            queue.put("3");
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	    }
	}

	public class Consumer implements Runnable{
	
	    protected BlockingQueue queue = null;
	
	    public Consumer(BlockingQueue queue) {
	        this.queue = queue;
	    }
	
	    public void run() {
	        try {
	            System.out.println(queue.take());
	            System.out.println(queue.take());
	            System.out.println(queue.take());
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	    }
	}

#### 交换器


![exchanger]({{ site.assets }}/img/exchanger.png)

	Exchanger exchanger = new Exchanger();

	ExchangerRunnable exchangerRunnable1 =
	        new ExchangerRunnable(exchanger, "A");
	
	ExchangerRunnable exchangerRunnable2 =
	        new ExchangerRunnable(exchanger, "B");
	
	new Thread(exchangerRunnable1).start();
	new Thread(exchangerRunnable2).start();


	public class ExchangerRunnable implements Runnable{

	    Exchanger exchanger = null;
	    Object    object    = null;
	
	    public ExchangerRunnable(Exchanger exchanger, Object object) {
	        this.exchanger = exchanger;
	        this.object = object;
	    }
	
	    public void run() {
	        try {
	            Object previous = this.object;
	
	            this.object = this.exchanger.exchange(this.object);
	
	            System.out.println(
	                    Thread.currentThread().getName() +
	                    " exchanged " + previous + " for " + this.object
	            );
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	    }
	}

#### ConcurrentMap

The java.util.concurrent.ConcurrentMap interface represents a Map which is capable of handling concurrent access (puts and gets) to it.

The ConcurrentMap has a few extra atomic methods in addition to the methods it inherits from its superinterface, java.util.Map.

使用 ConcurrentMap 效率更更高，而不是HashTable

	ConcurrentMap concurrentMap = new ConcurrentHashMap();

	concurrentMap.put("key", "value");

	Object value = concurrentMap.get("key");

java并发工具参考: [http://tutorials.jenkov.com/java-util-concurrent/index.html](http://tutorials.jenkov.com/java-util-concurrent/index.html)

## Git使用

SCM GIT 

获取帮助 - git help <verb>、 git <verb> --help、 man git-<verb>

与SVN的不同 

+ 分布式版本控制软件, 每个人的电脑上都保存了一个项目的所有版本副本，而SVN一旦远程服务器出错，版本数据将会丢失。
+ SVN的版本控制使用的是保存每一个版本的差异，而Git使用的是保存快照，所以Git切换版本特别快，但是使用了更多的空间。
+ Git切换分支很快，因为仅仅新建了一个hashcode，将HEAD指向了这个hashcode。

Hello World

	git init
	git add .
	git commit -m "first-commit"
	git log

git init 

初始化项目 - 在当前目录会出现一个隐藏文件夹.git，所有git的资源都放在这个目录下。这个操作仅仅创建了一些元数据，还没有开始跟踪项目里的任何一个文件。

git status

查看git状态

git add 

	git add .
	
	git add *.c

跟踪文件 - 添加到暂存区(index)

git commit

	git commit 
	git commit -m "message"

提交到本地版本仓库

git log

查看日志

git push

	git push -u origin master

git remote 

	git remote add origin http://192.168.2.230/zyl/git-test
	git remote -v
	git remote set-url origin http://192.168.2.230/zyl/git-test
	git remote remove origin
	git remote rename origin origin2
	 
git clone

	git clone http://192.168.2.230/zyl/git-test

git fetch

拉取元数据，并不拉取数据。

git merge

	git merge origin master	

+ 快进
+ 自动合并
+ 冲突 

git rebase

衍合

	git rebase

![]({{ site.assets }}/img/merge.jpg)

	git merge mywork origin

![]({{ site.assets }}/img/rebase0.jpg)
![]({{ site.assets }}/img/rebase.jpg)

	git rebase mywork origin

相当于git merge，但是要和git merge的区别就是git merge产生冲突时会新建一个分支但不会更改原来的分支,git rebase直接删掉自己的分支然后再新建一个分支,如果没有产生冲突和git merge一模一样。

git pull

git pull = git fetch + git merge。

	git pull origin master
	git pull (如果设置了upstream)

git reset

	git reset HEAD
	git reset --soft HEAD
	git reset --hard HEAD

git checkout
	
	git checkout new_branch
	git checkout -b new_branch
	git checkout -- .

git reflog

注意 reflog 只保存在本地

	git reflog
	git reset --hard HEAD@{1}

git diff

	git diff
	git diff HEAD^ HEAD 
	git diff ds21fashafg gdg1fshk
	git difftool --tool-help
	git config --global diff.tool bc3
	git config --global difftool.bc3.path "c:/program files/beyond compare 3/bcomp.exe"
	git config --global merge.tool bc3
	git config --global mergetool.bc3.path "c:/program files/beyond compare 3/bcomp.exe"

git config
	
	git config --list
	git config user.email xxx@xxx.com
	git config user.name xxx
	git config credential.helper store
	git config http.proxy http://127.0.0.1:8118
	git config auto.crlf true
	git config core.editor gvim
	git config alias.commit ci

gitk 
  
带界面的git

	gitk --all

.gitignore

忽略文件，例子
	
	*.c 忽略所有.c文件
	!lib.c lib.c除外
	/TODO 忽略根目录下的TODO文件夹
	doc/ 忽略doc文件夹下所有文件
	doc/*.txt 忽略doc/abc.txt但不忽略doc/server/arch.txt

git tag

	git tag v1.0
	git push origin v1.0
	
git hook 的使用。。。

## Maven使用

为什么用maven

maven - 构建工具， 使项目的构建简单化，并且有一个统一的本地仓库。

项目结构（约定优于配置的原则）

	src
		\_ src
			\_ main
				\_ java
				\_ resource

			\_ test
				\_ java
		\_ target
	pom.xml

pom文件介绍

每个项目根目录都有一个pom文件, 用于配置maven。

maven 的生命周期

+ 清理
+ 初始化
+ 编译
+ 测试
+ 打包
+ 集成测试
+ 验证
+ 部署
+ 站点生成

#### 再分为三类

+ clean 清理项目
+ default 真正构建的所有步骤  |  ref: [http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
    + validate
    + intitalize
    + generate-sources
    + process-sources
    + generate-resources
    + process-resources
    + compile
    + process-classes
    + generate-test-sources
    + process-test-sources
    + generate-test-resources
    + process-test-resources
    + test-compile
    + process-test-classes
    + test
    + prepare-package
    + package
    + pre-integration-test
    + integration-test
    + post-integration-test
    + verify
    + install 安装到本地
    + deploy 将最终的包复制到远程仓库
+ site 建立和发布项目站点，MAVEN能基于POM所包含的信息，自动生成一个友好的站点

maven常用命令: package/compile/clean/test

maven的属性

+ 内置属性 ${basedir} 项目根目录（即包含pom.xml的目录),${version} 表示项目版本
+ POM属性 引用POM文件中对应元素的值
    + ${project.build.sourceDirectory} 项目源码路径,默认为src/main/java
    + ${project.build.testSourceDirectory} 项目测试源码路径,默认为src/test/java/
    + ${project.build.directory} 项目构建输出目录，默认为target/
    + ${proect.outputDirectory} 项目主代码编译目录，默认为target/classes/
    + ${project.testOutputDirector} 项目测试代码编译输出目录,默认为target/test-classes
    + ${project.groupId} 项目的groupId
    + ${project.artifactId} 项目的artifactId
    + ${project.version} 与${version}等价
    + ${project.build.finalName} 项目打包输出文件的名称，默认为${project.artifactId}-${project.version}
+ 自定义属性，用`<properties>`元素定义的属性
+ Settings属性，如${settings.localRepositroy}
+ Java属性，所有的Java系统属性都可以使用Maven属性引用，如${user.home},可以用mvn help:system查看所有Java属性
+ 环境变量属性,如${env.JAVA_HOME}，可以用mvn help:system查看所有环境变量

**依赖**

	<dependency>
	    <groupId></groupId>
	    <artifactId></artifactId>
	    <version></version>
	    <type></type>
	    <optional></optional>
	    <scope></scope>
	    <exclusions>
	        <exclusion>
	        </exclusion>
	         ...
	    </exclusions>
	</dependency>

+ groupId、artifactId、version 基本坐标
+ type 依赖类型，对应项目坐标定义的packaging。大部分情况下，该元素不必声明，其默认值为jar
+ scope 依赖的范围
+ optional 标记依赖是否可选
+ exclusions 用来排除传递性依赖

依赖范围

+ compile 编译依赖范围。默认
+ test 测试依赖范围 junit
+ priovided 已提供依赖范围 tomcat
+ runtime 运行时依赖范围 jdbc-driver
+ system 系统依赖范围
+ import (Maven 2.0.9)导入依赖范围

传递性依赖

传递性依赖和依赖范围

依赖调解

依赖调解第一原则 路径最优者优先
1. A->B->C->X(1.0) 
2. A->D->X(2.0)

*选择2*

依赖调解第二原则 第一声明者优先(Maven 2.0.9)
1. A->B->Y(1.0)
2. A->C->Y(2.0)

如果B的依赖声明在C之前，*Y（1.0）就会被解析使用*

可选依赖
**依赖将不会被传递**，例如项目B有2个数据库驱动，设置这2个数据库驱动为可选，其他项目依赖项目B的时候就不会下载这2个数据库
`<option>true</option>`

排除依赖
如果你想排除掉某个传递性依赖，用`<exclusions>`,只需要`<groupId>`和`<artifactId>`

	<dependencies>
	</dependency>
	    <groupId></groupId>
	    <artifactId></artifactId>
	    <version></version>
	    <exclusions>
	        <exclusion>
	            <groupId></groupId>
	            <artifactId></artifactId>
	         </exclusion>
	      <exclusions>
	</dependency>
	<dependencies>

归类依赖

	<properties>
	    <springframework.verision>2.5</springframework.version>
	</properties>
	
	<dependecies>
	    <dependency>
	     <groupId>org.springframework</groupId>
	    <artifactId>spring-core</artifactId>
	    <version>${springframework.version}</version>
	    <dependency>
	</dependencies>


优化依赖
`mvn dependency:list`
`mvn dependency:tree`


maven 插件

插件目标：
	
一个插件目标就是一个功能。
如`dependency:analyze`、`dependency:tree`和`dependency:list`。

插件绑定 & 内置绑定

Maven的生命周期与插件相互绑定，用以完成实际的构建任务。
为了让用户几乎不用任何配置就能构建Maven项目，Maven的核心为一些主要的生命周期阶段绑定了很多插件的目标。  

自定义绑定

	<build>
	    <plugins>
	        <groupId>org.apache.maven.plugins</groupId>
	        <artifactId>maven-source-plugin</artifactId>
	        <version>2.1.1</version>
	        <executions>
	            <execution>
	                <id>attach-sources omg it's just a name</id>
	                <phase>verify</phase>
	                <goals>
	                    <goal>jar-no-fork</goal>
	                </goals>
	            </execution>
	        </executions>
	    </plugins>
	</build>

maven 聚合与继承

聚合

想要一次构建两个项目，而不是分别到模块的目录下执行mvn命令。Maven聚合（或者成为多模块）这一特性就是为该需求服务的。
aggregator:

	<project
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	    xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	    <modelVersion>4.0.0</modelVersion>
	    <groupId>com.sunyuki.ec</groupId>
	    <artifactId>sunyuki-erp-aggregator</artifactId>
	    <version>0.0.1-SNAPSHOT</version>
	    <packaging>pom</packaging>
	    <name>sunyuki ec erp</name>  
	    <modules>
	        <module>../sunyuki-erp-base</module>
	        <module>../sunyuki-erp-api</module>
	        <module>../sunyuki-erp-external-api</module>
	    </modules>  
	</project>

请注意`packaging`为`POM`

继承

两个POM有着许多相同的配置，例如有相同的groupId和version。在maven中，POM的继承这样的机制能让我们抽取出重复的配置。
parent:
	
	<project
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	    xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	    <modelVersion>4.0.0</modelVersion>
	    <groupId>com.sunyuki.ec</groupId>
	    <artifactId>sunyuki-erp-parent</artifactId>
	    <version>0.0.1-SNAPSHOT</version>
	    <packaging>pom</packaging>
	    <name>sunyuki ec erp</name>  
	</project>

请注意`packaging`为`POM`
child:

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	    <modelVersion>4.0.0</modelVersion>
	    <artifactId>sunyuki-erp-api</artifactId>
	    <packaging>jar</packaging>
	    <parent>
	        <groupId>com.sunyuki.ec</groupId>
	        <artifactId>sunyuki-erp-parent</artifactId>
	        <version>0.0.1-SNAPSHOT</version>
	        <relativePath>../sunyuki-erp-parent/pom.xml</relativePath>
	    </parent>  
	</project>

#### 可继承的POM元素
+ groupId
+ version
+ desciption
+ organization
+ inceptionYear
+ url
+ developers
+ contributors
+ distributionManagement
+ issueManagement
+ ciManagement
+ scm 软件配置管理（版本控制系统）
+ mailingLists
+ properties
+ dependecies
+ denpendecyManagement
+ repositroies
+ build 包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等
+ reporting

依赖管理

依赖可以继承，这时候容易想到在父类配置`<repositroies>`而子类不配置`<repositroies>`就可以继承，是可行的，但是存在问题，不需要对应库的子模块就一定要继承父类的`<repositroies>`吗?
maven提供的dependecyManagement元素能让子模块继承到父模块的依赖配置，又不会让子类引入实际的依赖。
父类：

	<?xml version="1.0" encoding="UTF-8"?>
	<project
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	    xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	    <modelVersion>4.0.0</modelVersion>
	    <groupId>com.sunyuki.ec</groupId>
	    <artifactId>sunyuki-erp-parent</artifactId>
	    <version>0.0.1-SNAPSHOT</version>
	    <packaging>pom</packaging>
	    <name>sunyuki ec erp</name>
	    <dependencyManagement>
	        <dependency>
	            <groupId>org.springframework</groupId>
	            <artifactId>spring-test</artifactId>
	            <scope>test</scope>
	            <version>1.0</version>
	        </dependency>
	    </dependencies> 
	</project>


子类也会继承到依赖管理，子类在写依赖时，version和scope都省去了，方便于统一管理
子类：

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	    <modelVersion>4.0.0</modelVersion>
	    <artifactId>sunyuki-erp-external-api</artifactId>
	    <packaging>jar</packaging>
	    <parent>
	        <groupId>com.sunyuki.ec</groupId>
	        <artifactId>sunyuki-erp-parent</artifactId>
	        <version>0.0.1-SNAPSHOT</version>
	        <relativePath>../sunyuki-erp-parent/pom.xml</relativePath>
	    </parent> 
	    <dependencies>  
	        <dependency>
	            <groupId>org.springframework</groupId>
	            <artifactId>spring-test</artifactId>
	        </dependency>
	    </dependecies>
	</project>

引入repositroyManagement的方式：
+ 复制
+ 继承
+ import - 引入`com.juvenxu.mvnbook.account.accout-parent`项目的`dependencyManagement`，`scope`为`import`,`type`为`pom`

	<dependencyManagement>
	    <dependencies> 
	        <dependency>
	            <groupId>com.juvenxu.mvnbook.account</groupId>
	            <artifactId>accout-parent</artifactId>
	            <scope>import</scope>
	            <version>1.0-SNAPSHOT</version>
	            <type>pom</type>
	        </dependency>
	    </dependencies> 
	</dependencyManagement>



## 没有了
