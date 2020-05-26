---
title: Troubleshoot OpenID Connect Integration
date: 2020-05-26 20:22:00 +05:30
---

In this post, I gone discuss about  basic  troubleshooting  steps or checkpoints to debug  the  OpenIDConnect integration of Identity Server with their consuming application. As you know, In  Identity Server 4, we basically support  only two types of  integration:

* Front-Channel  Login (aka Passive mode include  Hybrid, Authorization Code, Implicit)

* Back Channel Login (aka Client Credential) .

### **Front channel Login Integration troubleshooting**

In front channel login integration, you may use  any available  integration flow  like Authorization Code, Hybrid or Implicit. In all these grant type we need to different type of  different data. First I will listed out the common things in all these grant  types that we need to verify if we having any issue like 'unauthorized_client'.

* ClientId

* Scopes

* RedirectUrls

* ResponseType

* Client Secret 

### Back channel Login Integration troubleshooting

* ClientId

* Scopes

* ClientSecret