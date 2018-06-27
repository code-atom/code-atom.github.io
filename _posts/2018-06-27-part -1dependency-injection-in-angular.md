---
title: 'Part -1: Dependency Injection in Angular'
---

In Angular, we already now that Angular have their own dependency injection system for creating the object of services/components and also resolve the dependency of creating the object. In this article, we will talk about how to configure a service in Angular-Dependency Injection System.
Before  we dive into the configuration of providers in DI system, let we first start learning about DI system

> What is Dependency Injection?
> *In software engineering, dependency injection is a technique whereby one object (or static method) supplies the dependencies of another object.*

The term dependency injection is used to describe both design pattern and also the specific implementation DI library that is built-in to Angular.The major benefits of using dependency injection are that the client component doesn't have to be aware of how to create the dependencies, all the component need to know is how to interact with those dependencies.

The DI relies on a principle of inversion of control. 
>  The Inversion of control principle is also called informally the "Hollywood principle" that is a reference to the Hollywood motto "don't call us, we 'll call you."

Inversion of control of DI invert the process of creating objects from your application code, means no new and static method used to create an object. This supports the lossy coupling of application. DI  concentrate on abstraction rather than implementation. The lossy coupling of application makes code testable.