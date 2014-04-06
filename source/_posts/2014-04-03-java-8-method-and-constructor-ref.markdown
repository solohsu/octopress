---
layout: post
title: "Java 8 简明教程(4): method and constructor references"
date: 2014-04-03 16:46:42 +0800
comments: true
categories: 
- Java 8
---

###方法和构造器的引用

上个教程中的例子可以通过使用静态方法的引用来进一步简化：

<!--more-->

{% codeblock lang:java %}

	Converter<String, Integer> converter = Integer::valueOf;
	Integer converted = converter.convert("123");
	System.out.println(converted);   // 123

{% endcodeblock %}

Java 8 中可以使用`::`关键字来传递对方法或构造器的引用。上面的例子展示了如何引用一个静态方法。同样我们也可以引用对象中的方法:

{% codeblock lang:java %}

	class Something {
	    String startsWith(String s) {
	        return String.valueOf(s.charAt(0));
	    }
	}
	Something something = new Something();
	Converter<String, String> converter = something::startsWith;
	String converted = converter.convert("Java");
	System.out.println(converted);    // "J"

{% endcodeblock %}


下面来看如何使用`::`关键字来引用构造器。首先我们定义了一个拥有多个不同构造器的bean。

{% codeblock lang:java %}
	class Person {
	    String firstName;
	    String lastName;
	
	    Person() {}
	
	    Person(String firstName, String lastName) {
	        this.firstName = firstName;
	        this.lastName = lastName;
	    }
	}

{% endcodeblock %}

接下来定义一个person类的工厂接口来创建person实例。

{% codeblock lang:java %}

	interface PersonFactory<P extends Person> {
	    P create(String firstName, String lastName);
	}

{% endcodeblock %}
	
通过使用构造器的引用我们就可以避免手动去实现上面的工厂接口，代码简洁了很多。

{% codeblock lang:java %}

	PersonFactory<Person> personFactory = Person::new;
	Person person = personFactory.create("Peter", "Parker");

{% endcodeblock %}

我们使用`Person::new`创建了一个对`Person`类的构造器的引用。Java编译器会通过匹配`PersonFactory.create`方法的签名自动选用合适的构造器。