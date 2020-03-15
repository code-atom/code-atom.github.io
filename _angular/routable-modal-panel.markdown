---
title: Routable Modal Panel
date: 2020-03-15 18:33:00 +05:30
categories:
- angular
tags:
- angular
layout: post
---

The article is all about to demonstrate how we can use the existing functionality of a router to open modal or panels in angular application. Recently I am working on a new project where I need to open floatable panels in the application.

Floatable panels on the right side of the application over existing pages. The requirement is open panels in the existing route and re-use panels in various primary routes.

The components required for creating routable modal panel are following:-

* Named Router Outlet

* Container Panel Component

* Routing Configuration for Panel Router Outlet

* Add Bootstrap Modal CSS

**Named Router Outlet**: We need to create a panel named router outlet for open floatable panel on any route. Basically we use auxiliary route to making the floatable panel available to any route in application.

Add the panel router outlet in app component to make available to all routes.

`<router-outlet name="panel"></router-outlet>`

**Panel Container** : Panel Container Component contain backdrop and router outlet for making it routable.

https://stackblitz.com/edit/router-panels?embed=1&file=src/app/panels/panels.component.ts&hideExplorer=1&hideNavigation=1&view=editor

**Panel Routing Configuration**: Add Panel Routing configuration in router configuration.

**Add Bootstrap Model CSS**: To provide the transition and placement of panel is done by override existing bootstrap.