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
@Injectable is used to inject all required dependency for creating an object. There is a misconception about this decorator that if we create service, we should always need to declare the injectable decorator over the class.

@Injectable
class AuthService {
   isLogged() {
      return true;
    }
}

**@Inject**
A @Inject decorator is used to injecting service with the specific token.