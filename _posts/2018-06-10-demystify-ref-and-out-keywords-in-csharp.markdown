---
title: Demystify ref and out keywords in C#
date: 2018-06-10 22:45:00 +05:30
categories:
- c#
tags:
- c#
- c#-basics
background: "/uploads/c%23refout.jpg"
when to use: https://stackoverflow.com/questions/1516876/when-to-use-ref-vs-out
Field name: 
layout: post
---

In the C# world, the most commonly asked question in an interview, what is different between **ref**  and **out** keyword? In this article, we would understand what is exact difference between these two keywords. Let's start with basic of programming terms, there is a concept of **Pass by Value** and **Pass by Reference**.

When a parameter is **passed by reference**, the **caller** and the **callee** use the **same variable** for the parameter. If the **callee modifies the parameter** **variable, the effect is visible to the caller's variable.**

When a parameter is **passed by value**, the **caller** and **callee** have two independent variables with the same value. If the **callee modifies the parameter variable, the effect is not visible to the caller.**

The ref and out keywords both use the concept of Pass by Reference with data, but with some compiler restrictions.

You can think ref  keyword as two way where data passed from caller to callee and back while out keyword is a one way, it sends data from calle to caller and any data passed by a caller is discarded.

    class Example {
            public static void main(string[] args) {
                int i = 10;
                FunctionWithRef(ref i);
                Console.WriteLine("The manipulated value of i {0}", i);
                int j;
                FunctionWithOut(out j);
                Console.WriteLine("The manipulated value of j {0}", j);
            }
            
            public static void FunctionWithRef(ref int number) {
                number = number + 10;
            }
            
            public static void FunctionWithOut(out int number){
                number = number + 10;
            }   
    }

**Restriction force by the compiler on ref keyword:-**

* A variable must be initialized before a passing to function.

* In argument list, the variable must use ref keyword.

**Restriction force by the compiler on out keyword:-**

* A variable must be initialized inside the calle function before the returning to the caller function.

* In argument list, the variable must use out keyword.

The other common restriction is you can not use ref and out keyword with properties and indexers.

The **ref** and **out** keywords treated **same at compile time in IL code but different at run-time. The reason they are** treated the same at compile time is that their **method signatures are the same in MSIL** and this is why you **can't overload** them.

    class Example { 
       public void AFunction(ref string name){ //This show error } 
       public void AFunction(out string name){ }
    }

The error : "**Cannot define overloaded method 'AFunction' because it differs from another method only on ref and out**".

There are some discussions over the internet that ref and out are same at compile time but different at run-time. Yes, you can say that ref and out declaration treated differently in compile time because you can see the restriction that forced by the compiler as above make difference between ref and out.

Thanks for reading :)
