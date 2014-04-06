---
layout: post
title: "Java 8 简明教程(9): Date API"
date: 2014-04-06 00:42:44 +0800
comments: true
categories: 
- Java 8
---

### Date API ###

在Java 8的java.time包中包含了一组全新的date和time相关的API。新的Date API与Joda-Time库很类似，但是并不完全一样。在下面的示例中将看到新API中的最重要的部分是如何使用的。

<!--more-->

#### Clock ####

`Clock`用来获取当前的date和time。`Clock`可以区分不同时区，也可以用来代替`System.currentTimeMillis()`来获取当前的毫秒数。作为一个时间线上的一点，当前的毫秒数也可以使用`Instant`类来表示。`Instant`可以被用来创建遗留的`java.util.Date`对象。

{% codeblock lang:java %}
	Clock clock = Clock.systemDefaultZone();
	long millis = clock.millis();
	
	Instant instant = clock.instant();
	Date legacyDate = Date.from(instant);   // legacy java.util.Date
{% endcodeblock %}

#### Timezones ####

时区使用一个`ZoneId`对象来表示，可以使用`ZoneId`的静态工厂方法很方便的获取一个时区对象。时区实际上就是定义了一个偏移量，这个偏移量在进行instant与本地的date、time进行转换时很重要。

{% codeblock lang:java %}
	System.out.println(ZoneId.getAvailableZoneIds());
	// prints all available timezone ids
	
	ZoneId zone1 = ZoneId.of("Europe/Berlin");
	ZoneId zone2 = ZoneId.of("Brazil/East");
	System.out.println(zone1.getRules());
	System.out.println(zone2.getRules());
	
	// ZoneRules[currentStandardOffset=+01:00]
	// ZoneRules[currentStandardOffset=-03:00]
{% endcodeblock %}

#### LocalTime ####

`LocalTime`表示一个无时区的时间，例如 10pm 或者 17:30:15。下面的例子中分别为上面定义的两个timezone创建了一个localtime，然后对两个时间进行比较，并计算小时和分钟的差值。

{% codeblock lang:java %}
	LocalTime now1 = LocalTime.now(zone1);
	LocalTime now2 = LocalTime.now(zone2);
	
	System.out.println(now1.isBefore(now2));  // false
	
	long hoursBetween = ChronoUnit.HOURS.between(now1, now2);
	long minutesBetween = ChronoUnit.MINUTES.between(now1, now2);
	
	System.out.println(hoursBetween);       // -3
	System.out.println(minutesBetween);     // -239
{% endcodeblock %}

`LocalTime`自带了各种工厂方法来简化实例的创建，其中就包括解析时间字符串。

{% codeblock lang:java %}
	LocalTime late = LocalTime.of(23, 59, 59);
	System.out.println(late);       // 23:59:59
	
	DateTimeFormatter germanFormatter =
	    DateTimeFormatter
	        .ofLocalizedTime(FormatStyle.SHORT)
	        .withLocale(Locale.GERMAN);
	
	LocalTime leetTime = LocalTime.parse("13:37", germanFormatter);
	System.out.println(leetTime);   // 13:37
{% endcodeblock %}

#### LocalDate ####

`LocalDate`表示一个明确的日期。它是不可变的，工作方式与`LocalTime`类似。下面的例子演示了如何通过加/减天数、月数、年数来计算新的日期。需要注意的是每次操作返回的都是一个新的实例。

{% codeblock lang:java %}
	LocalDate today = LocalDate.now();
	LocalDate tomorrow = today.plus(1, ChronoUnit.DAYS);
	LocalDate yesterday = tomorrow.minusDays(2);
	
	LocalDate independenceDay = LocalDate.of(2014, Month.JULY, 4);
	DayOfWeek dayOfWeek = independenceDay.getDayOfWeek();
	System.out.println(dayOfWeek);    // FRIDAY
{% endcodeblock %}

同样可以将字符串解析成一个LocalDate对象：

{% codeblock lang:java %}
	DateTimeFormatter germanFormatter =
	    DateTimeFormatter
	        .ofLocalizedDate(FormatStyle.MEDIUM)
	        .withLocale(Locale.GERMAN);

	LocalDate xmas = LocalDate.parse("24.12.2014", germanFormatter);
	System.out.println(xmas);   // 2014-12-24
{% endcodeblock %}

#### LocalDateTime ####

`LocalDateTime`表示一个date-time。它将date和time放到一个对象中。`LocalDateTime`是不可变的，工作方式和`LocalTime`,`LocalDate`也类似。我们可以利用方法来获取date-time中的各个字段：

{% codeblock lang:java %}
	LocalDateTime sylvester = LocalDateTime.of(2014, Month.DECEMBER, 31, 23, 59, 59);
	
	DayOfWeek dayOfWeek = sylvester.getDayOfWeek();
	System.out.println(dayOfWeek);      // WEDNESDAY
	
	Month month = sylvester.getMonth();
	System.out.println(month);          // DECEMBER
	
	long minuteOfDay = sylvester.getLong(ChronoField.MINUTE_OF_DAY);
	System.out.println(minuteOfDay);    // 1439
{% endcodeblock %}

给定一个时区信息的话可以将它转换成一个instant对象。然后可以将`Instant`对象转换成遗留的时间对象`java.util.Date`。

{% codeblock lang:java %}
	Instant instant = sylvester
	        .atZone(ZoneId.systemDefault())
	        .toInstant();
	
	Date legacyDate = Date.from(instant);
	System.out.println(legacyDate);     // Wed Dec 31 23:59:59 CET 2014
{% endcodeblock %}

date-time的格式化与date、time的格式化类似。除了使用预定义的格式之外，我们还可以使用自定义的pattern创建新的formatter对象：

{% codeblock lang:java %}
	DateTimeFormatter formatter =
	    DateTimeFormatter
	        .ofPattern("MMM dd, yyyy - HH:mm");
	
	LocalDateTime parsed = LocalDateTime.parse("Nov 03, 2014 - 07:13", formatter);
	String string = formatter.format(parsed);
	System.out.println(string);     // Nov 03, 2014 - 07:13
{% endcodeblock %}

与`java.text.NumberFormat`不同的是，新创建的`DateTimeFormatter`对象是不可更改并且线程安全的。

关于pattern语法的详细信息参看[官方文档](http://download.java.net/jdk8/docs/api/java/time/format/DateTimeFormatter.html)