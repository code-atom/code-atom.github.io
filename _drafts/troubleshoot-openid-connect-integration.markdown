---
title: Troubleshoot OpenID Connect Integration
date: 2020-05-26 20:22:00 +05:30
categories:
- Identity Server
- Troubleshooting
tags:
- troubleshooting
- openid-connect
- identity sevrer
layout: post
---

In this post, I gone discuss about  basic  troubleshooting  steps or checkpoints to the  OpenID-Connect integration of Identity Server with their consuming application. As you know, In  Identity Server 4, we basically support  only two types of  integration:

* Front-Channel  Login (aka Passive mode include  Hybrid, Authorization Code, Implicit)

* Back Channel Login (aka Client Credential) .

### **Front channel Login Integration troubleshooting**

In front channel login integration, you may use  any available  integration flow  like Authorization Code, Hybrid or Implicit. In all these grant type we need to different type of  different data. First I will listed out the common things in all these grant  types that we need to verify if we having any issue like 'unauthorized_client'.

* ClientId

* Scopes

* RedirectUrls

* ResponseType\
       **Grant Type Flow
**\
  	**Authorization Code Flow = 0**\
  		ResponseType: "code"
\
  	**Implicit Flow = 1**
\
  		ResponseType: "id_token"
\
  		ResponseType: "id_token token"
\
  	**Hybrid Flow** = 2
\
  		ResponseType: "code id_token",
\
  		ResponseType: "code token"
\
  		ResponseType: "code id_token token"

* Client Secret (Required if code is provided in response type)

### Back channel Login Integration troubleshooting

It is simple to troubleshoot, because in this we need to verify clientid, client secret and scopes and make the scopes that used in this integration must belongs to API Resource. Identity Resources are not used in this integration because in this secnario there is no acitve user present. It's basically used for service to service trusted communication.

* ClientId

* Scopes

* ClientSecret