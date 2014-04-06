---
layout: post
title: "Java 8 简明教程(10): Annotations"
date: 2014-04-06 16:10:57 +0800
comments: true
categories: 
- Java 8
---

### Annotations ###

Java 8 中 annotations 是可重复的。我们通过下面这里例子来看一下。

<!--more-->

首先，我们定义一个包装器annotation，其中存放着真正的annotations的数组：

{% codeblock lang:java %}
	@interface Hints {
	    Hint[] value();
	}
	
	@Repeatable(Hints.class)
	@interface Hint {
	    String value();
	}
{% endcodeblock %}

Java 8 中我们可以使用添加`@Repeatable`来使用多个同种类型的注解。

变体1：使用容器annotation（过去的方法）

{% codeblock lang:java %}
	@Hints({@Hint("hint1"), @Hint("hint2")})
	class Person {}
{% endcodeblock %}

变体2：使用可重复的annotations（新式方法）

{% codeblock lang:java %}
	@Hint("hint1")
	@Hint("hint2")
	class Person {}
{% endcodeblock %}

使用变体2时，java编译器隐式的使用了`@Hints`注解。这在通过反射获取注解信息时显得尤为重要。

{% codeblock lang:java %}
	Hint hint = Person.class.getAnnotation(Hint.class);
	System.out.println(hint);                   // null
	
	Hints hints1 = Person.class.getAnnotation(Hints.class);
	System.out.println(hints1.value().length);  // 2
	
	Hint[] hints2 = Person.class.getAnnotationsByType(Hint.class);
	System.out.println(hints2.length);          // 2
{% endcodeblock %}

尽管我们在`Person`类上从未声明过`@Hints`，但是仍然可以通过`getAnnotation(Hints.class)`访问到它。然而，还有一个更方便的方法`getAnnotationsByType`，通过它能直接获取到所有标注为`@Hint`的注解。

另外，Java 8 中的annotations还扩展了两个新的target：

{% codeblock lang:java %}	
	@Target({ElementType.TYPE_PARAMETER, ElementType.TYPE_USE})
	@interface MyAnnotation {}
{% endcodeblock %}