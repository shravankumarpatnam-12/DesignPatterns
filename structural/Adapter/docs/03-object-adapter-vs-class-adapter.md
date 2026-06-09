# Object Adapter vs Class Adapter

## The Two Ways to Build an Adapter

In the previous chapter, we learned the mechanics of the Adapter Pattern.

We identified four participants:

```text
Client
Target
Adapter
Adaptee
```

However, there is an important design decision that most developers overlook:

> How should the Adapter connect to the Adaptee?

There are two possible approaches:

1. Object Adapter (Composition)
2. Class Adapter (Inheritance)

Both solve the same problem.

But they do so in very different ways.

Understanding this difference is crucial because modern enterprise Java overwhelmingly favors one approach.

---

# The Core Question

Suppose we have:

```java
public interface PaymentProcessor {

    PaymentResult process(PaymentRequest request);

}
```

And an incompatible third-party SDK:

```java
public class StripeSdk {

    public void makePayment(
            double amount,
            String currency) {

    }

}
```

How do we connect them?

Two options exist.

---

# Option 1: Object Adapter

Object Adapter uses:

```text
Composition
```

The adapter contains an instance of the adaptee.

```text
StripeAdapter
       ↓
    StripeSdk
```

Example:

```java
public class StripeAdapter
        implements PaymentProcessor {

    private final StripeSdk stripeSdk;

    public StripeAdapter(StripeSdk stripeSdk) {
        this.stripeSdk = stripeSdk;
    }

    @Override
    public PaymentResult process(
            PaymentRequest request) {

        stripeSdk.makePayment(
                request.getAmount(),
                request.getCurrency()
        );

        return PaymentResult.success();
    }
}
```

Notice:

```java
private final StripeSdk stripeSdk;
```

The adapter HAS-A StripeSdk.

This is composition.

---

# Structure of Object Adapter

```text
Client
   ↓
Target Interface
   ↓
Adapter
   ↓
Adaptee Instance
```

Or:

```text
OrderService
      ↓
PaymentProcessor
      ↓
StripeAdapter
      ↓
StripeSdk
```

The adapter delegates work to the adaptee.

---

# Advantages of Object Adapter

## 1. Loose Coupling

The adapter does not depend on implementation inheritance.

```text
Adapter
      ↓
Interface Contract
      ↓
Adaptee Instance
```

This makes systems easier to modify.

---

## 2. Supports Multiple Adaptees

Imagine:

```java
StripeSdk
PayPalSdk
RazorpaySdk
```

Each can have its own adapter.

```java
StripeAdapter
PayPalAdapter
RazorpayAdapter
```

No inheritance complexity.

---

## 3. Runtime Flexibility

The adaptee can be swapped dynamically.

```java
new StripeAdapter(stripeSdk);
```

or

```java
new StripeAdapter(mockStripeSdk);
```

Very useful for testing.

---

## 4. Works with Final Classes

Many third-party SDKs contain:

```java
public final class StripeSdk
```

Inheritance becomes impossible.

Composition still works.

---

## 5. Follows Modern Design Principles

Composition aligns with:

* SOLID
* Dependency Injection
* Spring Framework
* Clean Architecture
* Hexagonal Architecture

This is why Object Adapter dominates enterprise systems.

---

# Option 2: Class Adapter

Class Adapter uses:

```text
Inheritance
```

The adapter extends the adaptee.

Example:

```java
public class StripeAdapter
        extends StripeSdk
        implements PaymentProcessor {

    @Override
    public PaymentResult process(
            PaymentRequest request) {

        makePayment(
                request.getAmount(),
                request.getCurrency()
        );

        return PaymentResult.success();
    }
}
```

Notice:

```java
extends StripeSdk
```

The adapter IS-A StripeSdk.

---

# Structure of Class Adapter

```text
Client
   ↓
Target Interface
   ↓
Adapter
   ↓
Inheritance
   ↓
Adaptee
```

Or:

```text
OrderService
      ↓
PaymentProcessor
      ↓
StripeAdapter
      ↓
StripeSdk
```

The relationship is now inheritance-based.

---

# Advantages of Class Adapter

## 1. Less Delegation Code

You can directly call parent methods.

```java
makePayment(...)
```

instead of:

```java
stripeSdk.makePayment(...)
```

Slightly less code.

---

## 2. Direct Access to Protected Members

Inheritance allows access to:

```java
protected
```

members of the adaptee.

Sometimes useful.

---

# Problems with Class Adapter

Although class adapters appear simpler, they introduce several architectural problems.

---

## Problem 1: Tight Coupling

The adapter becomes tied to the adaptee's implementation.

```java
extends StripeSdk
```

This creates stronger dependency.

Changes in StripeSdk may affect the adapter.

---

## Problem 2: Cannot Adapt Multiple Classes

Java supports:

```java
Single Inheritance
```

only.

You cannot write:

```java
extends StripeSdk, PayPalSdk
```

Impossible.

---

## Problem 3: Difficult Testing

Mocking becomes harder.

You inherit behavior whether you want it or not.

Composition provides more control.

---

## Problem 4: Final Classes Break It

This immediately fails:

```java
public final class StripeSdk
```

because:

```java
Cannot extend final class
```

Many modern SDKs are intentionally final.

---

## Problem 5: Violates "Favor Composition Over Inheritance"

One of the most famous OO principles states:

> Favor object composition over class inheritance.

Class Adapter ignores this recommendation.

---

# Why Modern Java Prefers Object Adapter

Look at modern frameworks.

Spring:

```java
Bean Adapters
Handler Adapters
Resource Adapters
```

Mostly composition.

---

Cloud SDK Integrations:

```java
AWS Adapter
Azure Adapter
GCP Adapter
```

Mostly composition.

---

Microservices:

```java
REST Client Adapter
Kafka Adapter
Database Adapter
```

Mostly composition.

---

Hexagonal Architecture:

```text
Port
   ↓
Adapter
   ↓
External System
```

Entirely composition-based.

---

# Real Enterprise Example

Suppose your company uses:

```java
Amazon S3
```

Today.

Tomorrow management decides to migrate to:

```java
Azure Blob Storage
```

With Object Adapter:

```text
StorageService
      ↓
StorageAdapter
      ↓
S3Adapter
```

becomes

```text
StorageService
      ↓
StorageAdapter
      ↓
AzureAdapter
```

Business code remains unchanged.

---

With Class Adapter:

The design becomes much harder to evolve because the inheritance hierarchy is now coupled to a specific implementation.

---

# Comparison Table

| Aspect                 | Object Adapter | Class Adapter |
| ---------------------- | -------------- | ------------- |
| Technique              | Composition    | Inheritance   |
| Coupling               | Loose          | Tight         |
| Flexibility            | High           | Low           |
| Supports Final Classes | Yes            | No            |
| Testing                | Easy           | Harder        |
| Multiple Adaptees      | Easy           | Difficult     |
| Runtime Replacement    | Yes            | No            |
| Enterprise Usage       | Very Common    | Rare          |

---

# Interview Question

## Which Adapter Type Should We Use in Java?

Answer:

```text
Object Adapter
```

Reason:

```text
Java favors composition over inheritance.
```

Benefits:

* Better maintainability
* Better testing
* Better flexibility
* Better alignment with SOLID principles
* Better compatibility with Spring and modern frameworks

---

# Architect Perspective

Many developers think Adapter Pattern is the key concept.

Architects know something deeper.

The real lesson is:

> Composition creates adaptable systems.

Object Adapter succeeds because it relies on composition.

Class Adapter struggles because it relies on inheritance.

This principle appears repeatedly throughout software architecture.

You will see it again in:

* Strategy Pattern
* Decorator Pattern
* Bridge Pattern
* Dependency Injection
* Hexagonal Architecture

The Adapter Pattern is often the first place where developers truly understand why composition is so powerful.

---

# Key Takeaways

## Object Adapter

* Uses composition
* Holds a reference to the adaptee
* Most common implementation
* Preferred in enterprise Java

## Class Adapter

* Uses inheritance
* Extends the adaptee
* Less flexible
* Rarely used in modern systems

## Architect Perspective

The biggest lesson is not Adapter itself.

The biggest lesson is:

> Composition creates systems that can evolve.

This idea forms the foundation of modern software architecture.
