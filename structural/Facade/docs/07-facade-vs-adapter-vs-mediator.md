# Facade vs Adapter vs Mediator

## The Most Common Design Pattern Confusion

Ask developers:

> What's the difference between Facade, Adapter, and Mediator?

You'll often hear:

```text
They all sit between objects.
```

Technically true.

Architecturally wrong.

These patterns may look similar on class diagrams, but they solve completely different problems.

Understanding the distinction is one of the easiest ways to separate pattern memorization from actual design knowledge.

---

# Why Patterns Get Confused

All three patterns introduce an additional layer.

```text
Client
   |
   v
Something
   |
   v
Other Objects
```

Because their structures appear similar, developers assume their purposes are similar.

The reality:

```text
Facade  → Simplifies

Adapter → Converts

Mediator → Coordinates
```

Everything else follows from these three words.

---

# Facade: Simplify Complexity

## Intent

Facade provides a simpler interface to a complex subsystem.

---

## Problem

A subsystem has many moving parts.

Example:

```text
Inventory Service

Payment Service

Shipping Service

Notification Service
```

Clients must understand too much.

---

## Solution

Introduce:

```text
CheckoutFacade
```

Client:

```java
checkoutFacade.checkout(order);
```

instead of:

```java
inventory.reserve();

payment.process();

shipping.create();

notification.send();
```

---

## Key Question

Facade answers:

> How can I make a complex subsystem easier to use?

---

# Adapter: Convert Interfaces

## Intent

Adapter converts one interface into another interface expected by clients.

---

## Problem

Two systems cannot communicate because their interfaces differ.

Example:

Your application expects:

```java
public interface PaymentProcessor {

    void pay();
}
```

Third-party library provides:

```java
public class StripeGateway {

    void makePayment();
}
```

Interfaces don't match.

---

## Solution

Adapter bridges the gap.

```java
public class StripeAdapter
        implements PaymentProcessor {

    private StripeGateway stripe;

    @Override
    public void pay() {

        stripe.makePayment();
    }
}
```

Now the application can use Stripe without modification.

---

## Key Question

Adapter answers:

> How can incompatible interfaces work together?

---

# Mediator: Coordinate Communication

## Intent

Mediator centralizes communication between objects.

---

## Problem

Objects communicate directly with one another.

Dependencies explode.

Example:

```text
User

Chat Room

Notification Service

Message Service

Presence Service
```

Everyone talks to everyone.

Relationships become difficult to manage.

---

## Solution

Introduce:

```text
ChatMediator
```

All communication flows through it.

```text
User A
   |
   v
Mediator
   |
   v
User B
```

Objects stop communicating directly.

---

## Key Question

Mediator answers:

> How can I reduce communication complexity between collaborating objects?

---

# Side-by-Side Comparison

## Facade

### Purpose

Simplify a subsystem.

### Focus

Client experience.

### Relationship

One-way.

```text
Client
   |
Facade
   |
Subsystem
```

### Goal

Hide complexity.

---

## Adapter

### Purpose

Convert interfaces.

### Focus

Compatibility.

### Relationship

One-to-one mapping.

```text
Client
   |
Adapter
   |
Third-Party System
```

### Goal

Make incompatible interfaces work together.

---

## Mediator

### Purpose

Coordinate interactions.

### Focus

Object collaboration.

### Relationship

Many-to-many communication.

```text
Object A
    |
Object B
    |
Mediator
    |
Object C
```

### Goal

Reduce communication dependencies.

---

# Real Enterprise Example

Imagine integrating a payment provider.

---

## Using Adapter

Your application expects:

```java
processPayment()
```

Provider exposes:

```java
executeTransaction()
```

Use Adapter.

Purpose:

```text
Interface Conversion
```

---

## Using Facade

Payment workflow requires:

```text
Fraud Check

Payment Authorization

Payment Capture

Notification
```

Expose:

```java
paymentFacade.pay();
```

Purpose:

```text
Complexity Simplification
```

---

## Using Mediator

Payment components communicate:

```text
Fraud Service

Risk Engine

Payment Service

Audit Service
```

Use Mediator.

Purpose:

```text
Communication Coordination
```

---

# Visual Memory Trick

If you forget the difference, remember:

---

## Facade

Many things → One simple interface

```text
Complex → Simple
```

---

## Adapter

One interface → Another interface

```text
A → B
```

---

## Mediator

Many objects → One coordinator

```text
Many ↔ One
```

---

# Can They Work Together?

Absolutely.

Large systems often combine them.

Example:

```text
API Gateway
        |
        v
CheckoutFacade
        |
        +--> StripeAdapter
        |
        +--> FraudMediator
```

Each pattern solves a different problem.

Patterns are complementary.

Not competitors.

---

# Interview Scenario

Question:

> We integrated a third-party payment provider whose API doesn't match our application API.

Answer:

```text
Adapter
```

---

Question:

> We have five services involved in checkout and want one simple entry point.

Answer:

```text
Facade
```

---

Question:

> Multiple components communicate heavily and dependencies are becoming difficult to manage.

Answer:

```text
Mediator
```

---

# The Architect's View

Junior developers often focus on:

```text
Class Diagrams
```

Architects focus on:

```text
Intent
```

Because two patterns can look similar while solving completely different problems.

The question is never:

> What does the diagram look like?

The question is:

> What problem are we solving?

---

# Decision Framework

Choose Facade when:

```text
Subsystem complexity is leaking to clients.
```

---

Choose Adapter when:

```text
Interfaces are incompatible.
```

---

Choose Mediator when:

```text
Communication dependencies are exploding.
```

---

# Key Takeaways

* Facade simplifies complex subsystems.
* Adapter converts incompatible interfaces.
* Mediator coordinates communication between objects.
* Similar structures do not imply similar intent.
* Always choose patterns based on the problem being solved.
* Architects think in terms of intent, not diagrams.

---

# Chapter Summary

Facade, Adapter, and Mediator are frequently confused because they all introduce an intermediary layer.

However, their purposes are fundamentally different:

```text
Facade  → Simplify

Adapter → Convert

Mediator → Coordinate
```

If you remember these three words, you'll correctly identify the right pattern in most real-world situations.

More importantly, you'll start thinking like an architect—focusing on problems and intent rather than memorizing class structures.
