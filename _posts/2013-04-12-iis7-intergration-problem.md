---
layout: post
title:  "IIS7集成模式初始化Spring.NET容器（Request is not available in this context exception in Application_Start问题）"
date:   2013-04-12
categories: asp.net
---

一般Spring容器是在执行第一个请求的时候触发的，但我碰到个需求，必须在应用程序启动的时候就要能够初始化Spring上下文。换句话说，我在Application_Start时就要能够执行

{% highlight csharp %}
ContextRegistry.GetContext()
{% endhighlight %}

我使用了SignalR框架，为了和Spring一起协同工作，我需要使SignalR内置的IoC容器与Spring.NET容器协同工作（SignalR容器同时能够获取Spring容器中的对象，这不是本文重点）。

说一下本文出处解决的思路是将初始化工作延迟到第一次请求再执行（包括MVC注册路由等）在global.asax中添加如下私有类：

{% highlight csharp %}
private class FirstRequestInitialization
{
    private static bool s_InitializedAlready = false;
    private static Object s_lock = new Object();
    // Initialize only on the first request
    public static void Initialize(HttpContext context)
    {
        if (s_InitializedAlready)
        {
            return;
        }
        lock (s_lock)
        {
            if (s_InitializedAlready)
            {
                return;
            }
//这里的内容就是原本要放在Application_Start中做的事情
            DependencyResolver.SetResolver(new SpringMvcDependencyResolver(ContextRegistry.GetContext()));
            AreaRegistration.RegisterAllAreas();
            RegisterGlobalFilters(GlobalFilters.Filters);
            RegisterRoutes(RouteTable.Routes);
            s_InitializedAlready = true;
        }
    }
}
{% endhighlight %}

随后在覆盖BeginRequest方法

{% highlight csharp %}
protected void Application_BeginRequest(object sender, EventArgs e)
{
    FirstRequestInitialization.Initialize(Context);
}
{% endhighlight %}