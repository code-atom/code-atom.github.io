---
title: 'Part 2: Inject and Injectable decorator of Angular DI'
date: 2018-06-27 05:48:00 +05:30
categories:
- Angular
tags:
- Angular
- DI
- Decorators
layout: post
---

@Inject and @Injectable are two decorators in Angular DI System. Each of these decorators has their own use in Angular according to requirement.

But the first question is raised: 
> Why we need decorators for dependency injection in Angular?

Before answering this question first let's look at how Angular DI work.

Angular DI system uses tokens and providers. Think of this as HashTable, where a token is key and provider is our value and provider tells Angular which type of object they need to create and inject in another object.

we need to understand why we need to use this decorator in our code. Now Angular want to create your component/service, but your service/s or component/s depend on other services. So now how Angular which objects our component/service required.

Now Angular said that can you give me some data about your dependency like where it resides or type like stuff. But like we already see that we have already mentioned types in constructor now why Angular asking for type and their destination location. The reason is **Typing System** is a feature of Typescript, but our runnable code is in javascript and in javascript we don't have any typing and Angular want the dependence in js side.

Now our decorators come into the picture, they said if you want to resolve your service dependencies, please add me over the class definition, when Typescript compile me, I will add meta-data about the dependency of your services. Angular will read this metadata and resolve your dependencies.

**@Injectable**
@Injectable is used to inject all required dependency, for creating an object, by adding additional metadata in a declaration.

There is a misconception about this decorator that if we create service, we should always need to declare the injectable decorator over the class. But 

  

@Injectable
class AuthService {
   isLogged() {
      return true;
    }
}

**@Inject**
A @Inject decorator is used to injecting service with the specific token.