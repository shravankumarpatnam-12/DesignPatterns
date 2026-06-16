# Decorator vs Inheritance vs Composition

## The Most Important Question

Learning Decorator is easy.

Knowing **when not to use it** is what separates experienced developers from architects.

Many systems become overly complicated because developers force Decorator into problems that are better solved with:

* Inheritance
* Composition
* Strategy
* Proxy
* Chain of Responsibility
* AOP

This chapter focuses on decision-making.

---

# Decorator vs Inheritance

This is the battle that gave birth to Decorator.

## Inheritance Approach

Suppose we have:

```java
public class EmailNotification {
    public void send(String message) {
        System.out.println(message);
    }
}
```

Need logging:

```java
public class LoggedEmailNotification
        extends EmailNotification {
}
```

Need encryption:

```java
public class EncryptedEmailNotification
        extends EmailNotification {
}
```

Need both:

```java
public class LoggedEncryptedEmailNotification
        extends EmailNotification {
}
```

Need retry?

```java
public class LoggedEncryptedRetryEmailNotification
        extends EmailNotification {
}
```

The hierarchy starts exploding.

---

## Decorator Approach

```java
Notification notification =
    new RetryDecorator(
        new LoggingDecorator(
            new EncryptionDecorator(
                new EmailNotification()
            )
        )
    );
```

No new combinations.

No hierarchy growth.

Capabilities become modular.

---

## When Inheritance Is Better

Inheritance is not evil.

It is often the correct solution.

Use inheritance when behavior is:

```text
Stable

Predictable

Permanent
```

Example:

```java
Vehicle
    ├── Car
    ├── Bike
    └── Truck
```

These are true domain relationships.

```text
Car IS-A Vehicle
```

is unlikely to change.

Decorator adds unnecessary complexity here.

---

## Architect Rule

Use inheritance for:

```text
Identity
```

Use decorator for:

```text
Capabilities
```

Example:

```text
Car IS-A Vehicle
```

Inheritance.

Example:

```text
Vehicle has GPS

Vehicle has Logging

Vehicle has Monitoring
```

Decorator.

---

# Decorator vs Composition

Many developers think:

```text
Decorator = Composition
```

Not exactly.

Decorator is a specialized form of composition.

---

## Plain Composition

```java
public class Car {

    private Engine engine;

}
```

Car uses Engine.

No delegation chain.

No behavioral enhancement.

Just object collaboration.

---

## Decorator Composition

```java
public class LoggingDecorator
        implements Notification {

    private Notification notification;
}
```

Here:

```text
HAS-A Notification
```

and

```text
IS-A Notification
```

simultaneously.

That dual relationship makes Decorator unique.

---

## Key Difference

Composition:

```text
Uses another object
```

Decorator:

```text
Uses another object

And exposes the same contract
```

This distinction is critical.

---

# Decorator vs Strategy

These patterns frequently appear together.

Many developers confuse them.

---

## Strategy Purpose

Strategy changes:

```text
How something is done
```

Example:

```java
PaymentStrategy
```

Implementations:

```java
CreditCardPayment

PayPalPayment

UPIPayment
```

Only one strategy executes.

---

## Decorator Purpose

Decorator adds:

```text
Additional behavior
```

Example:

```java
LoggingDecorator

RetryDecorator

MetricsDecorator
```

All decorators execute.

---

## Mental Model

Strategy:

```text
Choose one
```

Decorator:

```text
Combine many
```

---

## Example

Strategy:

```java
PaymentStrategy strategy =
    new CreditCardPayment();
```

Decorator:

```java
new LoggingDecorator(
    new RetryDecorator(
        new PaymentService()
    )
);
```

Different goals.

Different solutions.

---

# Decorator vs Proxy

This comparison appears frequently in interviews.

Structurally:

```text
Almost identical
```

Purpose:

```text
Completely different
```

---

## Proxy Goal

Control access.

Examples:

* Security
* Lazy loading
* Remote calls
* Access restrictions

Proxy decides:

```text
Can this request proceed?
```

---

## Decorator Goal

Enhance behavior.

Decorator asks:

```text
How can I add value?
```

---

## Example Proxy

```java
SecurityProxy
```

```java
if(userAuthorized()) {
    service.execute();
}
```

Focus:

```text
Access control
```

---

## Example Decorator

```java
LoggingDecorator
```

```java
log();

service.execute();
```

Focus:

```text
Behavior enhancement
```

---

## Interview Shortcut

Proxy controls access.

Decorator adds responsibilities.

---

# Decorator vs Chain of Responsibility

This comparison confuses even senior developers.

Because both create chains.

---

## Decorator Chain

Every object executes.

```text
Decorator A

Decorator B

Decorator C
```

All participate.

---

## Chain of Responsibility

Processing may stop.

```text
Handler A

Handler B

Handler C
```

A handler decides:

```text
I handled it.

Stop.
```

Remaining handlers never execute.

---

## Example

Decorator:

```text
Logging

Encryption

Compression

Send
```

Everything runs.

---

Chain:

```text
Can you approve?

Manager

Director

CEO
```

One handler processes.

Others are skipped.

---

# Decorator vs AOP

This is extremely important for Spring developers.

---

## Problem

Developers often manually create:

```java
LoggingDecorator

RetryDecorator

MetricsDecorator

TransactionDecorator
```

Frameworks automate this.

---

## AOP Solution

```java
@Transactional

@Retryable

@Cacheable
```

Framework generates wrappers.

No manual chaining.

---

## Architect View

AOP is often:

```text
Decorator Pattern

+

Dynamic Proxy

+

Runtime Weaving
```

Decorator ideas still exist underneath.

AOP simply automates the implementation.

---

# When Decorator Is the Right Choice

Use Decorator when:

✅ Behavior is optional

✅ Features are combinable

✅ Features change independently

✅ Runtime configuration is required

✅ Open/Closed Principle matters

Examples:

* Logging
* Metrics
* Retry
* Security
* Compression
* Encryption
* Monitoring
* Auditing

---

# When Decorator Is the Wrong Choice

Avoid Decorator when:

❌ Domain hierarchy is stable

❌ Only one implementation exists

❌ Features are never combined

❌ Simpler composition works

❌ Complexity exceeds benefit

Example:

```java
Dog extends Animal
```

Decorator provides no value.

---

# The Architect's Decision Framework

Ask three questions:

### Question 1

Is this an identity relationship?

```text
IS-A
```

Use inheritance.

---

### Question 2

Is this a capability relationship?

```text
HAS-A
```

Consider Decorator.

---

### Question 3

Will capabilities be combined dynamically?

If yes:

```text
Decorator becomes very attractive
```

If no:

```text
Simple composition may be enough
```

---

# The Architectural Insight

Most junior developers focus on:

```text
How do I implement Decorator?
```

Architects focus on:

```text
What kind of variability am I modeling?
```

Decorator is not about wrapping objects.

Decorator is about modeling evolving capabilities without creating rigid hierarchies.

That is why the pattern continues to appear in nearly every major enterprise framework.

---

# Key Takeaways

* Inheritance models identity; Decorator models capabilities.
* Decorator is a specialized form of composition.
* Strategy selects one behavior; Decorator combines many behaviors.
* Proxy controls access; Decorator enhances behavior.
* Chain of Responsibility may stop processing; Decorator typically executes all layers.
* AOP often automates Decorator-like behavior.
* Use Decorator when behavior is optional, combinable, and independently evolving.
