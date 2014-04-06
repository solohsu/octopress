---
layout: post
title: "使Octopress支持UTF-8"
date: 2014-04-06 16:46:35 +0800
comments: true
categories: 
- Octopress
---

按照官方教程部署好Octopress后，如果想要支持中文blog的书写，需要修改一下几个地方：

### 环境变量 ###

如果在Windows操作系统下，需要添加两个环境变量：

+ LANG = zh_CN.UTF-8
+ LC_ALL = zh_CN.UTF-8

### Jekyll的源文件 ###

修改`{ruby_home}\lib\ruby\gems\1.9.1\gems\jekyll-0.12.0\lib\jekyll\convertible.rb`：

{% codeblock lang:diff convertible.rb %}

- self.content = File.read(File.join(base, name))
+ self.content = File.read(File.join(base, name), :encoding => 'utf-8')

{% endcodeblock %}

另外，还记录了一些别的修改的地方。

### 提交时间修改 ###

运行`rake deploy`时，在Rakefile中可以找到默认的提交信息是「Site updated at #{Time.now.utc}」，这里的时间使UTC时间，将`Time.now.utc`改为`Time.now`可以改为本地时间。

{% codeblock lang:diff convertible.rb %}

- self.content = File.read(File.join(base, name))
+ self.content = File.read(File.join(base, name), :encoding => 'utf-8')

{% endcodeblock %}

### JiaThis插件 ###

### codeblock插件 ###

现在[官方Github](https://github.com/imathis/octopress)的master分支下为Octopress2.0版本，暂不支持codeblock插件中的start, mark, and linenos选项，可以参考这两个临时解决方法：

- [https://github.com/imathis/octopress/issues/1472](https://github.com/imathis/octopress/issues/1472)
- [https://github.com/imathis/octopress/pull/1485](https://github.com/imathis/octopress/pull/1485)