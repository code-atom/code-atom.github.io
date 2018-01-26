---
title: Angular Best Practice For Component
date: 2018-01-21 21:30:00 +05:30
tags:
- Angular
- Angular-best-practices
subTitle: 
background: https://www.efrontlearning.com/blog/wp-content/uploads/2013/08/homepage-design-best-practices.jpg
layout: post
---

In angular, our all application built with components, component is same as a controller in angular V1.x. The Component is the important building of Angular App.  Due to its importance, we must have to follow the best practices while creating a component in the app.

In angular, applications built with components, a component is as same as a controller in angular V1.x. The Component is the important building block of Angular App. Due to its importance, we must have to follow the best practices while creating a component in the app.

## **Best Practices**

* Prefixing Component Selector.

* Separating inline CSS and HTML from the Component Declaration.

* Handle input and Output Property Declaration.

* Delegate the complex logic to services.

* Component Member Sequence.

* Implement Life Cycle hooks in Component.

## **Prefix Component Selector**

Let say if you create a component in an angular app, first it must be **located in their feature module**. The second thing is we need to set component selector according to feature module and if you have a **common component** that resides in a shared module then **selector prefix will be app name**.

Let say we look at a very common scenario like we have loading component in our application. so we can set their selector property with a prefix of the app name.
```
@Component({
  selector: 'app-loading-spinner', // here you can see the 'app' is prefix with component selector
  template: '<img *ngIf="loading" src="./assets/images/loading.gif" />'
})
export class LoadingComponent {
  @Input() loading:boolean;
}
```

It helps us to remove the conflict between different components. The **prefix must be 3 or 4 characters l**ong.

## **Separate inline CSS and HTML from Component**

It is considered to good practice if you have **code more than 3 line** in CSS and HTML template property of component declaration into a separate file for good readability. It also defines the Angular style guide. It also encourages Single Responsibility Principal and you can locate your code quickly.

## **Handle @Input and @Output**

In Angular Component, you can define the input and output property in metadata component or use decorators. Now which one better approach.** @Decorator approach is the best approach for declaring the input and output property** in the component. Because it helps us identify which property is input and which one is output at glance.

## **Delegating complex logic to service**

As we previous discuss the single repository principal in handling CSS and HTML template in a component. we separate the Html and Styling into a separate file for better readability. Now if we are doing some complex logic like call API endpoint in the component. That will violate SRP because now our component does two things providing data to view and make API call to the server for data. So what we do **create a service and move all complex logic like API related stuff in that service** and now component only knows that service gives data for displaying data.

## **Component Member Sequences**

The best practice of declaring member in the component is to declare all the properties before constructor or methods. The class member** ordering must be the public class member must be declared before the private class member**. This ordering will apply to both property and method means first public properties and then private properties and same for methods.

## **Implement Life Cycle hooks in Component**

The best practice to **implement the lifecycle hook of the component is to use their interface** that enforces the typescript class to implement the method. Like OnInit implements, the method ngOnInit; Placed this method after the constructor.