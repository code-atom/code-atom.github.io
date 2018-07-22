---
title: Components of Angular DI
date: 2018-06-27 05:48:00 +05:30
categories:
- Angular
tags:
- Angular
- DI
- Decorators
layout: post
---

@Inject and @Injectable are two decorators in our Angular DI System. Each of these decorators has their own use in Angular according to requirement.

**@Injectable**
@Injectable is used to inject all required dependency for creating an object. There is a misconception about this decorator that if we create service, we should always need to declare the injectable decorator over the class. But we need to understand why we need to use this decorator in our code. Now Angular want to create  your component/service, but your service/s or component/s depend on other services. So now how Angular which objects our component/service required.

Now Angular said that can you give me some data about your dependency like where it reside or type like stuff. But like we already know that we already mention type in constructor now why Angular asking for type and their defination location. Typing System is feature of Typescript, but our runnable code is in javascript and in javascript we don't have any typing and Angular want the dependence in js side.

Now our decorators comes into picture, they said if you want to resolve your service dependencies, please add me over class defination, when Typescript compile me, I will add meta-data about dependency of your services. Angular will read this metadata and resolve your dependencies.

  

@Injectable
class AuthService {
   isLogged() {
      return true;
    }
}

**@Inject**
A @Inject decorator is used to injecting service with the specific token.