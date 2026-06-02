# The Problem With `new`

## Before Learning Any Factory Implementation

Most Factory Pattern tutorials immediately introduce:

```java
Vehicle vehicle = VehicleFactory.create("CAR");
```

without first explaining why developers wanted to avoid:

```java
Vehicle vehicle = new Car();
```

This chapter focuses on the real problem.

To understand Factory Pattern, you must first understand why uncontrolled object creation becomes dangerous in large systems.

---

# What Really Happens When You Write `new`

Most developers see:

```java
new Car();
```

as a simple statement.

The JVM sees something far more complex.

Behind the scenes:

1. Memory is allocated on the heap
2. Object header is created
3. Fields are initialized
4. Constructor executes
5. Dependencies may be created
6. Additional objects may be allocated

Example:

```java
Car car = new Car();
```

Looks innocent.

But imagine:

```java
public class Car {

    private Engine engine;

    public Car() {
        this.engine = new Engine();
    }
}
```

Now creating a Car also creates an Engine.

---

# The Hidden Dependency Chain

Let's continue.

```java
public class Engine {

    private FuelInjector injector;

    public Engine() {
        injector = new FuelInjector();
    }
}
```

And:

```java
public class FuelInjector {

    private Configuration configuration;

    public FuelInjector() {
        configuration = new Configuration();
    }
}
```

Now one line:

```java
new Car();
```

creates:

```text
Car
 └── Engine
      └── FuelInjector
            └── Configuration
```

This is called a:

## Dependency Graph

---

# Why Dependency Graphs Matter

Small systems:

```text
Car
```

Large systems:

```text
OrderService
 ├── PaymentGateway
 │     ├── HttpClient
 │     ├── RetryPolicy
 │     └── MetricsCollector
 │
 ├── NotificationService
 │     ├── EmailClient
 │     └── SmsClient
 │
 └── FraudDetector
       ├── MLModel
       └── Cache
```

One object may indirectly require dozens of objects.

The deeper the graph becomes:

* harder to understand
* harder to test
* harder to modify

---

# The Coupling Problem

Consider:

```java
public class OrderService {

    private RazorpayGateway gateway =
            new RazorpayGateway();
}
```

OrderService now depends on:

```java
RazorpayGateway
```

directly.

This dependency is called:

## Concrete Coupling

The service knows exactly which implementation exists.

---

# Why Concrete Coupling Is Dangerous

Business requirement:

```text
Move from Razorpay to Stripe.
```

Current code:

```java
private RazorpayGateway gateway =
        new RazorpayGateway();
```

Must become:

```java
private StripeGateway gateway =
        new StripeGateway();
```

Now imagine:

```text
50 services
200 tests
20 utility classes
```

all doing the same thing.

A small business change becomes a massive code change.

This phenomenon is called:

## Change Amplification

---

# The Real Cost of Change

Architects measure software quality differently.

Beginners ask:

```text
Does it work?
```

Architects ask:

```text
How expensive is future change?
```

Because software spends most of its life being modified.

A system that works today but is difficult to change tomorrow is considered poorly designed.

---

# Violation of Dependency Inversion Principle

Consider:

```java
public class OrderService {

    private RazorpayGateway gateway =
            new RazorpayGateway();
}
```

Dependency:

```text
OrderService
      ↓
RazorpayGateway
```

High-level business logic depends on low-level implementation.

Dependency Inversion Principle says:

> High-level modules should not depend on low-level modules.

Instead:

```java
public interface PaymentGateway {
    void pay();
}
```

Now:

```java
OrderService
      ↓
PaymentGateway
```

Dependency is stable.

Implementation can change.

---

# Testing Nightmare

Suppose:

```java
public class PaymentService {

    private StripeGateway gateway =
            new StripeGateway();

    public void process() {
        gateway.pay();
    }
}
```

Unit test:

```java
@Test
void shouldProcessPayment() {
}
```

Problem:

The test automatically creates:

```java
StripeGateway
```

What if Stripe:

* calls external APIs
* needs credentials
* requires network access

Your test becomes slow and unreliable.

---

# Mocking Becomes Difficult

Ideal test:

```java
PaymentGateway gateway =
        mock(PaymentGateway.class);
```

Inject mock:

```java
PaymentService service =
        new PaymentService(gateway);
```

But if service creates its own dependency:

```java
private StripeGateway gateway =
        new StripeGateway();
```

you cannot replace it easily.

The class controls creation.

The test loses control.

---

# Constructor Explosion

Consider:

```java
public class UserService {

    private UserRepository repository =
            new UserRepository();

    private Cache cache =
            new Cache();

    private Metrics metrics =
            new Metrics();

    private Validator validator =
            new Validator();
}
```

Every dependency is hardcoded.

As requirements grow:

```text
UserService
 ├── Repository
 ├── Cache
 ├── Metrics
 ├── Validator
 ├── Logger
 ├── SecurityManager
 ├── AuditService
 └── EventPublisher
```

Class complexity increases rapidly.

---

# Object Creation Is Business Logic

Many developers assume:

```java
new PaymentGateway()
```

is purely technical.

Real systems require decisions.

Example:

```text
India      → Razorpay
USA        → Stripe
Europe     → PayPal
```

Creation itself contains business rules.

Question:

Who should own these decisions?

Certainly not every service.

This realization led directly to Factory Pattern.

---

# JVM Perspective

Modern JVMs are extremely efficient at object allocation.

Creating objects is usually cheap.

The problem is rarely:

```text
Allocation Cost
```

The problem is:

```text
Dependency Management
```

Many developers mistakenly believe Factory Pattern exists for performance.

It does not.

Factory Pattern exists primarily for maintainability and flexibility.

---

# Spring's Observation

Spring Framework creators observed a common issue:

Every class was doing:

```java
new Something()
```

everywhere.

This caused:

* tight coupling
* difficult testing
* poor modularity

Spring's solution:

```java
@Bean
public PaymentGateway gateway() {
    return new StripeGateway();
}
```

Object creation moved to a central container.

This is essentially Factory Pattern at framework scale.

---

# The Architectural Insight

The biggest mistake developers make is believing:

```java
new
```

is harmless.

Every use of `new` introduces a dependency.

Every dependency increases coupling.

Every coupling increases future maintenance cost.

Architectural quality often depends on controlling where object creation happens.

---

# Key Takeaways

## What Is Wrong With `new`?

Nothing.

For simple applications it is perfectly fine.

The problem appears when:

* systems grow
* implementations change
* testing becomes important
* dependency graphs become large

---

## What Problems Does Uncontrolled Object Creation Cause?

* Tight coupling
* Difficult testing
* Change amplification
* Dependency graph complexity
* Violation of Dependency Inversion Principle

---

## Most Important Insight

The issue is not object creation.

The issue is object creation responsibility.

The moment you ask:

> "Who should create this object?"

you begin moving toward Factory Pattern.

The moment you ask:

> "Who should manage all object creation in the system?"

you begin moving toward Dependency Injection frameworks such as Spring.

---
