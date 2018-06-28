---
title: 'Part -1: Dependency Injection in Angular'
date: 2018-06-26 00:00:00 +05:30
categories:
- Angular
- DI
tags:
- Angular
- Dependency-injection
- DI
background: "/uploads/DI.png"
layout: post
---

In Angular, we already now that Angular have their own dependency injection system for creating the object of services/components and also resolve the dependency of creating the object. In this article, we will talk about how to configure a service in Angular-Dependency Injection System.
Before  we dive into the configuration of providers in DI system, let we first start learning about DI system

> What is Dependency Injection?

> *In software engineering, dependency injection is a technique whereby one object (or static method) supplies the dependencies of another object.*

The term dependency injection is used to describe both design pattern and also the specific implementation DI library that is built-in to Angular. The major benefits of using dependency injection are that the client component doesn't have to be aware of how to create the dependencies, all the component need to know is how to interact with those dependencies.

The DI relies on a principle of inversion of control.

> The Inversion of control principle is also called informally the "Hollywood principle" that is a reference to the Hollywood motto "don't call us, we 'll call you."

Inversion of control of DI invert the process of creating objects from your application code, means no new and static method used to create an object. This supports the lossy coupling of application. DI  concentrate on abstraction rather than implementation. The lossy coupling of application makes code testable.

![dependency.png](/uploads/DI.png)

This diagram shows how angular another component to resolve their dependency in-app life cycle.

**Injection Example:-**

Let suppose we have Product class and each product has a base price and to calculate the actual price we rely on product service.

~~~
 class Product {
   constructor(basePrice: number) {
    this.service= new ProductService();
    this.price = basePrice;
   }
   price() {
     return this.service.calculate(this.price);
   }
}
~~~

This class is tightly coupled with product service and make difficult for us to test. Now suppose this service communicate with backend server and fetch details back and forth. Basically, we are making our tests more brittle by adding an unexcepted dependency between the Product class and ProductService that, in turn, depends on a database.
~~~
class Product {
   constructor(basePrice: number, service: ProductService) {
    this.service= service;
    this.price = basePrice;
   }
   price() {
     return this.service.calculate(this.price);
   }
}
~~~

Now when creating a Product, the client class becomes responsible for deciding which concrete implementation of the ProductService is going to be given to the new instance.

With that, we can make our tests a lot simpler by creating a mock version of the ProductService class.
~~~
class MockProductService{
  calculate(price: number) {
    return price* 1.69;
  }
}
~~~
And with this small change, we can tweak our test slightly and get rid the backend call.

~~~
let product;

beforeEach(() => {
  const service = new MockProductService();
  product = new Product(service, 11);
});

describe('price', () => {
 it('is calculated based on the basePrice', () => {
  expect(product.price()).toBe(18.59);
 });
});
~~~

We also get the bonus of having confidence that we're testing the Product class in isolation. That is, we're making sure that our class works with a predictable dependency.

In this part, we describe the angular dependency and next part we will start with configuring the provider and their different part of configuration and type of configuration for provider.
 
