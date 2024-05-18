---
title: SharedAPIKey Authentication in ASP.NET Core WebAPI
date: 2024-05-18 22:59:00 +05:30
tags:
- api-security
- asp.net core
- authentication
layout: post
---

API Key authentication represents the initial stage, or Level-0, within the API Security Maturity Model. In this phase, the focus is on basic security measures, primarily revolving around the use of API keys for authentication purposes. These keys are unique identifiers that grant access to the API and serve as a simple yet crucial first line of defence in API security.

Implementing SharedAPIKey authentication in [ASP.NET](https://asp.net/) Core WebAPI involves creating a custom authentication scheme that checks for a valid API key on incoming requests. This key is typically passed in the request header and is validated against a stored key on the server but we can pass API key in query string also. If the key is valid, the request is allowed to proceed; if not, it is rejected.

- Define the Request API Key Provider to extract the API key from the request. You can define multiple providers to extract the API key from various sources such as headers, query strings, etc.

```csharp
    public class ProviderApiKeyResult
    {
        public string ApiKey { get; set; }

        public ProviderApiKeyResult(string apiKey)
        {
            ApiKey = apiKey;
        }
    }

    public interface IRequestApiKeyProvider
    {
        Task<ProviderApiKeyResult?> DetermineProviderApiKeyResult(HttpContext httpContext);
    }
    
    public class RequestHeaderApiKeyProvider : IRequestApiKeyProvider
    {
        public string HeaderKey { get; set; } = "X-API-KEY";

        public async Task<ProviderApiKeyResult?> DetermineProviderApiKeyResult(HttpContext httpContext)
        {
            if (!httpContext.Request.Headers.ContainsKey(HeaderKey))
            {
                return default;
            }
            var apiKey = httpContext.Request.Headers[HeaderKey];
            return new ProviderApiKeyResult(apiKey);
        }
    }
```

- Create Shared API Key Authentication Options. In these options, you can configure your shared API key and Request API Key Providers.

```csharp
public class SharedApiKeyAuthenticationOptions : AuthenticationSchemeOptions
{
    public string? SharedApiKey { get; set; }

    public ICollection<IRequestApiKeyProvider> RequestApiKeyProviders { get; set; } 

    public SharedApiKeyAuthenticationOptions()
    {
		    RequestApiKeyProviders = new HashSet<IRequestApiKeyProvider>();
        RequestApiKeyProviders.Add(new RequestHeaderApiKeyProvider());
    }
}
```

- Create a custom authentication handler.

```csharp
 public class SharedApiKeyAuthenticationHandler : AuthenticationHandler<SharedApiKeyAuthenticationOptions>
 {
     public SharedApiKeyAuthenticationHandler(
         IOptionsMonitor<SharedApiKeyAuthenticationOptions> options,
         ILoggerFactory logger,
         UrlEncoder encoder) : base(options, logger, encoder)
     {
     }

     protected override async Task<AuthenticateResult> HandleAuthenticateAsync()
     {
         ProviderApiKeyResult? providerApiKeyResult = null;
         if (Options.RequestApiKeyProviders != null)
         {
             foreach (var provider in Options.RequestApiKeyProviders)
             {
                 providerApiKeyResult = await provider.DetermineProviderApiKeyResult(Context);
                 if (providerApiKeyResult != null)
                 {
                     break;
                 }
             }
         }

         if (providerApiKeyResult == null)
         {
             return AuthenticateResult.NoResult();
         }

         if (providerApiKeyResult.ApiKey != Options.SharedApiKey)
         {
             return AuthenticateResult.Fail("API Key does not match");
         }
         var claimIdentity = new ClaimsIdentity("SharedAPIKey");
         claimIdentity.AddClaim(new Claim("Key", providerApiKeyResult.ApiKey));
         var claimPrincipal = new ClaimsPrincipal(claimIdentity);
         return AuthenticateResult.Success(new AuthenticationTicket(claimPrincipal, Scheme.Name));
     }
 }

```

- Configure new  authentication scheme with custom authentication handler.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication("ApiKey")
        AddScheme<SharedApiKeyAuthenticationOptions, SharedApiKeyAuthenticationHandler>("ApiKey", option => option.SharedApiKey = "Test");
    ****// ... other services
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseAuthentication();
    // ... other middleware
}

```

- Configure controllers to use shared api key authentication scheme.

```csharp
[ApiController]
[Route("[controller]")]
[Authorize(AuthenticationSchemes = "ApiKey")]
public class ValuesController : ControllerBase
{
    // ... your actions
}
```

## Considerations for using shared API key authentication

1. **Never Expose API Keys in Public Repositories**: Avoid hardcoding API keys in your codebase, especially if the code is stored in public repositories. This is one of the most common mistakes that can lead to key exposure.
2. **Use Environment Variables**: Store your API keys in environment variables or configuration files that are not included in your version control system. Ensure that these files are listed in your `.gitignore` to prevent accidental exposure.
3. **Implement Key Rotation**: Regularly rotate your API keys to minimize the risk of an old key being exploited. Update the keys in your application to match the new keys.
4. **Restrict Key Permissions**: Limit what each API key can do by setting permissions. For instance, if a key only needs to read data, it shouldn't have write permissions.
5. **Secure Key Storage**: Use a secure storage solution for your API keys, such as a secrets management tool, hardware security module (HSM), or encrypted environment variables.
6. **Access Control**: Ensure that only authorized personnel have access to API keys. Implement proper access controls and audit who has access to the keys.
7. **Monitor Usage**: Keep track of how and when your API keys are used. This can help you detect unusual patterns that may indicate a security issue.
8. **Use HTTPS**: Always use HTTPS to encrypt the transmission of your API keys over the network.
9. **Consider Using Tokens**: For additional security, consider using tokens, which can be generated and validated dynamically, instead of static API keys.

Reference:

[https://curity.io/resources/learn/the-api-security-maturity-model/#:~:text=API Security Model%3A-,Level 0%3A API Keys and Basic Authentication,3%3A Centralized Trust Using Claims](https://curity.io/resources/learn/the-api-security-maturity-model/#:~:text=API%20Security%20Model%3A-,Level%200%3A%20API%20Keys%20and%20Basic%20Authentication,3%3A%20Centralized%20Trust%20Using%20Claims)

[https://www.devopsdigest.com/api-security-levels](https://www.devopsdigest.com/api-security-levels)

[https://gist.github.com/code-atom/2bebe459c756f15a519c3136b5be1a89](https://gist.github.com/code-atom/2bebe459c756f15a519c3136b5be1a89)