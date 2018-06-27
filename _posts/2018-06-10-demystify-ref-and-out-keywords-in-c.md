---
title: Demystify Ref and Out Keywords in C#
---

In the C# world, the most commonly asked question in an interview, what is different between **ref**  and **out** keyword? In this article, we would understand what is exact difference between these two keywords. Let's start with basic of programming terms, there is a concept of Pass by Value and Pass by Reference.

When a parameter is passed by reference, the caller and the callee use the same variable for the parameter. If the callee modifies the parameter variable, the effect is visible to the caller's variable.

When a parameter is passed by value, the caller and callee have two independent variables with the same value. If the callee modifies the parameter variable, the effect is not visible to the caller.

The ref and out keywords both use the concept of Pass by Reference, but with some compiler restrictions because ref and out keywords treated same at run-time but different during compile-time. Due to this reason, you can't overload a function with a ref and out keywords.

class Example {
	public void AFunction(ref string name) {  /// This show error while compiling
	}
	public void BFunction(out string name) {
	}
}
Restriction force by the compiler on ref keyword:-
Variable must be intialize before pass to function.
In argument list, the variable must use ref keyword.

Restriction force by the compiler on out keyword:-
A variable must be initialized inside the calle function before the returning to the caller function.
In argument list, the variable must use out keyword.

The other common restriction is you can not use ref and out keyword with  properties  and indexers