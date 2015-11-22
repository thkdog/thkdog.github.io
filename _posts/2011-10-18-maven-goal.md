---
layout: post
title:  "执行maven [goal]命令的含义"
date:   2011-10-18
categories: package
---
goal phase lifecycle这几个概念的区别我不赘述了，我前一篇博文便转载了，网上也大把大把的资料。

这篇文章说的是，例如执行 mvn archetype:generate 此类执行goal语句，冒号的两边分别代表什么含义。

它对应的含义是 mvn goal-prefix:goal

解释一下什么是goal-prefix，大家执行一下 mvn help:describe -Dplugin=archetype 相信就一目了然了

这条命令的输出：

{% highlight text %}
Name: Maven Archetype Plugin
Description: Maven Archetype is a set of tools to deal with archetypes, i.e.
  an abstract representation of a kind of project that can be instantiated into
  a concrete customized Maven project. An archetype knows which files will be
  part of the instantiated project and which properties to fill to properly
  customize the project.
Group Id: org.apache.maven.plugins
Artifact Id: maven-archetype-plugin
Version: 2.1
Goal Prefix: archetype
This plugin has 8 goals:
[具体内容省略]
{% endhighlight %}

可以很清楚的看到，一个plugin对应了一个goal prefix，这个prefix就是上述的goal-prefix，可以看做是plugin artifectId的简称。

这条命令也会清楚的列出这个plugin所有的goal，如果需要更加详细的信息，如这个plugin中每个goal的具体用法，参数，以及绑定的phase，可以执行

mvn help:describe -Dplugin=archetype -Ddetail