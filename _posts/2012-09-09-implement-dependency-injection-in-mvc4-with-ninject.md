---
layout: post
title: "Implement Dependency Injection in MVC 4 with Ninject"
tags: ["di", "dependency injection", "mvc4", "mvc", "ninject", ".net", "asp.net"]
---

<div class="message">
This time, I would like to show off the tricks I used to get Ninject working in MVC 4, to archive dependency injection. But you don't need to go through all the hustle if you are using MVC 3.
</div>

After creating an empty MVC 4 project, the first thing I realised was there were some framework [changes](http://www.asp.net/mvc/tutorials/hands-on-labs/whats-new-in-aspnet-mvc-4) in comparison to MVC 3. Most obvious changes happen in `Global.asax.cs`, which moves all the configuration setting registers into separate class files in the folder `App_Start`.

This actually prevents me from using `Ninject.MVC3` like I used to do in MVC 3. Even more unfortunately is that Ninject package for MVC 4 is not out yet, that why this alternative solution is used.


> NOTE: By the time I am updating this blog, [Ninject for MVC 4](https://www.nuget.org/packages/Ninject.MVC4/) has been available for a white, and I am sure there are cleaner and better ways to implement dependency injection. But carry on reading if you are still interested in the though process, and have fun!


# Add 'Ninject.MVC3' package

You will see `NinjectWebCommon.cs` file is generated inside 'App_Start' folder, which was meant to be part of package adding process.

# Modify 'Global.asax.cs'

- Inherit `MvcApplication` from `NinjectHttpApplication` instead of `HttpApplication`
- Implement `OnApplicationStarted` method inherited from `NinjectHttpApplication`, and move all registers in `Application_Start` this method to support the normal usage of those functions
- Implement `CreateKernel` method and register your own services

{% highlight csharp %}
namespace Ninject.MVC4
{
    // TODO: Remove this auto-generated class declaration
    //public class MvcApplication : System.Web.HttpApplication
    //{
    //    protected void Application_Start()
    //    {
    //        AreaRegistration.RegisterAllAreas();
    //
    //        WebApiConfig.Register(GlobalConfiguration.Configuration);
    //        FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
    //        RouteConfig.RegisterRoutes(RouteTable.Routes);
    //        BundleConfig.RegisterBundles(BundleTable.Bundles);
    //        AuthConfig.RegisterAuth();
    //    }
    //}

    public class MvcApplication : NinjectHttpApplication
    {
        protected override void OnApplicationStarted()
        {
            base.OnApplicationStarted();

            AreaRegistration.RegisterAllAreas();

            WebApiConfig.Register(GlobalConfiguration.Configuration);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            AuthConfig.RegisterAuth();
        }

        /// <summary>
        /// Creates the kernel that will manage your application.
        /// </summary>
        /// <returns>The created kernel.</returns>
        protected override IKernel CreateKernel()
        {
            var kernel = new StandardKernel();
            kernel.Bind<Func<IKernel>>().ToMethod(ctx => () => new Bootstrapper().Kernel);
            kernel.Bind<IHttpModule>().To<HttpApplicationInitializationHttpModule>();

            RegisterServices(kernel);
            return kernel;
        }

        /// <summary>
        /// Load your modules or register your services here!
        /// </summary>
        /// <param name="kernel">The kernel.</param>
        private static void RegisterServices(IKernel kernel)
        {
            kernel.Bind<IFlorentinesService>().ToDefaultChannel();
        }
    }
}
{% endhighlight %}

# Modify controller

Introduce service into controller constructor before consumption.

{% highlight csharp %}
namespace MVC4.Controllers
{
    public class HomeController : Controller
    {
        private ITestService service;
        public HomeController(ITestService service)
        {
            this. service = service;
        }
    }
}
{% endhighlight %}

# Delete 'NinjectWebCommon.cs'

By specifying client endpoint, the service injection is done.
