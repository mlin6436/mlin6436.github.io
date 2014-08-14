---
layout: post
title: "URL Rewrite in IIS 7.5"
tags: ["url", "rewirte", "iis", "7.5", ".net", "c#"]
---

<div class="message">
I use ASP.NET and IIS 7.5 for my web development. As I am currently working on dynamic websites, the long-string-unreadable-URL like "/Feeds.aspx?Key=66a718c4-1982-43b1-9e47-afeddea650e4&FeedType=PAGE" has been a major complain from clients. Therefore, I decided to rewrite URL which shortens the raw URL to a menu-name-related form.
</div>

In order to implement URL rewrite, I create the module RedirectHttpModule based on some open sources found, and use a XML for URL mapping.

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Xml.XPath;

namespace custom
{
    public class RedirectHttpModule : IHttpModule
    {
        public void Dispose()
        { }
        public void Init(HttpApplication application)
        {
            application.PostResolveRequestCache += (new EventHandler(this.Application_OnAfterProcess));
        }
        protected void Application_OnAfterProcess(Object source, EventArgs e)
        {
            HttpApplication application = (HttpApplication)source;
            HttpContext context = application.Context;
            if (!System.IO.File.Exists(application.Request.PhysicalPath))
            {
                string xmlPath = HttpContext.Current.Server.MapPath("PATH");
                XPathDocument docNav = new XPathDocument(xmlPath);
                XPathNavigator nav = docNav.CreateNavigator();
                XPathNodeIterator node;
                string path = context.Request.Path.ToString();
                node = nav.Select("/NewDataSet/Table[vURL='" + path + "']/PageID");
                string pageKey = string.Empty;
                while (node.MoveNext() && node.Count > 0)
                {
                    pageKey = node.Current.Value;
                }
                if (!pageKey.Equals(string.Empty))
                {
                    context.RewritePath(path.Replace(path, "~/Feeds.aspx?Key=" + pageKey + "&FeedType=PAGE"));
                }
                else
                {
                    context.Response.Redirect("~/404.aspx");
                }
            }
        }
    }
}
{% endhighlight %}

On top of that, web.config needs to be setup in the following way.

{% highlight xml %}
<system.web>
  <httpModules>
      <add name="RedirectHttpModule" type="custom.RedirectHttpModule, custom"/>
  </httpModules>
</system.web>
{% endhighlight %}

That's pretty much good to go if you use IIS 5, which I seriously doubt if that's the case. For IIS 7.5, the "HTTP Error 404.0 - Not Found" will keep popping up with error code "0x80070002" which was diagnosed as a mapping mistake. I tried many methods, including adding wildcard, until I found this:

{% highlight xml %}
<system.webServer>
  <validation validateIntegratedModeConfiguration="false" />
  <modules runAllManagedModulesForAllRequests="true">
      <add name="RedirectHttpModule" type="custom.RedirectHttpModule, custom"/>
  </modules>
</system.webServer>
{% endhighlight %}

And there you go.
