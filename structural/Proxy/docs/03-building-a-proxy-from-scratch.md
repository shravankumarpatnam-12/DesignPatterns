# Building a Proxy From Scratch

## The Goal

Most tutorials stop after showing:

```java
new DocumentProxy(
    new RealDocument()
);
```

That demonstrates the pattern.

It does not prepare you for real systems.

Production proxies must handle:

* Security
* Lazy loading
* Caching
* Thread safety
* Error handling
* Resource management

Let's build proxies the way they appear in enterprise applications.

---

# Step 1: Define the Subject

Every Proxy starts with a shared contract.

```java
public interface ReportService {

    String generateReport();
}
```

The client depends only on:

```java
ReportService
```

This is critical.

Without an abstraction:

```text
No transparent proxying.
```

---

# Step 2: Create the Real Subject

```java
public class RealReportService
        implements ReportService {

    @Override
    public String generateReport() {

        return "Financial Report";
    }
}
```

This class contains:

```text
Business Logic
```

and nothing else.

---

# Step 3: Create a Protection Proxy

Requirement:

```text
Only ADMIN users can generate reports.
```

---

## Implementation

```java
public class SecurityProxy
        implements ReportService {

    private final ReportService service;

    public SecurityProxy(
            ReportService service) {

        this.service = service;
    }

    @Override
    public String generateReport() {

        if(!isAdmin()) {

            throw new SecurityException(
                "Access denied"
            );
        }

        return service.generateReport();
    }

    private boolean isAdmin() {

        return true;
    }
}
```

Usage:

```java
ReportService service =
    new SecurityProxy(
        new RealReportService()
    );
```

Flow:

```text
Security Check

↓

Real Service
```

---

# Why This Is Better

Without proxy:

```java
RealReportService
```

must contain:

```text
Business Logic

Security Logic
```

Violation of:

```text
Single Responsibility Principle
```

Proxy separates concerns.

---

# Step 4: Virtual Proxy

One of the most common proxy types.

---

## Problem

Suppose:

```java
HighResolutionVideo
```

requires:

```text
10 seconds loading

Large memory consumption
```

Creating it immediately is expensive.

---

## Real Object

```java
public class RealVideo
        implements Video {

    public RealVideo() {

        loadVideo();
    }

    @Override
    public void play() {

        System.out.println(
            "Playing video"
        );
    }
}
```

---

## Virtual Proxy

```java
public class VideoProxy
        implements Video {

    private RealVideo video;

    @Override
    public void play() {

        if(video == null) {

            video = new RealVideo();
        }

        video.play();
    }
}
```

Usage:

```java
Video video =
        new VideoProxy();
```

Object creation:

```text
Deferred
```

until:

```java
play()
```

is called.

---

# Why Lazy Loading Matters

Imagine:

```text
1000 Images
```

displayed in a gallery.

Without proxy:

```text
1000 Images Loaded
```

immediately.

Huge memory waste.

---

With proxy:

```text
Load Only Viewed Images
```

Massive performance improvement.

---

# Step 5: Caching Proxy

Another extremely common enterprise pattern.

---

## Problem

Database query:

```java
exchangeRateService
```

takes:

```text
500 ms
```

Repeated calls:

```text
500 ms

500 ms

500 ms
```

Wasteful.

---

## Proxy Solution

```java
public class CachedExchangeRateProxy
        implements ExchangeRateService {

    private final ExchangeRateService service;

    private Double cachedValue;

    public CachedExchangeRateProxy(
            ExchangeRateService service) {

        this.service = service;
    }

    @Override
    public double getRate() {

        if(cachedValue == null) {

            cachedValue =
                service.getRate();
        }

        return cachedValue;
    }
}
```

Flow:

```text
Cache Check

↓

Cache Miss

↓

Real Service

↓

Store Result
```

Subsequent requests:

```text
Cache Hit
```

No database access.

---

# The Cache Invalidation Problem

Every architect knows:

```text
Caching is easy.

Cache invalidation is hard.
```

Suppose exchange rates change.

Current cache:

```text
Outdated
```

Now proxy must support:

```text
TTL

Refresh

Eviction
```

Real caching systems become significantly more complex.

---

# Step 6: Logging Proxy

Requirement:

```text
Audit all requests.
```

Implementation:

```java
public class LoggingProxy
        implements ReportService {

    private final ReportService service;

    @Override
    public String generateReport() {

        System.out.println(
            "Generating report"
        );

        return service.generateReport();
    }
}
```

---

# Is This Really Proxy?

Technically:

```text
Yes
```

if logging exists to:

```text
Control

Monitor

Govern Access
```

However:

```text
Decorator may be a better fit.
```

Intent matters.

We'll discuss this later.

---

# Step 7: Combining Proxies

Real systems often use multiple proxies.

Example:

```java
ReportService service =
    new SecurityProxy(
        new CachedProxy(
            new RealReportService()
        )
    );
```

Flow:

```text
Security

↓

Cache

↓

Real Service
```

Each layer controls a different concern.

---

# Thread Safety Problem

Consider:

```java
if(video == null) {

    video =
        new RealVideo();
}
```

Two threads execute simultaneously.

Result:

```text
Two RealVideo Objects
```

created.

Unexpected behavior.

---

# Safe Lazy Initialization

Example:

```java
public synchronized void play() {

    if(video == null) {

        video =
            new RealVideo();
    }

    video.play();
}
```

Or:

```java
Double Checked Locking
```

for higher performance.

---

# Error Handling Proxy

Suppose remote calls fail.

Proxy can protect clients.

Example:

```java
public class RetryProxy
        implements Service {
}
```

Flow:

```text
Call Service

↓

Failure

↓

Retry
```

This improves reliability.

---

# Circuit Breaker Style Proxy

Advanced proxy:

```text
Too Many Failures
```

↓

```text
Stop Calling Service
```

↓

```text
Return Fallback
```

Many resilience frameworks are built around this idea.

---

# Common Mistake #1

Business Logic Inside Proxy

Bad:

```java
SecurityProxy
```

contains:

```text
Tax Calculation

Discount Rules

Pricing Logic
```

Proxy should manage access.

Business logic belongs elsewhere.

---

# Common Mistake #2

Proxy Without Interface

Bad:

```java
new SecurityProxy(
    new RealReportService()
);
```

but client depends directly on:

```java
RealReportService
```

No transparency.

No substitution.

The pattern loses value.

---

# Common Mistake #3

Creating God Proxies

Bad:

```text
Security

Caching

Retry

Metrics

Logging

Monitoring

Transactions
```

inside one proxy.

Violates SRP.

Split responsibilities.

---

# Common Mistake #4

Forgetting Delegation

Bad:

```java
public String generateReport() {

    return "Access Granted";
}
```

Real object never executes.

Proxy becomes replacement instead of intermediary.

---

# The Proxy Checklist

A proper proxy should:

✅ Implement the same interface

✅ Hold a reference to the real object

✅ Control access

✅ Delegate work

✅ Remain transparent to clients

✅ Focus on one responsibility

---

# What Architects Notice

Junior developers see:

```java
SecurityProxy
```

Architects see:

```text
Policy Enforcement Point
```

Junior developers see:

```java
VideoProxy
```

Architects see:

```text
Resource Optimization Layer
```

Junior developers see:

```java
RemoteProxy
```

Architects see:

```text
Network Abstraction Boundary
```

The implementation matters less than the responsibility being controlled.

---

# The Architectural Insight

Proxy is one of the first patterns that teaches:

```text
Business Logic

≠

Infrastructure Concerns
```

Security.

Caching.

Remoting.

Transactions.

Monitoring.

These concerns should surround business logic, not live inside it.

Proxy provides one mechanism for achieving that separation.

---

# Key Takeaways

* Production proxies commonly handle security, lazy loading, caching, and remoting.
* Proxy delegates work while controlling access.
* Virtual proxies enable lazy initialization.
* Caching proxies improve performance but introduce cache invalidation challenges.
* Thread safety becomes important in lazy-loading proxies.
* Proxy should focus on access and infrastructure concerns, not business logic.
* Enterprise frameworks frequently build proxy chains around business services.
