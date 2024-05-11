---
title: Fix IIS Server Version Disclosure in ASP.NET CORE
date: 2024-05-11 22:22:00 +05:30
categories:
- asp.net core
- Security
tags:
- security
layout: post
---

The server header identifies the server software that processed the request and created the response. However, this header can reveal too much information, making the server vulnerable to attacks. Those with malicious intent can use this information to find weaknesses. If you're using IIS with an [ASP.NET](http://asp.net/) Core application, you'll notice that IIS inserts a `Server` header into your HTTP headers, advertising the server platform.

One might assume you can just use `Response.Headers.Remove("Server")` to solve the issue. However, this approach doesn't work with the IIS Server header. Unlike other headers, it is not produced by the [ASP.NET](http://asp.net/) Core framework, but by the IIS's internal pipeline, and IIS injects it late in the Response processing.

### **Options 1: Removing the IIS Server Header - in web.config**

For this reason the reliable and most effective way to remove the `Server` header is **to do it as part of the IIS processing configuration**, in this case by using `web.config` and setting one of the `requestFiltering` options:

    <system.webServer>
     <security>
        <requestFiltering removeServerHeader="true" />
     </security>
    </system.webServer>

> This feature only works in IIS 10 which was introduced with Windows 10 and Windows Server 2016.

This works great and it requires no other changes in your application. You'll just have to ensure that you publish your custom `web.config` that includes the above `<security>` section with your application.

### **Options 2: Overwrite the Server Header (M**isdirection\*\*)\*\*

If you're comfortable with leaving a server header but want to misdirect by displaying a version of Apache or nginx instead of IIS, you can overwrite the server header with a new value.

So for example using the middleware I described in the previous post you can do:

    app.Use(async(context, next) =>
    {
        context.Response.Headers.Append("Server", "nginx");
        await next();
    });

This produces:

![/uploads/image.png](/uploads/image.png)

Apparently IIS won't inject its own `Server` header, **if your application already has set it**. This means you can use your own header and add some misdirection for potential attacks against your server.

### **What doesn't work**

* **Removing header via code in Middleware**

  While you can modify headers with `Response.Headers` or generic middleware, the `Server` header, added late in the IIS request pipeline by IIS itself, can't be removed through [ASP.NET](http://asp.net/) code to my knowledge.

* **Kestrel Hosting Configuration Options**

  Several older Posts and StackOverflow posts point at using Kestrel configuration options to remove the `Server` header via the `Builder` configuration. While that works for Kestrel's Server header, it has no effect on when hosted in IIS.

      var builder = WebApplication.CreateBuilder(args);
      builder.WebHost
         .UseKestrel(option => option.AddServerHeader = false);
         .UseIIS();

This does not work for the IIS header! It only works for the Kestrel header.

* **Using the web.config`<customHeaders>`Section**

  Like the other approaches mentioned above you can't use the IIS `<customHeaders>` configuration section to `<remove value="server">` either.