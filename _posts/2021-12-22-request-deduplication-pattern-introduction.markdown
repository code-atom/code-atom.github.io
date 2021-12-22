---
title: 'Request Deduplication Pattern: Introduction'
date: 2021-12-22 23:48:00 +05:30
categories:
- API Design
tags:
- api-design-pattern
layout: post
---


## **Motivation**

- Safe retry mechanism for non-idempotent method.

In general, when you try to send a request to a server and don’t get any response, there are two possibilities.

- In one case, your device is incapable to connect with the server due to a network issue.
- In the second case, your device can connect with a server but does not get any response from a server.

The client is incapable to differentiate between these two cases because a client does not get any response from a server.

**Compare the above two cases with the idempotent and non-idempotent method**

- With the idempotent resource method (Ex: [GET]: Retrieving customers list) :
    - In both cases, if the client requests the idempotent method, in that case, we do not need to worry about it.
- With the non-idempotent resource method(Ex: [POST]: Intiaite payment) :
    - In both cases, if the client request the non-idempotent method, and the client retries the method again, it will cause duplication on the server.

## Goal

The main goal of the request deduplication pattern is to define a mechanism by which we can prevent duplicate requests specifically for non-idempotent methods, across the API. In other words, we should be able to safely retry the method without knowing whether the request was received and without worrying about the method being executed twice as a result of retrying.

## High-Level Design

In this pattern, a unique identifier is assigned to each request that we want to ensure is serviced only once. Using this unique identifier, we can see whether the current request was already handled by the API service and if so, we can avoid acting on it again.

Now the question comes: what happened if the API service caught any duplicate request? Should API service return an error message stating that the request has been already processed? but that is not useful in the real world, because when we retry the request, we expect some useful result, whether the request has been already processed.

To handle this, we need to cache the response corresponding to the request’s unique identifier. When the API service found any duplicate request, we can return the response from the cache as it would have been returned when the request was first made.