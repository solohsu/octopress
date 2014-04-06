---
layout: post
title: "Java 8 简明教程(1): default method"
date: 2014-04-01 23:56:29 +0800
comments: true
categories: 
- Java 8
---

###接口的默认方法

Java 8 中我们可以利用 `default` 关键字来为接口添加非抽象方法。这个特性也被称为**扩展方法**。

<!--more-->

例子如下：

{% codeblock lang:java %}

	interface Formula {
    	double calculate(int a);
	
    	default double sqrt(int a) {
        	return Math.sqrt(a);
    	}
	}

{% endcodeblock %}

除了`calculate`方法这个抽象方法之外，`Formula`接口还定义了一个默认方法`sqrt`，接口的实现类只需要实现抽象方法`calculate`，而默认方法`sqrt`是直接可以拿来用的。

{% codeblock lang:java %}

	Formula formula = new Formula() {
    	@Override
    	public double calculate(int a) {
        	return sqrt(a * 100);
    	}
	};

	formula.calculate(100);     // 100.0
	formula.sqrt(16);           // 4.0

{% endcodeblock %}

上面的`formula`对象使用匿名对象的方式实现了`Formula`接口。代码看起来比较啰嗦，用了6行代码实现了一个简单的计算`sqrt(a * 100)`。在后面的教程中，我们将会看到在Java 8 中如何使用更简单的方式来实现一个单方法对象。