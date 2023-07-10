---
title: Manual Validate Azure Graph API Access token
date: 2023-07-10 21:01:00 +05:30
categories:
- azure
- jwt
- c#
layout: post
---

Yesterday, one of my connections contacted me and asked for assistance with manually validating access tokens, stating that he was experiencing Signature validation errors. So I reasoned that there could be a problem with the signing keys used to validate the signature. However, after hours of investigation, we discovered that the access token we received from Azure Ad is produced for graph api and has a nonce value in the token header. As a result, our validation procedure fails..

Azure AD has two token generating API versions. The regular token validation technique will work for version 1. However, before verifying the token for version 2, we must do some more work. You must extract the nonce from the JWT header, compute its SHA256, and then add it back to the JWT header.

    string[] parts = accessToken.Split('.');
    string header = parts[0];
    string payload = parts[1];
    string signature = parts[2];
    var tokenHandler = new JwtSecurityTokenHandler();
    var jsonToken = tokenHandler.ReadJwtToken(accessToken);
    if (jsonToken.Header.TryGetValue("nonce", out object nonceAsObject))
    {
        string plainNonce = nonceAsObject.ToString();
        using (SHA256 sha256 = SHA256.Create())
        {
            byte[] hashedNonceAsBytes = sha256.ComputeHash(
                System.Text.Encoding.UTF8.GetBytes(plainNonce));
            string hashedNonce = Base64Url.Encode(hashedNonceAsBytes);
            jsonToken.Header.Remove("nonce");
            jsonToken.Header.Add("nonce", hashedNonce);
            header = tokenHandler.WriteToken(jsonToken).Split('.')[0];
    
            jsonToken = tokenHandler.ReadJwtToken($"{header}.{payload}.{signature}");
        }
    }

Here is full code with token generate and validation logic.

    var app = ConfidentialClientApplicationBuilder
    .Create("XXXXXXXXXXXXXXXXX")
    .WithClientSecret("XXXXXX")
    .WithTenantId("XXXX")
    .Build();
    
    string[] scopes = new string[] { "https://graph.microsoft.com/.default" };
    var authResult = await app.AcquireTokenForClient(scopes).WithForceRefresh(true).ExecuteAsync();
    string accessToken = authResult.AccessToken;
    
    var authorityEndpoint = "https://login.microsoftonline.com/XXXX/v2.0/";
    var openIdConfigurationEndpoint = $"{authorityEndpoint}.well-known/openid-configuration";
    IConfigurationManager<OpenIdConnectConfiguration> configurationManager = new ConfigurationManager<OpenIdConnectConfiguration>(openIdConfigurationEndpoint, new OpenIdConnectConfigurationRetriever(), new HttpDocumentRetriever());
    OpenIdConnectConfiguration openIdConfig = await configurationManager.GetConfigurationAsync(CancellationToken.None);
    var keys = openIdConfig.SigningKeys.Where(x => x.GetType() == typeof(Microsoft.IdentityModel.Tokens.X509SecurityKey)).ToList();
    TokenValidationParameters validationParameters = new TokenValidationParameters
    {
      ValidateAudience = false,
      ValidateIssuer = false,
      IssuerSigningKeys = keys,
      ValidateLifetime = false,
      ValidateIssuerSigningKey = false,
      TokenReader = (string token, TokenValidationParameters validationParameters) =>
      {
          string[] parts = token.Split('.');
          string header = parts[0];
          string payload = parts[1];
          string signature = parts[2];
          var tokenHandler = new JwtSecurityTokenHandler();
          var jsonToken = tokenHandler.ReadJwtToken(token);
          if (jsonToken.Header.TryGetValue("nonce", out object nonceAsObject))
          {
              string plainNonce = nonceAsObject.ToString();
              using (SHA256 sha256 = SHA256.Create())
              {
                  byte[] hashedNonceAsBytes = sha256.ComputeHash(
                      System.Text.Encoding.UTF8.GetBytes(plainNonce));
                  string hashedNonce = Base64Url.Encode(hashedNonceAsBytes);
                  jsonToken.Header.Remove("nonce");
                  jsonToken.Header.Add("nonce", hashedNonce);
                  header = tokenHandler.WriteToken(jsonToken).Split('.')[0];
    
                  jsonToken = tokenHandler.ReadJwtToken($"{header}.{payload}.{signature}");
              }
          }
          return jsonToken;
      }
    };
    IdentityModelEventSource.ShowPII = true;
    var handler = new JwtSecurityTokenHandler();
    var user = handler.ValidateToken(accessToken, validationParameters, out var tt);

### **Reference links**

* [https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/issues/609#issuecomment-405683736](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/issues/609#issuecomment-405683736)

* [https://tonesandtones.github.io/azure-ad-and-the-unvalidatable-access-token/](https://tonesandtones.github.io/azure-ad-and-the-unvalidatable-access-token/)

* [https://stackoverflow.com/questions/56954716/cannot-validate-aad-access-token-idx10511-signature-validation-failed/71588115#71588115](https://stackoverflow.com/questions/56954716/cannot-validate-aad-access-token-idx10511-signature-validation-failed/71588115#71588115)