---
layout: post
title: "Java 8 简明教程(5): lambda scope" 
date: 2014-04-03 17:21:13 +0800
comments: true
categories: 
- Java 8
---

###Lambda的作用域

在lambda表达式中访问外部变量的方式与匿名对象相似。从lambda表达式中可以访问外部作用域中的final局部变量，外部对象的成员字段和静态变量。

<!--more-->

####访问局部变量

从lambda表达式中访问外部的final局部变量：

{% codeblock lang:java %}

	final int num = 1;
	Converter<Integer, String> stringConverter =
	        (from) -> String.valueOf(from + num);
	
	stringConverter.convert(2);     // 3

{% endcodeblock %}

但是与匿名对象不同的是，`num`不必显式的声明为final变量。所以下面的代码也是正确的：

{% codeblock lang:java %}

	int num = 1;
	Converter<Integer, String> stringConverter =
	        (from) -> String.valueOf(from + num);
	
	stringConverter.convert(2);     // 3

{% endcodeblock %}

但是为了代码能成功编译，`num`必须隐含为final变量。像下面的代码就无法编译通过：

{% codeblock lang:java %}

	int num = 1;
	Converter<Integer, String> stringConverter =
	        (from) -> String.valueOf(from + num);
	num = 3;

{% endcodeblock %}

在lambda表达式里对`num`进行写操作也是被禁止的。

####访问字段和静态变量

与访问局部变量时相反，在lambda表达式内我们可以对外部对象的成员字段和静态变量进行读写操作。这个特性在匿名对象中已经广为人知了。

{% codeblock lang:java %}

	class Lambda4 {
	    static int outerStaticNum;
	    int outerNum;
	
	    void testScopes() {
	        Converter<Integer, String> stringConverter1 = (from) -> {
	            outerNum = 23;
	            return String.valueOf(from);
	        };
	
	        Converter<Integer, String> stringConverter2 = (from) -> {
	            outerStaticNum = 72;
	            return String.valueOf(from);
	        };
	    }
	}

{% endcodeblock %}

####访问默认接口方法

还记得教程(1)中的formula的例子吗？`Formula`接口定义了一个默认的方法`sqrt`，任何一个formula的实例都可以访问这个方法，包括匿名对象在内。但在lambda表达式中这个就不适用了。

在lambda表达式内是无法访问默认方法的。下面的代码无法通过编译：

{% codeblock lang:java %}

	Formula formula = (a) -> sqrt( a * 100);

{% endcodeblock %}
