---
layout: post
title: "Java 8 简明教程(7): streams"
date: 2014-04-04 09:43:47 +0800
comments: true
categories: 
- Java 8
---

###Streams

`java.util.Stream`表示一个多元素的数组，在上面可以进行一项或者多项操作。Stream操作分为两种，一种是intermediate操作，一种是terminal操作。terminal操作返回的是某一特定类型的结果，而intermediate操作返回的stream对象本身，这样以来我们就可以在一行中将多个方法链接起来。Streams创建的时候必须指定一个source，例如`java.util.Collection`中的lists或者sets（maps是不被支持的）。Streams操作既可以是串行的也可以是并行的。

<!--more-->

首先来看一个串行执行streams操作的例子，这个例子中的source是一个string数组：

{% codeblock lang:java %}

	List<String> stringCollection = new ArrayList<>();
	stringCollection.add("ddd2");
	stringCollection.add("aaa2");
	stringCollection.add("bbb1");
	stringCollection.add("aaa1");
	stringCollection.add("bbb3");
	stringCollection.add("ccc");
	stringCollection.add("bbb2");
	stringCollection.add("ddd1");

{% endcodeblock %}

Java 8对`Collections`进行了扩展，我们可以直接调用`Collection.stream()`或者`Collection.parallelStream()`来创建stream。下面是一些常见的stream操作。

#### Filter ####

Filter接受一个判断条件并对stream中的所有元素进行过滤操作。这是一个intermediate操作，我们可以在它的返回结果上继续调用其他的stream操作（比如这里的forEach）。ForEach接受一个「消费者」，这个「消费者」对经过过滤后的stream中的每一个元素进行「消费」。ForEach是一个terminal操作。它的返回值是void，所以我们无法再继续调用其他操作了。

{% codeblock lang:java %}

	stringCollection
	    .stream()
	    .filter((s) -> s.startsWith("a"))
	    .forEach(System.out::println);
	
	// "aaa2", "aaa1"

{% endcodeblock %}

#### Sorted ####

Sorted是一个intermediate操作，它返回经过排序后的stream。默认的排序是按照自然顺序，想要实现自定义的比较方式需要向sorted方法传递一个自定义的Comparator。

{% codeblock lang:java %}

	stringCollection
	    .stream()
	    .sorted()
	    .filter((s) -> s.startsWith("a"))
	    .forEach(System.out::println);
	
	// "aaa1", "aaa2"

{% endcodeblock %}

需要注意的是，sorted仅创建了当前stream经过排序后的一个视图，而没有真正修改后方的collection。`stringCollection`中的顺序依然保持不变：

{% codeblock lang:java %}

	System.out.println(stringCollection);
	// ddd2, aaa2, bbb1, aaa1, bbb3, ccc, bbb2, ddd1

{% endcodeblock %}

#### Map ####

map也是一个intermediate操作，它使用给定的函数将每个元素转换成另一个对象。下面的例子中将每一个字符串进行了upper操作。另外，也可以使用map将对象转换成另一种类型。得到的stream的泛型取决于传递给map的函数的返回值类型。

{% codeblock lang:java %}

	stringCollection
	    .stream()
	    .map(String::toUpperCase)
	    .sorted((a, b) -> b.compareTo(a))
	    .forEach(System.out::println);
	
	// "DDD2", "DDD1", "CCC", "BBB3", "BBB2", "AAA2", "AAA1"

{% endcodeblock %}

#### Match ####

各式各样的匹配操作可以用来检查一个特定的谓词表达式是否与stream相匹配。所有的匹配操作都是terminal类型的，并且返回的是boolean类型的结果。

{% codeblock lang:java %}

	boolean anyStartsWithA =
	    stringCollection
	        .stream()
	        .anyMatch((s) -> s.startsWith("a"));
	
	System.out.println(anyStartsWithA);      // true
	
	boolean allStartsWithA =
	    stringCollection
	        .stream()
	        .allMatch((s) -> s.startsWith("a"));
	
	System.out.println(allStartsWithA);      // false
	
	boolean noneStartsWithZ =
	    stringCollection
	        .stream()
	        .noneMatch((s) -> s.startsWith("z"));
	
	System.out.println(noneStartsWithZ);      // true

{% endcodeblock %}

#### Count ####

count是一个terminal操作，它以long类型返回stream中元素的个数。

{% codeblock lang:java %}

	long startsWithB =
	    stringCollection
	        .stream()
	        .filter((s) -> s.startsWith("b"))
	        .count();
	
	System.out.println(startsWithB);    // 3

{% endcodeblock %}

#### Reduce ####

reduce是一个terminal操作，它使用给定的函数对stream中的元素进行reduction操作。返回的是一个Optional对象，其中存放着reduce之后的值。

{% codeblock lang:java %}

	Optional<String> reduced =
	    stringCollection
	        .stream()
	        .sorted()
	        .reduce((s1, s2) -> s1 + "#" + s2);
	
	reduced.ifPresent(System.out::println);
	// "aaa1#aaa2#bbb1#bbb2#bbb3#ccc#ddd1#ddd2"

{% endcodeblock %}

### Parallel Streams ###

上面提到了streams分为串行和并行两类。串行streams中的操作都是在一个线程中执行的，而并行sterams上的操作是在多个线程中并发执行的。

下面的例子向我们展示了使用并行streams来提升性能是如此的简单。

首先创建一个没有重复元素的大数组。

{% codeblock lang:java %}

	int max = 1000000;
	List<String> values = new ArrayList<>(max);
	for (int i = 0; i < max; i++) {
	    UUID uuid = UUID.randomUUID();
	    values.add(uuid.toString());
	}

{% endcodeblock %}

接下来我们来测一下对这个数组的stream进行排序所消耗的时间。

#### 串行排序 ####

{% codeblock lang:java %}

	long t0 = System.nanoTime();
	
	long count = values.stream().sorted().count();
	System.out.println(count);
	
	long t1 = System.nanoTime();
	
	long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
	System.out.println(String.format("sequential sort took: %d ms", millis));
	
	// sequential sort took: 899 ms

{% endcodeblock %}

#### 并行排序 ####

{% codeblock lang:java %}

	long t0 = System.nanoTime();
	
	long count = values.parallelStream().sorted().count();
	System.out.println(count);
	
	long t1 = System.nanoTime();
	
	long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
	System.out.println(String.format("parallel sort took: %d ms", millis));
	
	// parallel sort took: 472 ms

{% endcodeblock %}

可以看到两段代码基本完全相同，但是并行排序并串行排序快了大约50%。差别仅是把`stream()`替换成了`parallelStream()`。