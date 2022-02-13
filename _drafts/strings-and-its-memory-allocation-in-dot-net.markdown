---
title: Strings and its memory allocation in .NET
date: 2022-02-13 23:02:00 +05:30
---

# String and its memory allocation

The string is the common data type and is used by almost every application. The string is a sequence of characters. Each character encoded in UTF-16 format, String data type has the following characteristics:

- It is a reference type
    
    The string is a reference type, which means the string is a type of object which is allocated in a special memory called Heap, and CLR GC is responsible for the cleanup heap for further allocations. It is immutable in nature which is why it’s treated the same as a value type.
    
- Immutable in nature
    
    The string is immutable in nature. Have you ever tried to change the string value without using reflection?. Actually, we change the string variable reference when we update the string
    
    ```csharp
    string value = "This is foo statement";
    value = value.replace(”foo”, “bar”);
    ```
    
    Above example, we update the `value` variable reference to point to a new string after replacing `“foo”` with `“bar”` in the `value` string. Due to this, strings are thread-safe. 
    
- It overloads the `==` operator
    
    String use == operator, when comparing two string values. String.Equals method is used to compare the content of string variables. It only works if both operators are a type of string data type. If the object data type is used in that case reference equality is performed. Make sure when finding the equality of string always use string data type both side, operator are not applied polymorphically.
    

## When strings are allocated?

When we create something inside “”, CLR will automatically generate string data type inside a memory. Although, you can create a string with their constructor. But in general, we use “”, read data from HTTP request or read content from the file, it will automatically generate a string object inside the memory. Due to immutable in nature, if you try to perform any operation, it will generate a new string object. But wait a minute, then what is string intern pool (aka string pool).

## String interning

.NET has the concept of the intern pool, it is basically a set of strings, in which every time you refer to any string literal, it will always return the same reference of string.

Now the question is, how we can add the string inside a string intern pool?

`String.Intern` method is used to add the strings inside a string intern pool. `String.IsInterned` method is used to check whether a string exists inside a string intern pool. 

For example, the following snippet will only keep two strings around:

```csharp
var greetingText= "Hello";

var stringList = new List<string>();
for (int i = 0; i < 100; i++)
{
    stringList.Add(string.Intern(greetingText+ " World"));
}
```

In the above example, “Hello” is literal, and automatically added in the intern pool and inside a for loop, we interned “Hello World” and for 100 calls, we always return the same string reference and add it into stringList. Optimized solution.

Now you can think why we can’t put every string inside memory or use the `String.IsInterned` method before creating any string, as it always returns the same reference, and CLR doesn’t need to create string again and again. 

In this case, we simply increase the CPU usage as first we need to check whether string exists then create string and in another case, we increase memory usage. so it depends on the use case and CLR is very optimized to clean up memory space. One more reason not to intern all strings automatically is that Interned strings no longer appear on the heap and are allocated on the intern pool instead. There is no garbage collection on that pool: this means those strings will stay in memory forever (well, for the lifetime of the `AppDomain`), even if they are no longer being referenced. So use with caution!

## Conclusion

- If the application has a lot of frequently used strings and is not unique, in that case interning strings can improve the performance.
- If the application has a unique and distinct string, in that case interning string affects the performance and memory usage.
- If the application has short-lived strings, GC will handle the clean-up task.
- When in doubt, measure. Use [a memory profiler](http://jetbrains.com/dotmemory) to detect string duplicates and analyze where they come from and how they can be optimized. Do watch out: [you may see strings as a potential memory issue but they most probably are not](https://blogs.msdn.microsoft.com/tess/2009/02/27/net-memory-leak-reader-email-are-you-really-leaking-net-memory/).

References

- [https://blog.maartenballiauw.be/post/2016/11/15/exploring-memory-allocation-and-strings.html](https://blog.maartenballiauw.be/post/2016/11/15/exploring-memory-allocation-and-strings.html)
- [https://csharpindepth.com/Articles/Strings](https://csharpindepth.com/Articles/Strings)