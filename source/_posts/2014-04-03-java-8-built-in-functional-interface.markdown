---
layout: post
title: "Java 8 简明教程(6): built-in functional interfaces"
date: 2014-04-03 20:06:22 +0800
comments: true
categories: 
- Java 8
---

###内置的函数式接口

JDK 1.8 的API中包含很多内置的函数式接口。它们中有些从旧版本就开始被人们所熟知，如`Comparator`，`Runnable`。这些现存的接口通过添加`@FunctionalInterface`注解进行扩展，以提供对lambda的支持。

<!--more-->

Java 8的API同样拥有大量的函数式接口。许多接口都来自著名的Google Guava library。就算你对这个库不熟悉，你也应该去研究研究这些接口是如何进行的方法扩展。

####Predicates

Predicates是一些接受一个参数并返回boolean类型值的函数。这个接口包含各种用来组成复杂的逻辑术语的谓词的默认方法（and，or，negate）。

{% codeblock lang:java %}

	Predicate<String> predicate = (s) -> s.length() > 0;
	
	predicate.test("foo");              // true
	predicate.negate().test("foo");     // false
	
	Predicate<Boolean> nonNull = Objects::nonNull;
	Predicate<Boolean> isNull = Objects::isNull;
	
	Predicate<String> isEmpty = String::isEmpty;
	Predicate<String> isNotEmpty = isEmpty.negate();

{% endcodeblock %}

####Functions

Functions接受一个参数，并产生一个结果。默认方法可以用来将多个Functions链接到一起（compose，andThen）。

{% codeblock lang:java %}

	Function<String, Integer> toInteger = Integer::valueOf;
	Function<String, String> backToString = toInteger.andThen(String::valueOf);
	
	backToString.apply("123");     // "123"

{% endcodeblock %}

####Suppliers

Suppliers产生一个给定的泛型结果。与Functions不同的是，Suppliers不接受任何参数。

{% codeblock lang:java %}

	Supplier<Person> personSupplier = Person::new;
	personSupplier.get();   // new Person

{% endcodeblock %}

####Consumers

Consumers表示在一个输入参数上进行的操作。

{% codeblock lang:java %}

	Consumer<Person> greeter = (p) -> System.out.println("Hello, " + p.firstName);
	greeter.accept(new Person("Luke", "Skywalker"));

{% endcodeblock %}

####Comparators

Comparators在旧版本中就很有名了。Java 8又向这个接口中添加了许多默认方法。

{% codeblock lang:java %}

	Comparator<Person> comparator = (p1, p2) -> p1.firstName.compareTo(p2.firstName);
	
	Person p1 = new Person("John", "Doe");
	Person p2 = new Person("Alice", "Wonderland");
	
	comparator.compare(p1, p2);             // > 0
	comparator.reversed().compare(p1, p2);  // < 0

{% endcodeblock %}

####Optionals

Optionals不是函数式接口, 而是一个用来防止`NullPointerException`的小工具。Optional是下一节中的重要概念，这里我们先大体看一下它是如何工作的。

Optional是一个值的简单容器，这个值可以为null，也可以不为null。想象有一个可以返回非null的结果但有时什么都不返回的方法。在Java 8中你可以返回一个Optional而不返回null。

{% codeblock lang:java %}

	Optional<String> optional = Optional.of("bam");
	
	optional.isPresent();           // true
	optional.get();                 // "bam"
	optional.orElse("fallback");    // "bam"
	
	optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"


{% endcodeblock %}