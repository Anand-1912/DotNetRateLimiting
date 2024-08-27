
## Ratelimiter in Asp.Net Core

1. Ratelimiting Middleware was introdcued in `.Net 7`.

2. Add the Rate Limiter Services and define the Policy

- builder.Services.AddRateLimiter(options => { });

3. Use the Middleware

- app.UseRateLimiter();

### RateLimiting Algorithm

#### Fixed Window Rate Limiting

The AddFixedWindowLimiter method uses a fixed time window to limit requests. 
When the time window expires, a new time window starts and the request limit is reset.

```chsarp

builder.Services.AddRateLimiter(options => 
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;
    options.AddFixedWindowLimiter("fixed", o =>
    {
        o.PermitLimit = 10;
        o.Window = TimeSpan.FromSeconds(10);
    });
});

/*Here "fixed" is the policy name

The above policy accepts a maximum of 10 requests in a timespan of 10 seconds.

*/

app.MapControllers().RequireRateLimiting("fixed"); // applying the policy globally

```

```csharp

// Ratelimiting based on the IP Address of the client. (Partitioning based on IP Address)

builder.Services.AddRateLimiter(options => 
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;
    options.
    AddPolicy(
        policyName: "fixed",
        partitioner: httpContext => RateLimitPartition.GetFixedWindowLimiter
        (
        partitionKey: httpContext.Connection.RemoteIpAddress?.ToString(),
        factory: _ => new FixedWindowRateLimiterOptions()
            {
                PermitLimit = 10,
                Window = TimeSpan.FromSeconds(10)
            })
        );
});

```
