---
title: 'Part 2: Inject and Injectable decorator of Angular DI'
date: 2018-06-27 05:48:00 +05:30
categories:
- Angular
tags:
- Angular
- DI
- Decorators
- "@Inject"
- "@Injectable"
layout: post
---

@Inject and @Injectable are two decorators in Angular DI System. Each of these decorators has their own use in Angular according to requirement.

But the first question is raised: 
> Why we need decorators for dependency injection in Angular?

Before answering this question first let's look at how Angular DI work.

Angular DI system uses tokens and providers. Think of this as HashTable, where a token is key and provider is our value and provider tells Angular which type of object they need to create and inject in another object.

Now Angular want to create your service and your service/s depend on another service/s. So how Angular knows which objects our service required and how to provide this dependency.

Now decorators come into a picture, they said if you want to resolve your service dependencies, please add me over the class definition, when Typescript compile me, I will add meta-data about the dependency of your services. Angular will read this metadata and resolve your dependencies. Metadata includes dependency token and according to a token, Angular DI retrieve their provider and create an object. 

**@Inject**
A @Inject decorator is used to injecting service with the specific token.

**@Injectable**
@Injectable is used to inject all required dependency, for creating an object, by adding additional metadata in a declaration.

There is a misconception about this decorator that if while creating service, we should always need to declare the injectable decorator over the service class. This is not mandatory, this is due to this [issue](https://github.com/angular/angular/issues/13820).
You only need to add this decorator if service has any dependencies.

If service has any dependencies, you should add @injectable decorator over service class and Angular DI automatically create an object.

Injectable decorator adds metadata to all dependency by defining a single decorator instead of adding multiple @Inject decorators in a constructor.

import { Inject } from  '@angular/core';

class UserService{
   constructor(@Inject(Http) private http: Http, @Inject(AuthService) private auth: AuthService){
   }
}
Instead of this

import { Injectable } from  '@angular/core';

@Injectable()
class UserService{
   constructor(private http: Http, private auth: AuthService){
   }
}



