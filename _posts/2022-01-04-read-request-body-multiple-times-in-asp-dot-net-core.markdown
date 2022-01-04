---
title: Read request body multiple times in ASP.NET Core
date: 2022-01-04 09:14:00 +05:30
categories:
- asp.net core
tags:
- asp.net core
Field name: 
layout: post
---

In [ASP.NET](http://asp.net/) Core, the request can not be read once it is consumed. If you want to read the request body multiple times, you need to set

```csharp
context.Request.EnableBuffering()
```

As you know, the request body stream is forward only, you can not read the content from the body once it gets read by middleware or handler. The above method introduced in [ASP.NET](http://asp.net/) Core 2.1, provides multiple time seekable request body streams. This method copy incoming request body stream into [FileBufferingReadStream](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.webutilities.filebufferingreadstream?view=aspnetcore-6.0). 

> **Note:** `Request.EnableBuffering` must be used before any operation is performed on the request body. As you know once the request body is read, it can not read again.
> 

`FileBufferingReadStream` wraps incoming request stream enable rewinding by buffering content as it is read. The content is buffered in memory up to a certain size and then spooled to a temp file on disk. The temp file will be deleted on request disposal.

Prior to [ASP.NET](http://ASP.NET) Core 2.1, `Request.EnableRewind()` get used to make seekable multiple time. Actually `Request.EnableBuffering()` call `Request.EnableRewind()` method internally. But `Request.EnableBuffering` is in a public namespace rather than internal one, it should be prefered over `Request.EnableRewind()`.

Github:[https://github.com/dotnet/aspnetcore/blob/ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52/src/Http/Http/src/Internal/BufferingHelper.cs#L14](https://github.com/dotnet/aspnetcore/blob/ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52/src/Http/Http/src/Internal/BufferingHelper.cs#L14)

Then to read the body stream you could for example do this:

```csharp
string bodyContent = new StreamReader(Request.Body).ReadToEnd();
```

> **Note**: Do not wrap the stream reader instance in `using` block, because it will dispose of the underline stream and code later in the request lifecycle won’t be able to read the body.
> 

On the safer side, set the `Request.Body.Position` reset to 0. That way any code later in the request lifecycle will find the request body in the state just like it hasn’t been read yet.

```csharp
Request.Body.Position = 0;
```

Things to do for reading or seeking `Request.Body` multiple times:

- Use `Request.EnableBuffering` prior to any read operation on `Request.Body` stream.
- Don’t dispose of the `Stream` via `StreamReader` instance.
- Reset `Request.Body.Position` to 0.

Just keep in mind that:

- Even though the memory stream is rented from a pool, it still has memory costs associated with it.
- After the read is over the bufferThreshold in FileBufferingReadStream,  the performance will be slower since a file stream will be used.


