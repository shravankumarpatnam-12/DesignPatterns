# Simple Factory Pattern

## The First Attempt to Solve the Problem

In the previous chapter we discovered a critical issue:

```java
new Something()
```

creates coupling.

As systems grow, object creation logic becomes scattered across hundreds of classes.

The question becomes:

> Can we move object creation into a single place?

The simplest answer is:

> Yes. Create a dedicated class responsible for creating objects.

This idea is known as the **Simple Factory Pattern**.

---

# The Problem We Are Trying to Solve

Imagine an application that supports multiple payment providers.

Without a factory:

```java
public class OrderService {

    public void placeOrder(String country) {

        PaymentGateway gateway;

        if(country.equals("INDIA")) {
            gateway = new RazorpayGateway();
        }
        else if(country.equals("USA")) {
            gateway = new StripeGateway();
        }
        else {
            gateway = new PaypalGateway();
        }

        gateway.pay();
    }
}
```

Looks manageable.

Now imagine:

* 50 services
* 20 schedulers
* 30 event consumers

all containing the same logic.

A provider change becomes a massive migration project.

---

# First Refactoring

Move creation logic into a dedicated class.

```java
public class PaymentGatewayFactory {

    public static PaymentGateway create(String country) {

        if(country.equals("INDIA")) {
            return new RazorpayGateway();
        }

        if(country.equals("USA")) {
            return new StripeGateway();
        }

        return new PaypalGateway();
    }
}
```

Usage:

```java
PaymentGateway gateway =
        PaymentGatewayFactory.create(country);
```

The service no longer knows how the object is created.

---

# What Changed?

Before:

```text
OrderService
    ├── Business Logic
    └── Creation Logic
```

After:

```text
OrderService
    └── Business Logic

PaymentGatewayFactory
    └── Creation Logic
```

Responsibilities are separated.

This is the first architectural win.

---

# Why This Feels Better

Consider a new requirement:

```text
Canada → Stripe
```

Without factory:

```text
Modify:
    Service A
    Service B
    Service C
    Service D
    ...
```

With factory:

```text
Modify:
    PaymentGatewayFactory
```

One change.

One location.

One deployment validation.

This significantly reduces change amplification.

---

# The Core Idea

Simple Factory centralizes object creation.

Instead of:

```java
new RazorpayGateway()
```

appearing everywhere,

it appears in one place.

This improves:

* Maintainability
* Readability
* Discoverability

---

# UML View

```text
                +------------------+
                | PaymentGateway   |
                +------------------+
                         ^
                         |
        --------------------------------
        |              |              |
        |              |              |
+---------------+ +-------------+ +-------------+
| Razorpay      | | Stripe      | | Paypal      |
+---------------+ +-------------+ +-------------+

               ^
               |
               |
 +----------------------------+
 | PaymentGatewayFactory      |
 +----------------------------+
 | create(country)            |
 +----------------------------+
```

The factory becomes the central creation point.

---

# Why Developers Love Simple Factories

Benefits appear immediately.

## 1. Reduced Coupling

Before:

```java
OrderService
      ↓
StripeGateway
```

After:

```java
OrderService
      ↓
PaymentGateway
```

Less knowledge about implementation details.

---

## 2. Easier Maintenance

Creation rules live in one place.

Developers know exactly where to look.

---

## 3. Better Readability

Business code becomes cleaner.

Before:

```java
if(country.equals("INDIA")) {
   ...
}
else if(...)
```

After:

```java
gateway.pay();
```

Business intent becomes clearer.

---

## 4. Better Reuse

Multiple modules can use the same creation logic.

No duplication.

---

# Real World Example: Database Drivers

Without a factory:

```java
if(database.equals("MYSQL")) {
    return new MySqlConnection();
}

if(database.equals("POSTGRES")) {
    return new PostgreSqlConnection();
}
```

A connection factory can hide implementation details.

Usage:

```java
Connection connection =
        ConnectionFactory.create(type);
```

Consumers don't care which implementation is returned.

---

# Real World Example: Notification Systems

```java
Notification notification =
        NotificationFactory.create(type);
```

Factory decides:

```text
EMAIL → EmailNotification
SMS   → SmsNotification
PUSH  → PushNotification
```

The caller only sees:

```java
Notification
```

---

# The Hidden Problem

Simple Factory solves one problem.

But introduces another.

Imagine a new provider:

```text
AmazonPay
```

Factory must change.

Current version:

```java
public static PaymentGateway create(String country)
```

becomes:

```java
if(country.equals("AMAZON")) {
    return new AmazonPayGateway();
}
```

The factory itself keeps growing.

---

# Open/Closed Principle Violation

Open/Closed Principle says:

> Software entities should be open for extension but closed for modification.

Every new provider requires:

```java
PaymentGatewayFactory
```

to be modified.

Meaning:

```text
New implementation
        ↓
Modify Factory
```

This is a violation.

---

# Factory Becomes a God Class

As systems grow:

```java
if(type.equals(...))
if(type.equals(...))
if(type.equals(...))
if(type.equals(...))
if(type.equals(...))
if(type.equals(...))
```

The factory becomes enormous.

Example:

```text
PaymentFactory
    500+ lines

NotificationFactory
    700+ lines

ReportFactory
    1200+ lines
```

This is often called:

## Factory Explosion

or

## God Factory

---

# Why Large Companies Rarely Stop Here

Simple Factory works well for:

* Small applications
* Internal tools
* Stable requirements

It struggles when:

* New implementations arrive frequently
* Plugins are added dynamically
* Teams work independently

Large systems require a more extensible approach.

This led to the next evolution:

## Factory Method Pattern

---

# Is Simple Factory an Official GoF Pattern?

Interesting fact:

The Gang of Four book defines:

* Factory Method
* Abstract Factory

but not:

* Simple Factory

Simple Factory became popular later because it is easy to understand and extremely practical.

Most developers encounter it before learning Factory Method.

---

# When Should You Use Simple Factory?

Good choice when:

✅ Small application

✅ Limited implementations

✅ Stable business requirements

✅ Fast delivery matters

Example:

```text
Internal Dashboard
Reporting Tool
Small API Service
Admin Portal
```

---

# When Should You Avoid It?

Avoid when:

❌ New implementations are added frequently

❌ Plugin architecture exists

❌ Multiple teams contribute independently

❌ Open/Closed Principle is important

❌ Factory becomes a maintenance bottleneck

---

# Architect Perspective

Simple Factory is often the first step in architecture maturity.

Stage 1:

```java
new Something()
```

everywhere.

Stage 2:

```java
SomethingFactory.create()
```

centralized creation.

Stage 3:

```java
Factory Method Pattern
```

extensible creation.

Stage 4:

```java
Dependency Injection
```

container-managed creation.

The industry evolved through these stages over decades.

---

# Key Takeaways

## What Problem Does Simple Factory Solve?

* Scattered creation logic
* Duplicate conditionals
* Tight coupling
* Poor maintainability

---

## What Does It Improve?

* Centralized creation
* Cleaner business code
* Easier maintenance
* Better abstraction

---

## Biggest Weakness

Every new implementation requires modifying the factory.

This violates the Open/Closed Principle.

---

## Architect Insight

Simple Factory is usually not the destination.

It is the bridge between:

```java
new Something()
```

and more sophisticated object creation patterns.

Understanding its limitations is what naturally leads to Factory Method Pattern.

---
