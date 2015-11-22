---
layout: post
title:  "suckfish and superfish——dropdown风格导航栏制作"
date:   2013-11-04
categories: html5
---
虽然现在比较流行扁平化的导航栏，但dropdown在很多场景下依然必不可少，但是手动实现要支持全平台（IE8/9/10, Safari, Chrome，Android…）的导航栏还是要煞费苦心。

在alistapart上有一个非常著名的dropdown风格导航栏制作，名suckfish，这是一个手工实现的原理性解释，以及一些css hack，它提供了一种非常通用的dropdown风格导航范式。

当然如果我们自己实现，更希望是一个组件，灵活的组件，因此就诞生了superfish。它基于jquery实现，使用基于suckfish的设计方法，并仅只用一行代码使导航生效，并可自定义css样式修改其默认风格，相当好用。（本人测试其支持IE8+，在IE7下面下拉菜单宽度不正常）

{% highlight javascript %}
    jQuery(document).ready(function() {
        jQuery('ul.sf-menu').superfish();
    });
{% endhighlight %}

具体使用和css修改方式在这里不作赘述，请直接阅读官方文档。