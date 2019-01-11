---
title: Constants in C#
date: 2019-01-11 17:06:00 +05:30
tags:
- c#
- c#-basics
layout: postv2
---

A constant is a static field whose value can never change. A constant is evaluated statically at compile time and the compiler literally substitutes its value whenever used (rather like a macro in C\+\+). A constant can be any of the built-in numeric types, bool, char, string, or an enum type.Any reference type excepted string can not be assigned to constant. A constant is declared with the const keyword and must be initialized with a value. Let take an example to explain above lines:-
As we said compiler literally substitutes its values whenever used.
In C# implementation
using System;
```
public class Program
{
public const string CONSTANTSTR = "replaceableString";
public static void main(string\[\] args)
{
string _constantValue = Program.CONSTANTSTR;
Console.WriteLine(_constantValue);
}
}
```
after compiling, MSIL output:
```
.class public auto ansi beforefieldinit Program
extends \[mscorlib\]System.Object
{
// Fields (Constants that we declared)
.field public static literal string CONSTANTSTR = "replaceableString"

    // Methods (Main Method)
    .method public hidebysig static 
        void Main (
            string[] args
        ) cil managed 
    {
        // Method begins at RVA 0x2050
        // Code size 15 (0xf)
        .maxstack 1
        .entrypoint
        .locals init (
            [0] string _constantValue
        )
    
        IL_0000: nop
        IL_0001: ldstr "replaceableString" // here compiler replace it complete.
        IL_0006: stloc.0
        IL_0007: ldloc.0
        IL_0008: call void [mscorlib]System.Console::WriteLine(string)
        IL_000d: nop
        IL_000e: ret
    } // end of method Program::Main
    
    .method public hidebysig specialname rtspecialname 
        instance void .ctor () cil managed 
    {
        // Method begins at RVA 0x206b
        // Code size 8 (0x8)
        .maxstack 8
    
        IL_0000: ldarg.0
        IL_0001: call instance void [mscorlib]System.Object::.ctor()
        IL_0006: nop
        IL_0007: ret
    } // end of method Program::.ctor

} // end of class Program
```
The advantage of a constant that it reduces the runtime effort that system has to perform while running the application.what I mean, if you can set use constantly anywhere in the application your compiler automatically replace it. If you declare a variable to be a constant, then the optimizer can often eliminate it via 'constant folding' and thus both speed up your program and save you space(link). It makes the code more readable.
There is also one pitfall with constants. For instance, suppose assembly X exposes a constant as follows:
public const decimal ProgramVersion = 2.3;

If assembly Y references X and uses this constant, the value 2.3 will be baked into assembly Y when compiled. This means that if X is later recompiled with the constant set to 2.4, Y will still use the old value of 2.3 until Y is recompiled.
Constant Folding and Constant Propagation
Constant folding and Constant propagation are compiler optimization technique. Constant folding is the process of recognizing and evaluating constant expressions at compile time rather than computing them at runtime.Constant propagation is the process of substituting the values of known constants in expressions at compile time.
For understanding, let take an example:
i = 10 \*100;

This what we commonly saw in our application, now when compiler compiles this code, it will automatically replace the 10\*100 with 1000.
Same thing for string, like we try to combine two string like this
string combine = "com "\+ "bine";

Complier compile and make result
string combine = "combine";

Constant folding can be done in a compiler's front end on the IR tree that represents the high-level source language, before it is translated into three-address code, or in the back end, as an adjunct to constant propagation.
Constant propagation is the process of substituting the values of known constants in expressions at compile time.
Common example of the constant propagation
int x = 10;
int y = x\* 0;

Now everyone knows if any number multiple with 0, it will zero. the compiler also analyze that code and replace with
int x = 10;
int y = 0;

Question:
I have a  question regarding string constant, I said why the string is used as constant?
After asking this question on StackOverflow, I found the many facts about string and how they used in constant.Here link to my question. String constant has built in compatibility in the compiler and only string literal is assign to string constant because literal is a notation of representing the fixed value. Common constants are used in place of literals. The string is immutable that why is it used as constant. but few point makes in mind the while initialize the constant it does take any new keyword assignment and static variable assignment only literals are assigned to constants.
Sources :

* https://en.wikipedia.org/wiki/Constant_folding

* C# in Depth (Book)

* StackOverflow