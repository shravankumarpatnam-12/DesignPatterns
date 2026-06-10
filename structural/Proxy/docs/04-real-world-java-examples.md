# Real-World Java Examples

## Why This Chapter Matters

Many developers learn Proxy through:

```text id="tazr7h"
ImageProxy

VideoProxy

DocumentProxy
```

Then never recognize it in production systems.

The reality is completely different.

If you work with:

* Spring Boot
* Spring AOP
* Spring Security
* Hibernate
* JPA
* REST Clients
* Distributed Systems

you are using Proxy constantly.

Often without realizing it.

---

# Example 1: Spring @Transactional

One of the most important examples.

Developers write:

```java id="r1d6ut"
@Service
public class PaymentService {

    @Transactional
    public void processPayment() {

    }
}
```

Looks simple.

---

## What Developers Think

```text id="mjlwm81"
Client

↓

PaymentService
```

---

## What Actually Happens

Conceptually:

```text id="mjlwm82"
Client

↓

TransactionProxy

↓

PaymentService
```

Execution:

```text id="mjlwm83"
Begin Transaction

↓

Call Service

↓

Commit

or

Rollback
```

---

## Why Proxy?

The service should focus on:

```text id="mjlwm84"
Business Logic
```

not:

```text id="mjlwm85"
Transaction Management
```

Proxy separates concerns.

---

# Example 2: Spring Security

Suppose:

```java id="mjlwm86"
@PreAuthorize("hasRole('ADMIN')")
```

Developers see:

```java id="mjlwm87"
deleteUser()
```

Architects see:

```text id="mjlwm88"
Security Proxy

↓

Business Service
```

Execution:

```text id="mjlwm89"
Authorization Check

↓

Service Call
```

Without modifying service code.

---

# Example 3: Spring AOP

Consider:

```java id="mjlwm90"
@Around
```

Advice.

Example:

```java id="mjlwm91"
@LogExecutionTime
```

Developers think:

```text id="mjlwm92"
Magic
```

Reality:

```text id="mjlwm93"
Proxy Intercepts Method

↓

Runs Advice

↓

Calls Target
```

Proxy is the foundation of Spring AOP.

---

# Example 4: Hibernate Lazy Loading

One of the most famous proxy examples.

Entity:

```java id="mjlwm94"
@Entity
public class Order {

    @ManyToOne(fetch = LAZY)
    private Customer customer;
}
```

---

## What Developers Expect

```java id="mjlwm95"
order.getCustomer();
```

returns:

```java id="mjlwm96"
Customer
```

---

## What Actually Happens

Initially:

```text id="mjlwm97"
CustomerProxy
```

is returned.

The real customer is not loaded.

---

## First Access

```java id="mjlwm98"
customer.getName();
```

Proxy executes:

```text id="mjlwm99"
Database Query

↓

Load Customer

↓

Return Name
```

This is:

```text id="mjlwm100"
Virtual Proxy
```

in action.

---

# Why Hibernate Uses Proxy

Without proxy:

```text id="mjlwm101"
Every relationship loads immediately.
```

Potential result:

```text id="mjlwm102"
Huge memory usage

Slow queries

N+1 problems
```

Proxy enables:

```text id="mjlwm103"
Lazy Loading
```

which dramatically improves performance.

---

# Example 5: REST Clients

Developers write:

```java id="mjlwm104"
weatherClient.getForecast();
```

Looks local.

Reality:

```text id="mjlwm105"
HTTP Request

↓

Serialization

↓

Authentication

↓

Network Call

↓

Deserialization
```

The client sees:

```java id="mjlwm106"
WeatherService
```

The implementation is often a:

```text id="mjlwm107"
Remote Proxy
```

---

# Example 6: OpenFeign

Spring Cloud example:

```java id="mjlwm108"
@FeignClient("weather-service")
public interface WeatherClient {

    Forecast getForecast();
}
```

Notice:

```text id="mjlwm109"
Interface Only
```

No implementation exists.

---

## What Spring Creates

At runtime:

```text id="mjlwm110"
Feign Proxy
```

Execution:

```text id="mjlwm111"
Method Call

↓

HTTP Request

↓

Remote Service
```

Proxy hides networking complexity completely.

---

# Example 7: API Gateway

Modern architectures often contain:

```text id="mjlwm112"
Client

↓

API Gateway

↓

Microservice
```

Gateway responsibilities:

```text id="mjlwm113"
Authentication

Authorization

Rate Limiting

Routing

Monitoring
```

Architecturally:

```text id="mjlwm114"
Protection Proxy
```

for downstream services.

---

# Example 8: Caching Layer

Suppose:

```java id="mjlwm115"
ProductService
```

requires:

```text id="mjlwm116"
Database Access
```

for every request.

Proxy:

```java id="mjlwm117"
CachingProductServiceProxy
```

Flow:

```text id="mjlwm118"
Cache Check

↓

Cache Hit

↓

Return Result
```

or

```text id="mjlwm119"
Cache Miss

↓

Database

↓

Store Result
```

Business service remains unchanged.

---

# Example 9: Connection Pools

Developers think:

```java id="mjlwm120"
Connection connection =
        dataSource.getConnection();
```

returns:

```text id="mjlwm121"
Real Database Connection
```

Often it returns:

```text id="mjlwm122"
Connection Proxy
```

Why?

Because:

```java id="mjlwm123"
connection.close();
```

should not actually:

```text id="mjlwm124"
Close Connection
```

Instead:

```text id="mjlwm125"
Return To Pool
```

Proxy intercepts the call.

---

# Example 10: Logging Frameworks

Consider:

```java id="mjlwm126"
logger.info("Hello");
```

Sometimes the logger is wrapped by proxies that:

```text id="mjlwm127"
Filter Messages

Route Messages

Buffer Messages
```

before reaching the real destination.

---

# Example 11: Security Frameworks

Many frameworks implement:

```text id="mjlwm128"
Permission Checks
```

through proxies.

Flow:

```text id="mjlwm129"
User Request

↓

Security Proxy

↓

Protected Resource
```

The resource remains unaware of security policies.

---

# Example 12: Distributed Systems

Service:

```java id="mjlwm130"
inventoryService.checkStock();
```

Looks local.

Reality:

```text id="mjlwm131"
Network

Retries

Timeouts

Circuit Breakers

Serialization
```

The local interface is often backed by a:

```text id="mjlwm132"
Remote Proxy
```

This abstraction is essential in distributed architectures.

---

# Example 13: Circuit Breakers

Frameworks such as resilience libraries conceptually build:

```text id="mjlwm133"
CircuitBreakerProxy
```

Flow:

```text id="mjlwm134"
Request

↓

Failure Threshold Reached

↓

Reject Request

↓

Return Fallback
```

The target service may never execute.

Classic access control behavior.

---

# Example 14: Monitoring and Observability

Proxy intercepts:

```java id="mjlwm135"
service.execute();
```

and records:

```text id="mjlwm136"
Latency

Error Rate

Throughput
```

before delegating.

This is how many monitoring tools integrate without modifying business code.

---

# The Pattern Hidden Everywhere

Once you understand Proxy, you'll repeatedly encounter:

```text id="mjlwm137"
Intercept

Control

Delegate
```

Different names:

```text id="mjlwm138"
Proxy

Gateway

Stub

Client

Interceptor

Generated Client
```

Same fundamental idea.

---

# What Architects Notice

Junior developers see:

```java id="mjlwm139"
@Transactional
```

Architects see:

```text id="mjlwm140"
Transaction Proxy
```

---

Junior developers see:

```java id="mjlwm141"
@FeignClient
```

Architects see:

```text id="mjlwm142"
Remote Proxy
```

---

Junior developers see:

```java id="mjlwm143"
LAZY Fetch
```

Architects see:

```text id="mjlwm144"
Virtual Proxy
```

---

The framework changes.

The pattern remains.

---

# The Big Insight

Proxy is not important because of:

```java id="mjlwm145"
DocumentProxy
```

Proxy is important because it teaches:

```text id="mjlwm146"
How infrastructure concerns
can surround business logic
without becoming business logic.
```

That lesson powers:

* Spring AOP
* Spring Transactions
* Spring Security
* Hibernate Lazy Loading
* Remote Clients
* API Gateways
* Resilience Frameworks

Understanding Proxy unlocks a huge portion of modern Java architecture.

---

# Key Takeaways

* Spring transactions are commonly implemented through proxies.
* Spring Security frequently relies on protection proxies.
* Hibernate lazy loading is a classic virtual proxy example.
* Feign clients act as remote proxies.
* API gateways often behave as protection proxies.
* Connection pools use proxies to manage resources.
* Proxy is one of the most heavily used patterns in enterprise Java systems.
