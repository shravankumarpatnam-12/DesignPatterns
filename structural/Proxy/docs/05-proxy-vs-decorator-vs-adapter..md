# Proxy vs Decorator vs Adapter

## Why This Chapter Matters

This is where many developers struggle.

Consider these classes:

```java
class LoggingDecorator
        implements Service
```

```java
class SecurityProxy
        implements Service
```

```java
class ServiceAdapter
        implements Service
```

All three:

```text
Implement an interface

Contain another object

Delegate calls
```

Structurally:

```text
Almost identical.
```

Yet they solve completely different problems.

Architects care far more about:

```text
Intent
```

than structure.

---

# The Most Important Rule

When comparing patterns:

```text
Structure can be similar.

Intent is everything.
```

This single idea will save you from many interview mistakes.

---

# Proxy

## Purpose

Proxy controls:

```text
Access
```

to an object.

Question:

```text
Should this request reach
the real object?
```

Examples:

```text
Security

Caching

Lazy Loading

Remote Access

Transactions
```

---

## Mental Model

```text
Gatekeeper
```

Proxy stands in front of the real object.

---

## Example

```java
reportService.generateReport();
```

Actual flow:

```text
Security Check

↓

Real Service
```

The goal is:

```text
Control
```

not enhancement.

---

# Decorator

## Purpose

Decorator adds:

```text
Responsibilities
```

to an object.

Question:

```text
How can I add capabilities
without modifying code?
```

Examples:

```text
Logging

Metrics

Auditing

Compression

Encryption
```

---

## Mental Model

```text
Enhancer
```

Decorator enriches behavior.

---

## Example

```java
new LoggingDecorator(
    service
);
```

Flow:

```text
Log

↓

Service
```

Goal:

```text
Additional behavior
```

not access control.

---

# Adapter

## Purpose

Adapter solves:

```text
Incompatible Interfaces
```

Question:

```text
How can these two classes
work together?
```

---

## Example

Existing code expects:

```java
PaymentProcessor
```

Third-party library provides:

```java
StripeClient
```

Different APIs.

Adapter translates.

---

## Mental Model

```text
Translator
```

---

## Example

```java
PaymentProcessor processor =
    new StripeAdapter(
        stripeClient
    );
```

Goal:

```text
Compatibility
```

not enhancement.

not access control.

---

# Visual Comparison

## Proxy

```text
Client

↓

Proxy

↓

Real Object
```

Purpose:

```text
Control Access
```

---

## Decorator

```text
Client

↓

Decorator

↓

Component
```

Purpose:

```text
Add Responsibility
```

---

## Adapter

```text
Client

↓

Adapter

↓

Incompatible Service
```

Purpose:

```text
Translate Interface
```

---

# The Famous Interview Trap

Suppose:

```java
class LoggingService
        implements Service
```

Question:

```text
Decorator or Proxy?
```

Answer:

```text
Depends on intent.
```

---

## If Purpose Is

```text
Add logging capability
```

Then:

```text
Decorator
```

---

## If Purpose Is

```text
Audit access before execution
```

Then:

```text
Proxy
```

Same code structure.

Different intent.

---

# Proxy vs Decorator

This is the most important comparison.

---

## Structural Similarity

Both:

```java
implements Service
```

Both:

```java
private Service service;
```

Both delegate.

---

## Intent Difference

Decorator asks:

```text
What additional behavior
can I provide?
```

Proxy asks:

```text
Can this request proceed?
```

---

## Example

Decorator:

```java
new MetricsDecorator(
    service
);
```

Goal:

```text
Collect Metrics
```

---

Proxy:

```java
new SecurityProxy(
    service
);
```

Goal:

```text
Authorize Access
```

---

## Architect Shortcut

Decorator:

```text
Enhances
```

Proxy:

```text
Protects
```

---

# Proxy vs Facade

Another common confusion.

---

## Facade Purpose

Simplify a subsystem.

Example:

```text
Inventory

Payment

Shipping

Notification
```

Client wants:

```java
orderFacade.placeOrder();
```

Facade coordinates complexity.

---

## Proxy Purpose

Proxy does not simplify.

Proxy controls access.

---

## Mental Model

Facade:

```text
Simplifier
```

Proxy:

```text
Gatekeeper
```

---

# Proxy vs Bridge

Bridge solves:

```text
Independent Evolution
```

Example:

```text
Shape

and

Rendering Engine
```

evolve separately.

---

Proxy solves:

```text
Access Mediation
```

Completely different problem.

---

# Proxy vs AOP

This is extremely important for Spring developers.

---

## Developer View

```java
@Transactional

@Cacheable

@PreAuthorize
```

Looks like annotations.

---

## Framework View

Often becomes:

```text
Proxy

↓

Target Object
```

Proxy intercepts method calls.

---

## Architect View

Many AOP implementations are:

```text
Proxy

+

Interception Logic
```

Proxy is usually the underlying mechanism.

---

# Proxy vs Middleware

Modern systems often use:

```text
Authentication

Authorization

Rate Limiting

Monitoring
```

before requests reach services.

Looks like:

```text
API Gateway
```

or

```text
Middleware
```

Architecturally:

```text
Protection Proxy
```

at a larger scale.

---

# Decision Framework

Ask these questions.

---

## Question 1

Do I need:

```text
Access Control?
```

Examples:

```text
Security

Transactions

Caching

Lazy Loading

Remoting
```

Use:

```text
Proxy
```

---

## Question 2

Do I need:

```text
Additional Capabilities?
```

Examples:

```text
Metrics

Logging

Auditing
```

Use:

```text
Decorator
```

---

## Question 3

Do I need:

```text
Interface Translation?
```

Use:

```text
Adapter
```

---

## Question 4

Do I need:

```text
Subsystem Simplification?
```

Use:

```text
Facade
```

---

# Real Enterprise Examples

## Hibernate Lazy Loading

Goal:

```text
Delay Database Access
```

Pattern:

```text
Proxy
```

---

## Spring Security

Goal:

```text
Authorize Access
```

Pattern:

```text
Proxy
```

---

## Java I/O Streams

Goal:

```text
Add Buffering

Add Compression
```

Pattern:

```text
Decorator
```

---

## Payment Gateway Integration

Goal:

```text
Translate Third Party API
```

Pattern:

```text
Adapter
```

---

## Order Management API

Goal:

```text
Hide Subsystem Complexity
```

Pattern:

```text
Facade
```

---

# What Architects Actually Think About

Junior developers ask:

```text
What pattern is this?
```

Architects ask:

```text
What problem is being solved?
```

The problem determines the pattern.

Not the code structure.

---

# The Bigger Insight

Several patterns share similar implementations:

```java
Interface

↓

Wrapper

↓

Delegation
```

The differentiator is:

```text
Intent
```

Proxy:

```text
Control
```

Decorator:

```text
Enhancement
```

Adapter:

```text
Translation
```

Facade:

```text
Simplification
```

If you remember only one thing from this chapter, remember that.

---

# Key Takeaways

* Proxy controls access to an object.
* Decorator adds responsibilities to an object.
* Adapter translates one interface into another.
* Facade simplifies subsystem interaction.
* Structure may look identical across patterns.
* Intent is the primary differentiator.
* Architects classify patterns based on the problem being solved, not class diagrams.
