---
layout: post
title:  "JAVA异常处理"
date:   2020-10-02 10:00:34
categories: 
tags: java,Exception
excerpt: 
---

如以下代码，在输入不正确时会报错。

```java
public class  Test1{
	public static void main(String[] args) {
		@SuppressWarnings("resource")
		Scanner sc = new Scanner(System.in);
		System.out.print("请输入一个整数: ");
		int a = sc.nextInt();
		System.out.println("输入的数字是: " + a);	
	}
}
```

比如：

```
请输入一个整数: 321.23
Exception in thread "main" java.util.InputMismatchException
	at java.util.Scanner.throwFor(Scanner.java:864)
	at java.util.Scanner.next(Scanner.java:1485)
	at java.util.Scanner.nextInt(Scanner.java:2117)
	at java.util.Scanner.nextInt(Scanner.java:2076)
	at me.yaoyan.test.testException.Test1.main(Test1.java:27)
```

### 捕获并处理异常

一般使用 try...catch...finally...来捕获并处理异常。

```java
public class Test1 {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		System.out.print("请输入一个整数: ");
		try {
			// 首先执行以下代码块
			int a = sc.nextInt();
			System.out.println("输入的数字是:  " + a);
		} catch (Exception e) {
			// 如果try代码块执行有报错，则抓住异常对象e，执行此段
			// e.printStackTrace();
			System.out.println("输入错误，程序终止");
		} finally{
			// finally 不管try是否成功，此段都终被执行。
			// 一般用于关闭资源等不管是否成功都需要做的事
			sc.close();
		}
	}
}
```

### 将异常抛至上层

一般，在当前类中考虑会出现的异常，进行处理。也可以不处理异常，要求调用该类时处理。

比如有一个类 ``` GetInt``` ，在可能出现输入异常的地方，未进行 ``` try ``` ```catch``` 处理：

```java
public class GetInt {
	int a;
	public void get(){
		System.out.println("输入： ");
		Scanner sc = new Scanner(System.in);
		this.a = sc.nextInt();
		sc.close();
	}
}
```

使用该类：

```java
public class Test2 {
	public static void main(String[] args) {
		GetInt cc = new GetInt();
		cc.get();
		System.out.println("数字是： " + cc.a);
	}
}
```

正常情况如下：

```
输入： 
123
数字是： 123
```
如果输入错误：

```
输入： 
3adfdaf
Exception in thread "main" java.util.InputMismatchException
	at java.util.Scanner.throwFor(Scanner.java:864)
	at java.util.Scanner.next(Scanner.java:1485)
	at java.util.Scanner.nextInt(Scanner.java:2117)
	at java.util.Scanner.nextInt(Scanner.java:2076)
	at me.yaoyan.test.testException.GetInt.get(GetInt.java:27)
	at me.yaoyan.test.testException.Test2.main(Test2.java:23)
```

如果在```GetInt``` 使用``` throws Exception``` 声明：

```java
public class GetInt {
	int a;
	public void get() throws Exception{
		System.out.println("输入： ");
		Scanner sc = new Scanner(System.in);
		this.a = sc.nextInt();
		sc.close();
	}
}
```

在使用有```throws```声明的方法时，则会要求处理异常。或也可以继续使用```throws```往更上层扔，直到抛给用户。



