---
layout: post
title: "Java 8 简明教程(2): lambda expression"
date: 2014-04-02 15:34:54 +0800
comments: true
categories: 
- Java 8
---

###Lambda 表达式

先来看一个对字符串数组进行排序的例子，在之前的Java版本中，我们通常这样来实现：

<!--more-->

{% codeblock lang:java %}

	List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

	Collections.sort(names, new Comparator<String>() {
    	@Override
    	public int compare(String a, String b) {
        	return b.compareTo(a);
    }
});

{% endcodeblock %}

静态方法`Collections.sort`接受一个数组和一个比较器来对这个数组进行排序。因而我们经常需要创建一个匿名的比较器并把它传递给`sort`方法。


为了避免整天没完没了的创建匿名对象，Java 8使用了一种更简洁的语法，lambda表达式：


{% codeblock lang:java %}

	Collections.sort(names, (String a, String b) -> {
    	return b.compareTo(a);
});

{% endcodeblock %}

可以看到代码比以前短了不少并且变得更加易读。但是它还能变得更短：

{% codeblock lang:java %}

	Collections.sort(names, (String a, String b) -> b.compareTo(a));

{% endcodeblock %}

如果方法中只有一行语句，可以省略大括号和`return`关键字。但是，这还没完：

{% codeblock lang:java %}

	Collections.sort(names, (a, b) -> b.compareTo(a));

{% endcodeblock %}


Java编译器会自动进行参数类型检查，所以这些我们也可以省略掉。接下来我们将更深入的讲解一下lambda表达式更广泛的用途。