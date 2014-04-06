---
layout: post
title: "Java 8 简明教程(3): functional interface"
date: 2014-04-02 16:33:19 +0800
comments: true
categories: 
- Java 8
---

###函数式接口

lambda表达式是如何符合Java的类型系统的呢？每个lambda表达式对应一个指定的函数式接口。所谓的**函数式接口**(functional interface)必须包含且仅包含一个抽象方法。每个lambada表达式都会与对应的函数式接口中的这个抽象方法进行匹配。而由于default方法不是抽象的，所以我们可以向函数式接口中随意添加default方法。

<!--more-->

只要一个接口只含有一个抽象方法，我们就可以将它用作lambda表达式。我们可以使用`@FunctionalInterface`注解来确保我们的接口能够满足这一需求。当我们尝试向添加了此注解的接口中添加第二个抽象方法时，编译器会提示存在编译错误。

例子：

{% codeblock lang:java %}

	@FunctionalInterface
	interface Converter<F, T> {
	    T convert(F from);
	}
	Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
	Integer converted = converter.convert("123");
	System.out.println(converted);    // 123

{% endcodeblock %}

需要注意的是去掉`@FucntionalInterface`注解后代码依然是正确有效的。