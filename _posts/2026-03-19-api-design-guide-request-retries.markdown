---
title: 'API Design Guide: Request Retries'
date: 2026-03-19 19:41:00 +05:30
categories:
- API Design
- asp.net core
tags:
- api-design-pattern
- api-design
- asp.net core
layout: post
---

## Table of Contents

1. [Overview](#overview)
2. [Retry Criteria](#retry-criteria)
   - [HTTP Status Code Classification](#http-status-code-classification)
   - [HTTP Method Safety](#http-method-safety)
3. [Retry Strategies (Client-Side)](#retry-strategies-client-side)
   - [Naive Fixed Retry](#1-naive-fixed-retry)
   - [Exponential Backoff](#2-exponential-backoff)
   - [Exponential Backoff with Jitter](#3-exponential-backoff-with-jitter-recommended)
4. [Server-Side Guidance: Retry-After Header](#server-side-guidance-retry-after-header)
5. [Complete Recommended Implementation](#complete-recommended-implementation)
6. [Summary](#summary)

---

## Overview

In a distributed system, services are independently developed, deployed, and operated by different teams. Transient failures — due to network blips, downstream unavailability, or temporary overload — are unavoidable. Well-designed APIs must be built with retry behavior in mind so clients can recover gracefully without manual intervention.

**The goal is to serve as many requests as possible while minimizing unnecessary retries.** This requires answering three questions:

- **When** should a client retry?
- **How often** should it retry?
- **How long** should it wait between retries?

Retry logic is primarily implemented by the consumer (client), but the server can — and should — provide signals that make client retries more effective and safe.

---

## Retry Criteria

### HTTP Status Code Classification

Not all failures are worth retrying. Retrying on a `400 Bad Request` wastes resources and will never succeed; the request itself is broken. Retrying on a `503 Service Unavailable` is worthwhile because the failure is likely temporary.

| Category | Status Codes | Retry? | Rationale |
|---|---|---|---|
| **Retryable** | `500` Internal Server Error | Yes | Transient server fault |
| | `502` Bad Gateway | Yes | Upstream proxy/load balancer issue |
| | `503` Service Unavailable | Yes | Server temporarily overloaded or restarting |
| | `504` Gateway Timeout | Yes | Upstream dependency timed out |
| | `408` Request Timeout | Yes | Connection dropped; safe to resend |
| **Non-Retryable** | `400` Bad Request | No | Malformed request; retrying won't fix it |
| | `401` Unauthorized | No | Authentication required; obtain valid credentials first |
| | `403` Forbidden | No | Authorization failure; retrying won't change permissions |
| | `404` Not Found | No | Resource does not exist |
| | `422` Unprocessable Entity | No | Semantic validation error in the payload |
| **Conditionally Retryable** | `429` Too Many Requests | After delay | Rate limit hit; wait for the `Retry-After` window |
| | `503` with `Retry-After` | After delay | Server is explicitly signaling backpressure |

> **Rule of thumb:** Retry on server errors (`5xx`) and network-level timeouts. Never retry on client errors (`4xx`), except for `429`.

### HTTP Method Safety

The HTTP method matters as much as the status code. **Idempotent** methods can be retried freely because repeating the same request produces the same result. **Non-idempotent** methods require extra care.

| Method | Idempotent? | Safe to Retry? | Notes |
|---|---|---|---|
| `GET` | Yes | Yes | Read-only; no side effects |
| `PUT` | Yes | Yes | Replaces the full resource; repeating is safe |
| `DELETE` | Yes | Yes | Deleting an already-deleted resource is typically a no-op |
| `POST` | **No** | With caution | May create duplicate records; use **idempotency keys** |
| `PATCH` | No | With caution | Partial update; depends on operation semantics |

**Idempotency Keys for POST:** To safely retry `POST` requests, include a client-generated unique key in the request header (e.g., `Idempotency-Key: <uuid>`). The server stores the key and, on a duplicate request, returns the original response instead of processing it again.

```http
POST /v1/payments HTTP/1.1
Idempotency-Key: 8f14e45f-ceea-367f-a27e-3d305e49f8c8
Content-Type: application/json

{ "amount": 100, "currency": "USD" }
```

---

## Retry Strategies (Client-Side)

Each strategy builds on the previous one. Start simple; add complexity only when you have evidence it is needed.

### 1. Naive Fixed Retry

The simplest approach: retry up to _N_ times with a constant delay between attempts. We need to add number of retries and maximum delay duration as parameters. if retry duration is more than the maximum duration, we will give up. 

**Problem:** If many clients fail simultaneously (e.g., after a server restart), they all retry at the same intervals. This synchronized wave of traffic can overwhelm a recovering server — known as the **thundering herd problem**.

```
Attempt 1 → FAIL
Wait 1s
Attempt 2 → FAIL
Wait 1s
Attempt 3 → FAIL
→ Give up
```

Use this only for low-traffic, single-client scenarios.

---

### 2. Exponential Backoff

Double the wait time after each failure. This spaces out retry attempts and gives the server progressively more time to recover.

```
Attempt 1 → FAIL → wait 2s
Attempt 2 → FAIL → wait 4s
Attempt 3 → FAIL → wait 8s
→ Give up
```

**Remaining problem:** All clients experiencing the same failure will still calculate identical delays and retry in lock-step, because `2^n` is deterministic.

---

### 3. Exponential Backoff with Jitter (Recommended)

Add a random offset (jitter) to each backoff delay. This desynchronizes retries across clients, spreading the load over a window rather than a spike.

```
Client A: wait 2s + 312ms
Client B: wait 2s + 781ms  ← staggered, not a synchronized spike
Client C: wait 2s + 94ms
```

This is the **recommended baseline strategy** for all HTTP clients in our services.

---

## Server-Side Guidance: Retry-After Header

All prior strategies are reactive — the client guesses how long to wait. A better approach is to let the server communicate the expected recovery window explicitly via the `Retry-After` response header.

This is mandatory for `429 Too Many Requests` and strongly recommended for `503 Service Unavailable`.

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
```

**Always use a numeric duration (seconds), not an HTTP-date timestamp.**

Using a timestamp (e.g., `Retry-After: Wed, 19 Mar 2026 14:00:00 GMT`) introduces clock skew risk: if the client's clock differs from the server's, it may retry too early or wait far longer than necessary. A numeric value is always relative to *now*, eliminating this ambiguity.

### Server-Side: ASP.NET Core Middleware

The cleanest place to inject `Retry-After` is in a dedicated middleware that intercepts error responses before they are sent to the client. Set headers **before** writing the response body to avoid `Cannot modify headers after response has started` errors.

```csharp
// RetryAfterMiddleware.cs
public class RetryAfterMiddleware(RequestDelegate next)
{
    // Configurable durations; inject via IOptions in production.
    private const int RateLimitRetryAfterSeconds = 60;
    private const int ServerErrorRetryAfterSeconds = 30;

    public async Task InvokeAsync(HttpContext context)
    {
        // Register a callback that fires before headers are flushed to the client.
        // At this point the status code is set but the body has not been written yet.
        context.Response.OnStarting(() =>
        {
            var status = context.Response.StatusCode;
            if ((status == StatusCodes.Status429TooManyRequests ||
                 status == StatusCodes.Status503ServiceUnavailable ||
                 status == StatusCodes.Status504GatewayTimeout) &&
                !context.Response.Headers.ContainsKey("Retry-After"))
            {
                int seconds = status == StatusCodes.Status429TooManyRequests
                    ? RateLimitRetryAfterSeconds
                    : ServerErrorRetryAfterSeconds;

                context.Response.Headers["Retry-After"] = seconds.ToString();
            }

            return Task.CompletedTask;
        });

        await next(context);
    }
}

// Extension method for Program.cs registration
public static class RetryAfterMiddlewareExtensions
{
    public static IApplicationBuilder UseRetryAfter(this IApplicationBuilder app)
        => app.UseMiddleware<RetryAfterMiddleware>();
}
```

Register it early in the pipeline, before exception-handling middleware:

```csharp
// Program.cs
app.UseRetryAfter();
app.UseExceptionHandler("/error");
// ... rest of pipeline
```

---

## Complete Recommended Implementation

This single Polly policy covers all the strategies above: it retries on transient errors, applies exponential backoff with jitter as the default, and honors a server-provided `Retry-After` header when present.

```csharp
// Program.cs / service registration
using System.Net;
using Polly;
using Polly.Extensions.Http;

builder.Services.AddHttpClient("ApiClient", client =>
{
    client.BaseAddress = new Uri("https://api.example.com/");
    client.Timeout = TimeSpan.FromSeconds(30);
})
.AddPolicyHandler(BuildRetryPolicy());

static IAsyncPolicy<HttpResponseMessage> BuildRetryPolicy()
{
    // Use a single Random instance per policy to avoid seed collisions.
    var rng = new Random();

    return HttpPolicyExtensions
        // Handles: 5xx, 408 (Request Timeout), and network-level exceptions.
        .HandleTransientHttpError()
        // Also handle 429 Too Many Requests.
        .OrResult(r => r.StatusCode == HttpStatusCode.TooManyRequests)
        .WaitAndRetryAsync(
            retryCount: 5,
            sleepDurationProvider: (attempt, outcome, _) =>
            {
                // Priority 1: Honor the server's Retry-After directive.
                if (outcome.Result?.Headers.RetryAfter is { } retryAfter)
                {
                    if (retryAfter.Delta.HasValue)
                        return retryAfter.Delta.Value;

                    if (retryAfter.Date.HasValue)
                    {
                        var serverDelay = retryAfter.Date.Value - DateTimeOffset.UtcNow;
                        if (serverDelay > TimeSpan.Zero)
                            return serverDelay;
                    }
                }

                // Priority 2: Exponential backoff with jitter.
                // Delays: ~2s, ~4s, ~8s, ~16s, ~32s (plus up to 500ms jitter each).
                var backoff = TimeSpan.FromSeconds(Math.Pow(2, attempt));
                var jitter  = TimeSpan.FromMilliseconds(rng.Next(0, 500));
                return backoff + jitter;
            },
            onRetryAsync: (outcome, delay, attempt, _) =>
            {
                // Replace with your structured logger (ILogger) in production.
                Console.WriteLine(
                    $"[Retry {attempt}/5] Waiting {delay.TotalSeconds:F1}s. " +
                    $"Status: {outcome.Result?.StatusCode.ToString() ?? outcome.Exception?.Message}");
                return Task.CompletedTask;
            });
}
```

**Checklist before shipping:**

- [ ] Retry only on transient errors — `5xx`, `408`, network exceptions, and `429`.
- [ ] Never retry `4xx` responses (except `429`).
- [ ] Use idempotency keys on `POST` requests that may be retried.
- [ ] Keep `retryCount` bounded (5 is a reasonable maximum) to limit tail latency.
- [ ] Servers must return `Retry-After` on `429` and `503` responses.
- [ ] `Retry-After` values must be a numeric duration in seconds, not an HTTP-date.
- [ ] Log each retry attempt with attempt number, delay, and failure reason.

---

## Summary

Designing for retries is a shared contract between the server and its clients.

| Concern | Responsibility | Recommendation |
|---|---|---|
| Retry trigger | Client | Retry on `5xx`, `408`, network failures, `429` |
| Retry strategy | Client | Exponential backoff + jitter |
| Duplicate prevention | Client + Server | Idempotency keys for `POST` |
| Backpressure signaling | **Server** | `Retry-After` header (numeric seconds) on `429` / `503` |
| Retry budget | Client | Cap at 5 retries; respect total request timeout |

When every service in the system follows these conventions, transient failures become self-healing events rather than cascading outages.