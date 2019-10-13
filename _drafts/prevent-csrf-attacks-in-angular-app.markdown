---
title: Prevent CSRF Attacks in Angular App
date: 2019-10-13 22:52:00 +05:30
categories:
- Security
tags:
- security
- angular
- ".netcore"
- owasp
layout: post
---

Before getting started with CSRF Attack, First, we understand how our web authentication works. As you all know HTTP is a stateless protocol. Aaaah? What it means. It means all HTTP requests are executed independently, without any knowledge of request executed before it. So if that is the case, you need to specify your username/password every time, when you send requests to a banking site.

But we provide username/password only once and after we don’t need to provide any username/password in subsequent requests. How this magic happened because when we logged in and get a response from the server, it sends a session cookie or authentication cookie. It is a browser's responsibility to save this cookie and send the cookie with every request. The browser stored the cookies specific to the domain(host). The browser restricts the sites to access cookies of another domain(host). The browser did great security from there side. And we know nobody can access my site cookie into their domain. It means we are secured.

But wait for a second, here attacker know to exploit your bank cookie from their site. 
They used this code in their site, which you open by-chance or get clickbait

# How to protect our application CSRF protected? 

In a common anti-XSRF technique, the application server sends a randomly generated authentication token in a cookie. The client code reads the cookie and adds a custom request header with the token in all subsequent requests. The server compares the received cookie value to the request header value and rejects the request if the values are missing or don't match.
This technique is effective because all browsers implement the same-origin policy. Only code from the website on which cookies are set can read the cookies from that site and set custom headers on requests to that site. That means only your application can read this cookie token and set the custom header. The malicious code on evil.com can't.
To prevent this, the application must ensure that a user request originates from the real application, not from a different site. The server and client must cooperate to thwart this attack.

**Client-side**
HttpClient supports a common mechanism used to prevent XSRF attacks. When performing HTTP requests, an interceptor reads a token from a cookie, by default XSRF-TOKEN, and sets it as an HTTP header, X-XSRF-TOKEN. Since only code that runs on your domain could read the cookie, the backend can be certain that the HTTP request came from your client application and not an attacker.

```ts
imports: [
  HttpClientModule,
  HttpClientXsrfModule.withOptions({
    cookieName: 'X-XSRF-COOKIE',
    headerName: 'X-XSRF-TOKEN',
  }),
]
```

**Server Side**
In Asp.net's core application, we need to add AntiForgery services in application DI.

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

After that, we need to assign an anti-forgery cookie to request using middleware, that we need to validate on every modification endpoint.

```cs
app.Use(next => context =>
{
    if (string.Equals(context.Request.Path.Value, "/", StringComparison.OrdinalIgnoreCase) ||
        string.Equals(context.Request.Path.Value, "/index.html", StringComparison.OrdinalIgnoreCase))
    {
        var tokens = antiforgery.GetAndStoreTokens(context);
        context.Response.Cookies.Append("X-XSRF-COOKIE", tokens.RequestToken,
            new CookieOptions() {HttpOnly = false});
    }

    return next(context);
});
 ```

Now Anti-forgery mechanisms configure, we only need to add ValidateAntiForgeryAttributes on our modification endpoint.
