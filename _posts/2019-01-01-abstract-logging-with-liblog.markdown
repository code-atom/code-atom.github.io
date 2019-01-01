---
title: Abstract Logging with LibLog
date: 2019-01-01 19:00:00 +05:30
---

Let suppose you are going to develop some library that going to use in many different project, and you do some logging in project. Let suppose you use Seril.Log in your library project, Now when you distribute your library and if somebody tries to use it, it also need to install Seril.Log Logging Framework even they already have logging framework inplace. In that case you supply unwanted dependency to application that going to use your library.
In order to create library you have following constraints:
- Use existing logging framework, if available.
- if no logging framework avilable, don't force the consumer use one.

Second Scenario: Suppose we are developing software, everything great, sudden somebody said that current logging framework is not efficient and we need to change it, what you going to do change complete logging into your application. Few later, they again request to change the logging framework, again we need to do rework on it. As client point of view, he/she not interested in such thing, he/she focus on their product, if such changes cost them, In this case we can also use abstract logging. 

Yes we can also abstract the logging framework using IOC framework.   

How this can be achieve, by creating or using abstract logging. In this article, we going to discuss the LigLog and how you can configure it and use in it project.