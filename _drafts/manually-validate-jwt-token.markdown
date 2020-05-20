---
title: Manually Validate JWT Token
date: 2020-05-19 22:31:00 +05:30
categories:
- c#
- Security
- jwt
tags:
- c#
- jwt
- security
Field name: 
layout: post
---

In this post, I am gone talk about how to manually validate the JWT token concerning IdentityServer. As you know, what JWT is, It stands for JSON Web Token. it is a type of web token that we used in our authentication and authorization system.

JWT token is a way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA or ECDSA.

JWT token basic focus on authenticity and integrity of information exchange between parties mean token must be issued from valid trust source and their containing data must be non-temper.

First, you need to install these two packages:

* Install-Package System.IdentityModel.Tokens.Jwt

* Install-Package Microsoft.IdentityModel.Protocols.OpenIdConnect

The first thing is to download the OIDC Configuration from the OpenID Connect Discovery endpoint. This will contain (among other things) the JSON Web Key Set containing the public key(s) that can be used to verify the token signature.

``` csharp
var openIdConfigurationEndpoint = $"{auth0Domain}.well-known/openid-configuration";
IConfigurationManager<OpenIdConnectConfiguration> configurationManager = new ConfigurationManager<OpenIdConnectConfiguration>(openIdConfigurationEndpoint, new OpenIdConnectConfigurationRetriever());
OpenIdConnectConfiguration openIdConfig = await configurationManager.GetConfigurationAsync(CancellationToken.None);
```

Next up we need to configure the token validation parameters. I specify the issuer and audience(s) and also tell it to use the signing keys - i.e. the public key(s) - which were downloaded above.

``` csharp
TokenValidationParameters validationParameters = new TokenValidationParameters
{
    ValidIssuer = auth0Domain,
    ValidAudiences = new\[\] { auth0Audience },
    IssuerSigningKeys = openIdConfig.SigningKeys
};
```

With that in place, all you need to do is validate the token:
 
``` csharp
SecurityToken validatedToken;
JwtSecurityTokenHandler handler = new JwtSecurityTokenHandler();
var user = handler.ValidateToken("eyJhbGciOi.....", validationParameters, out validatedToken);
```

ValidateToken will return a ClaimsPrincipal which will contain all the claims from the JSON Web Token.So for example, to get the user’s ID, we can query the NameIdentifier claim:

``` csharp
Console.WriteLine($"Token is validated. User Id {user.Claims.FirstOrDefault(c => c.Type == ClaimTypes.NameIdentifier)?.Value}");
```
