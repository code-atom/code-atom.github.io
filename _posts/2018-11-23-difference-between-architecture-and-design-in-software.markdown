---
title: Difference between Architecture and Design in Software
date: 2018-11-23 07:25:00 +05:30
categories:
- architecture
tags:
- architechture
- design
background: 
layout: post
---

We often use these two keywords in our daily work life “Design” and “Architecture”. But what is an actual difference in Design and Architecture?

Architecture is the logical implementation of Software.

Design is the physical implementation of Software.

<p class="text-center">
![Untitled Diagram.jpg](/uploads/Untitled%20Diagram.jpg)
</p>
<p class="text-center"><img class="img" src="/uploads/Untitled%20Diagram.jpg" alt="difference btw architecture and design"/></p>

The architecture describes what and where we need to do things whereas design illustrates how to do things.

Architecture deal with essential things whereas Design dealings with detailed implementation.

The architecture provides a high-level arrangement of components or modules. Whereas Design provides a detail arrangement within component how they communicate with each other, what design pattern used to solve this problem. How to break down complexity and which design pattern is applicable. The architect provides a high-level arrangement of components, leaves modules or components design to module designer.

Architecture support non-functional requirements (like logging, load balancing, fault tolerant, cloud supported, Testing approach, Documentation, Scalability, Security, Maintainability etc) and has to be review and approved by other architects. The design support functional requirement and the design changes are internal to component whereas Architecture changes refer to the architectural change

<p class="text-center"><img class="img" src="/uploads/Untitled%20Diagram.png" alt="difference btw architecture and design"/></p>

That is why we said that it is hard to change the architecture because our design components or modules depend on it and if we change the architecture we also need to change the components.

Technical Example: We have 3 layer architecture patter in which we divided our complexity into three different layers. Now my application presentation layer is in web form and now I want it to change into MVC pattern. What we need to do change only the presentation layer.

Now I want to add the logging or performance metric in our application so that I can measure application efficiency, now this change belongs to architecture because I need to change all layers in application to support this requirement.

General Example:- Let suppose we have a project of multi-story building, before start construction, we have a various factor about estate and near about area like markets, electricity supply, water supply, earthquake zone, support for conventional and non-conventional sources of energy etc. These are non-functional requirements, now architect gathers all these information starts creating architecture, which is approved by the government. After the approval, we start creating the building and according to the requirement, we support a maximum 4 Bedroom Apartments to create in our building. If we need to support 5 Bedroom Apartment, then we need to redesign our architecture, That is we said architecture is hard to change. Now we create Apartment, in which customer specify their requirement like they need 3 Bedroom, Kitchen facing to the north and so on. All these requirements are design specific, it doesn’t affect our architecture of the building. Now somebody wants to the solar power supply in their apartment, now if our architecture supports the conventional source of energy then we will provide the option.

I think example explains the basic difference between architecture and design.

Share your opinions on this. Please refer to this link for presentation.