---
title: Demystify ref and out keyword in C
date: 2018-06-10 22:45:00 +05:30
categories:
- c#
tags:
- c#
- c#-basics
layout: post
---

In the C# world, the most commonly asked question in an interview, what is different between **ref**  and **out** keyword? In this article, we would understand what is exact difference between these two keywords. Let's start with basic of programming terms, there is a concept of Pass by Value and Pass by Reference.

When a parameter is passed by reference, the caller and the callee use the same variable for the parameter. If the callee modifies the parameter variable, the effect is visible to the caller's variable.

When a parameter is passed by value, the caller and callee have two independent variables with the same value. If the callee modifies the parameter variable, the effect is not visible to the caller.

The ref and out keywords both use the concept of Pass by Reference with data, but with some compiler restrictions.

You can think ref keyword as two way where data passed from caller to callee and back while out keyword is a one way, it sends data from calle to caller and any data passed by a caller is discarded.

**Restriction force by the compiler on ref keyword :-**

* A variable must be initialized before a passing to function.

* In argument list, the variable must use ref keyword.

**Restriction force by the compiler on out keyword :-**

* A variable must be initialized inside the calle function before the returning to the caller function.

* In argument list, the variable must use out keyword.

The other common restriction is you can not use ref and out keyword with properties and indexers.

The **ref** and **out** keywords treated same at compile time in IL code but different at run-time.  The reason they are treated the same at compile time is that their method signatures are the same in MSIL and this is why you can't overload them. Due to this reason, you can not overload the method with a ref and out keywords.

    class Example { 
       public void AFunction(ref string name){ //This show error } 
       public void AFunction(out string name){ }
    }

The error : "**Cannot define overloaded method 'AFunction' because it differs from another method only on ref and out**".