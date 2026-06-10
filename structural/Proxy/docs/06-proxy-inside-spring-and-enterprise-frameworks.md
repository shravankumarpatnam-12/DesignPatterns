# Proxy Inside Spring and Enterprise Frameworks

## Why This Chapter Matters

Most developers learn Proxy through:

```java
ImageProxy

DocumentProxy

VideoProxy
```

These examples teach the pattern.

But they don't explain why Proxy became one of the most important patterns in enterprise Java.

The real reason is simple:

```text
Enterprise applications contain
many infrastructure concerns.
```

Examples:

* Transactions
* Security
* Caching
* Monitoring
* Retry
* Lazy Loading
* Remote Communication

Business services should not contain this logic.

Proxy became one of the primary mechanisms for solving this problem.

---

# The Enterprise Problem

Consider:

```java
@Service
public class PaymentService {

    public void processPayment() {

        // business logic
    }
}
```

Business responsibility:

```text
Process Payment
```

Now requirements arrive:

```text
Transactions

Security

Logging

Metrics

Retry

Caching
```

Naive implementation:

```java
public void processPayment() {

    beginTransaction();

    securityCheck();

    logRequest();

    // business logic

    commitTransaction();
}
```

Result:

```text
Business Logic

+

Infrastructure Logic
```

mixed together.

Poor separation of concerns.

---

# The Spring Solution

Instead of modifying:

```java
PaymentService
```

Spring creates:

```text
Proxy

↓

PaymentService
```

Infrastructure concerns live in the proxy.

Business logic remains clean.

---

# Spring Transaction Proxy

Consider:

```java
@Transactional
public void processPayment() {

}
```

Developers often think:

```text
Annotation performs magic.
```

Not really.

Conceptually Spring creates:

```text
TransactionProxy

↓

PaymentService
```

Execution:

```text
Begin Transaction

↓

Invoke Service

↓

Commit

or

Rollback
```

---

## Simplified View

Proxy logic:

```java
beginTransaction();

try {

    target.processPayment();

    commit();
}
catch(Exception e) {

    rollback();
}
```

Business service remains unchanged.

---

# Why Proxy Is Perfect Here

Transaction management is:

```text
Cross-Cutting Concern
```

It affects:

```text
Order Service

Payment Service

Inventory Service

User Service
```

Proxy allows reuse without duplication.

---

# Spring Security Proxy

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser() {

}
```

Conceptual execution:

```text
SecurityProxy

↓

UserService
```

Flow:

```text
Check Role

↓

Allow

or

Reject
```

Again:

```text
Control Access
```

Classic Proxy responsibility.

---

# Spring AOP

One of the biggest Proxy use cases.

Example:

```java
@Around
```

Advice:

```java
@LogExecutionTime
```

---

## Conceptual Flow

```text
Logging Proxy

↓

Business Service
```

Execution:

```text
Start Timer

↓

Invoke Target

↓

Calculate Duration
```

The service remains unaware.

---

# Dynamic Proxy Creation

At runtime Spring often creates proxies dynamically.

Developers write:

```java
@Service
public class PaymentService {
}
```

Spring may actually expose:

```text
PaymentServiceProxy
```

to other beans.

The proxy becomes the object that everyone interacts with.

---

# JDK Dynamic Proxy

Java provides built-in support.

Package:

```java
java.lang.reflect.Proxy
```

---

## Requirements

Target must implement an interface.

Example:

```java
public interface PaymentService {
}
```

---

## Runtime Generation

Java generates:

```text
PaymentServiceProxy
```

automatically.

No manual class creation required.

---

## Benefits

Frameworks can create:

```text
Security Proxies

Transaction Proxies

Logging Proxies
```

without developers writing classes.

This is foundational to Spring AOP.

---

# CGLIB Proxy

Problem:

```text
Not every class has an interface.
```

Example:

```java
public class PaymentService {
}
```

No interface exists.

JDK proxy cannot help.

---

## Spring Solution

Use:

```text
CGLIB
```

CGLIB creates:

```text
Subclass Proxy
```

Conceptually:

```java
class PaymentServiceProxy
        extends PaymentService
```

Method calls are intercepted.

---

# JDK Proxy vs CGLIB

## JDK Proxy

Requires:

```java
Interface
```

Proxy:

```text
Implements Interface
```

---

## CGLIB

Requires:

```java
Concrete Class
```

Proxy:

```text
Subclass
```

---

## Interview Shortcut

```text
JDK Proxy

→ Interface Based

CGLIB

→ Subclass Based
```

---

# Hibernate Lazy Loading

One of the most famous Proxy examples.

Entity:

```java
@Entity
public class Order {

    @ManyToOne(fetch = LAZY)
    private Customer customer;
}
```

---

## What Developers Think

```java
Customer customer =
    order.getCustomer();
```

returns:

```java
Customer
```

---

## What Actually Happens

Initially:

```text
CustomerProxy
```

is returned.

No database query yet.

---

## First Access

```java
customer.getName();
```

Proxy performs:

```text
SQL Query

↓

Load Customer

↓

Return Name
```

Classic:

```text
Virtual Proxy
```

---

# Why Hibernate Uses Proxy

Without proxy:

```text
Load Order

↓

Load Customer

↓

Load Address

↓

Load Country

↓

Load Currency
```

Massive object graph.

Huge performance cost.

Proxy enables:

```text
Load On Demand
```

which is critical for ORM performance.

---

# Feign Client Proxy

Developers write:

```java
@FeignClient("user-service")
public interface UserClient {

    User getUser(Long id);
}
```

No implementation exists.

---

## What Spring Creates

Runtime proxy.

Conceptually:

```text
UserClientProxy
```

Execution:

```text
Method Call

↓

HTTP Request

↓

Remote Service

↓

Deserialize Response
```

This is:

```text
Remote Proxy
```

---

# API Gateway

Architecture:

```text
Client

↓

Gateway

↓

Microservices
```

Responsibilities:

```text
Authentication

Authorization

Rate Limiting

Monitoring
```

Gateway often behaves as:

```text
Protection Proxy
```

for backend services.

---

# Resilience4j

Example:

```java
@CircuitBreaker
```

Conceptually:

```text
CircuitBreakerProxy

↓

Remote Service
```

Proxy decides:

```text
Should request proceed?
```

If service is unhealthy:

```text
Reject Request

Return Fallback
```

Classic access mediation.

---

# Caching

Example:

```java
@Cacheable
public Product getProduct() {
}
```

Conceptual flow:

```text
Caching Proxy

↓

Product Service
```

Execution:

```text
Cache Hit

↓

Return Value
```

or

```text
Cache Miss

↓

Invoke Service

↓

Store Result
```

Business service remains unchanged.

---

# The Layered Proxy Reality

A Spring bean may actually look like:

```text
Metrics Proxy

↓

Security Proxy

↓

Transaction Proxy

↓

Caching Proxy

↓

Business Service
```

Developers see:

```java
paymentService.processPayment();
```

Framework sees:

```text
Multiple Proxy Chain
```

This is why understanding Proxy is so important.

---

# What Architects Notice

Junior developers see:

```java
@Transactional
```

Architects see:

```text
Transaction Proxy
```

---

Junior developers see:

```java
@Cacheable
```

Architects see:

```text
Caching Proxy
```

---

Junior developers see:

```java
@FeignClient
```

Architects see:

```text
Remote Proxy
```

---

Junior developers see:

```java
LAZY
```

Architects see:

```text
Virtual Proxy
```

---

# The Bigger Insight

Proxy became a foundational enterprise pattern because it allows:

```text
Infrastructure

and

Business Logic
```

to evolve independently.

Without Proxy:

```text
Security Logic

Transaction Logic

Caching Logic

inside every service.
```

With Proxy:

```text
Infrastructure surrounds
business logic.
```

The result is cleaner, more maintainable systems.

---

# The Architectural Insight

Most enterprise Java frameworks are not built around:

```text
Inheritance
```

They are built around:

```text
Interception
```

And interception is frequently implemented using:

```text
Proxy
```

Understanding Proxy is therefore not merely understanding a design pattern.

It is understanding one of the fundamental mechanisms behind Spring, Hibernate, RPC frameworks, API gateways, and modern enterprise architecture.

---

# Key Takeaways

* Spring Transactions are commonly implemented using proxies.
* Spring Security frequently relies on protection proxies.
* Spring AOP is heavily proxy-based.
* JDK Dynamic Proxies are interface-based.
* CGLIB proxies are subclass-based.
* Hibernate lazy loading is a classic virtual proxy example.
* Feign clients are remote proxies.
* Modern enterprise frameworks use proxy chains extensively.
* Proxy is one of the foundational patterns behind Spring and Hibernate.
