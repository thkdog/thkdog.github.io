---
layout: post
title:  "ASP.NET WebForm使用IoC容器（Spring.NET容器、Castle等等）"
date:   2013-04-22
categories: asp.net
---

WebForm模型不像MVC，MVC的Controller本身使用工厂模式获取，有ControllerFactory的概念，WebForm无法像MVC一样直接替换Controller工厂。

构造注入就别想了，aspx直接被.NET初始化成对象的，你没机会干预这个过程，只能从后期的属性注入下手。

因此主要实现思路有以下2种：

1. 在aspx.cs文件中，需要被注入的属性直接从SpringContext中获取对象

{% highlight csharp %}
ClassName object = (ClassName)ContextRegistry.GetContext().
GetObject("objectId");
{% endhighlight %}

这种方式获取，这种比较适合于页面数量不多，偶尔用一下下的那种（我是MVC项目里嵌了一个aspx.cs文件，图个方便就用这种方式了。）

2. 通过HttpModule，遍历IHttpHandler（aspx页面就是Page类，它同样继承这个接口）中的属性，检测Attribute并实现注入
注意一下，Castle容器比较推荐自动装配的概念，所以Attribute上用Type来表示了，而Spring通常都是用id来描述一个对象，所以Attribute上最好带个objectId字段，这2种方式最好同时支持。
以下是一个Castle的容器示例，这个同样可以应用于Spring，实现起来差不多，Spring中容器上下文获取方法是

{% highlight csharp %}
ContextRegistry.GetContext();
{% endhighlight %}

接下来用法和Castle的就差不多了。（这段代码是网上抄来的，嘿嘿~ ）

{% highlight csharp %}
public partial class IndexPage : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        Logger.Write("page loading");
    }

    [Inject]
    public ILogger Logger { get; set; }
}

// WindsorHttpModule.cs
public class WindsorHttpModule : IHttpModule
{
    private HttpApplication _application;
    private IoCProvider _iocProvider;

    public void Init(HttpApplication context)
    {
        _application = context;
        _iocProvider = context as IoCProvider;

        if(_iocProvider == null)
        {
            throw new InvalidOperationException("Application must implement IoCProvider");
        }

        _application.PreRequestHandlerExecute += InitiateWindsor;
    }

    private void InitiateWindsor(object sender, System.EventArgs e)
    {
        Page currentPage = _application.Context.CurrentHandler as Page;
        if(currentPage != null)
        {
            InjectPropertiesOn(currentPage);
            currentPage.InitComplete += delegate { InjectUserControls(currentPage); };
        }
    }

    private void InjectUserControls(Control parent)
    {
        if(parent.Controls != null)
        {
            foreach (Control control in parent.Controls)
            {
                if(control is UserControl)
                {
                    InjectPropertiesOn(control);
                }
                InjectUserControls(control);
            }
        }
    }

    private void InjectPropertiesOn(object currentPage)
    {
        PropertyInfo[] properties = currentPage.GetType().GetProperties();
        foreach(PropertyInfo property in properties)
        {
            object[] attributes = property.GetCustomAttributes(typeof (InjectAttribute), false);
            if(attributes != null && attributes.Length &gt; 0)
            {
                object valueToInject = _iocProvider.Container.Resolve(property.PropertyType);
                property.SetValue(currentPage, valueToInject, null);
            }
        }
    }
}
{% endhighlight %}