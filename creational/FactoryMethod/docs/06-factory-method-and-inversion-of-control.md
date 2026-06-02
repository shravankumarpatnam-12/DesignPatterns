# Factory Method and Inversion of Control

## Why This Chapter Exists

By now, we've learned that Factory Method helps achieve:

* Separation of creation logic
* Open/Closed Principle
* Framework extensibility

We've also seen that Factory Method is often embedded inside Template Method.

But there is still a deeper question:

> Why do frameworks use Factory Method so heavily?

The answer is not object creation.

The answer is:

# Inversion of Control (IoC)

Factory Method was one of the earliest patterns that helped developers move toward IoC-based architectures.

Understanding this chapter is critical because modern Java development revolves around IoC.

Without understanding IoC, it is impossible to truly understand:

* Spring
* Spring Boot
* Hibernate
* JUnit
* Servlet Containers
* Dependency Injection

---

# Traditional Programming

Let's start with traditional application design.

Imagine an order processing system.

```java id="e2mds9"
public class OrderService {

    public void process() {

        PaymentGateway gateway =
                new StripeGateway();

        gateway.pay();
    }
}
```

Who controls execution?

```text id="w3l6uo"
OrderService
      ↓
Creates Object
      ↓
Calls Methods
```

The application controls everything.

This is known as:

```text id="bgkg2r"
Control Flow Owned By Application
```

---

# The Problem

Initially this seems fine.

But as systems grow:

```text id="t7vvnt"
Authentication
Authorization
Logging
Metrics
Transactions
Caching
Retry Logic
Monitoring
```

must be applied consistently.

Imagine every developer writing:

```java id="7zjylj"
authenticate();

log();

startTransaction();

execute();

commitTransaction();
```

inside every method.

Chaos follows.

Framework developers needed a better approach.

---

# The Big Idea

Instead of:

```text id="jg9nqz"
Application Controls Framework
```

What if:

```text id="j9vt5i"
Framework Controls Application
```

This simple reversal changed software architecture forever.

---

# What Is Inversion of Control?

Traditional:

```text id="3vwdc8"
Application
      ↓
Calls Framework
```

IoC:

```text id="5x8p1k"
Framework
      ↓
Calls Application
```

Control direction is inverted.

Hence the name:

# Inversion of Control

---

# Example Without IoC

Suppose we want to send notifications.

```java id="6ln9cw"
public class NotificationService {

    public void send() {

        EmailSender sender =
                new EmailSender();

        sender.send();
    }
}
```

Application controls:

* Creation
* Execution
* Dependencies

Everything.

---

# Example With IoC

Framework:

```java id="e5q5zl"
public abstract class NotificationFramework {

    protected abstract Sender createSender();

    public final void execute() {

        Sender sender =
                createSender();

        sender.send();
    }
}
```

Application:

```java id="d9grrj"
public class EmailFramework
        extends NotificationFramework {

    @Override
    protected Sender createSender() {

        return new EmailSender();
    }
}
```

Now who controls execution?

The framework.

The application only supplies customization.

This is IoC.

---

# Where Factory Method Fits

Look carefully:

```java id="7hyifv"
protected abstract Sender createSender();
```

This is Factory Method.

The framework owns:

```java id="r36cmu"
execute();
```

The application owns:

```java id="xik4jt"
createSender();
```

Factory Method becomes a mechanism that enables IoC.

---

# The Historical Evolution

Software evolved roughly like this:

### Stage 1

Application controls everything.

```java id="k5ot6g"
new Something();
```

---

### Stage 2

Simple Factory.

```java id="dywzww"
Factory.create();
```

Creation centralized.

---

### Stage 3

Factory Method.

```java id="j7ix9m"
createProduct();
```

Creation delegated.

---

### Stage 4

IoC Frameworks.

```java id="i5h6cm"
Framework calls application code
```

Control inverted.

---

### Stage 5

Dependency Injection Containers.

```java id="n9m3ka"
Spring
Guice
Dagger
```

Framework controls creation and wiring.

---

# Hollywood Principle

A famous rule emerged from IoC.

It is called:

> Don't call us. We'll call you.

This is known as the Hollywood Principle.

Traditional application:

```text id="v3j55h"
Application
      ↓
Calls Framework
```

IoC framework:

```text id="l2vf4t"
Framework
      ↓
Calls Application
```

The framework decides when your code executes.

---

# Real World Example: Servlet Container

You write:

```java id="y65u4m"
protected void doGet(...)
```

Do you call:

```java id="h2q0y8"
doGet()
```

yourself?

No.

Tomcat calls it.

Workflow:

```text id="tm7u8j"
Request Arrives
      ↓
Authentication
      ↓
Routing
      ↓
Create Request Object
      ↓
Call doGet()
      ↓
Build Response
```

You control only one piece.

Tomcat controls everything else.

---

# Real World Example: JUnit

You write:

```java id="0vh55h"
@Test
void shouldWork() {
}
```

Do you call:

```java id="dwxh52"
shouldWork()
```

yourself?

No.

JUnit calls it.

Workflow:

```text id="dk9gso"
Create Test
Run Setup
Run Test
Run Cleanup
Generate Report
```

JUnit controls execution.

You provide extension points.

---

# Real World Example: Spring Framework

You write:

```java id="frl90u"
@Bean
public PaymentGateway gateway() {

    return new StripeGateway();
}
```

You never call:

```java id="7mqynk"
gateway()
```

directly.

Spring calls it.

Spring decides:

```text id="m7r45m"
When
How Often
Lifecycle
Scope
Dependencies
```

The framework controls execution.

You provide creation logic.

This is IoC at enterprise scale.

---

# Why Architects Love IoC

Without IoC:

```text id="gt0b6u"
Business Logic
Logging
Security
Transactions
Monitoring
```

become scattered everywhere.

With IoC:

Framework applies these concerns centrally.

Benefits:

* Consistency
* Reusability
* Maintainability
* Reduced duplication

---

# The Connection Between Factory Method and IoC

Factory Method itself is not IoC.

But it enables IoC.

Framework:

```java id="x64cm7"
execute();
```

Application:

```java id="k3l7ul"
createProduct();
```

Framework decides execution.

Application supplies customization.

This is the beginning of inversion.

---

# Dependency Injection Is Built On This Idea

Many developers think:

```text id="i9jnq7"
Factory Method
```

and

```text id="br7vr4"
Dependency Injection
```

are unrelated.

They're not.

Dependency Injection evolved from the same philosophy.

Instead of:

```java id="tkjlwm"
new StripeGateway();
```

inside application code,

the framework supplies the dependency.

The application stops controlling creation entirely.

---

# The Deep Architectural Shift

Traditional OOP focuses on:

```text id="7j4v9d"
Objects
Methods
Inheritance
```

IoC focuses on:

```text id="rfzlfd"
Ownership Of Control Flow
```

Architects care less about objects.

They care more about:

```text id="h4yc5r"
Who controls execution?
```

Because that determines flexibility.

---

# Common Misunderstanding

Many developers define IoC as:

> Dependency Injection.

This is incomplete.

Dependency Injection is one implementation of IoC.

IoC is the broader concept.

Factory Method and Template Method were introducing IoC ideas long before modern DI frameworks existed.

---

# Advantages of IoC

## Centralized Control

Framework manages execution.

---

## Consistent Behavior

Cross-cutting concerns become standardized.

---

## Easier Extension

Applications provide customization points.

---

## Better Separation of Concerns

Framework and business logic remain separate.

---

## Improved Maintainability

Common workflows stay centralized.

---

# Disadvantages of IoC

## More Abstraction

Execution paths become harder to follow.

---

## Framework Dependency

Applications become dependent on framework lifecycle.

---

## Debugging Complexity

Finding where code is invoked can be difficult.

---

# Architect Perspective

Junior developers ask:

```text id="fxc7s8"
What does this class do?
```

Senior developers ask:

```text id="xt5n3n"
Who creates this object?
```

Architects ask:

```text id="x33j4o"
Who controls execution?
```

That question leads directly to IoC.

And Factory Method is one of the earliest patterns that helps answer it.

---

# The Deep Insight

Factory Method teaches:

```text id="1glc66"
Subclasses control creation
```

IoC teaches:

```text id="fh1fwj"
Framework controls execution
```

Together they create the foundation of modern frameworks.

This is why Factory Method remains relevant decades after the GoF book was published.

It introduced ideas that eventually evolved into Spring, Hibernate, and modern application containers.

---

# Key Takeaways

## What Is Inversion of Control?

A design principle where frameworks control execution and applications provide customization.

---

## How Does Factory Method Relate to IoC?

Factory Method provides extension points that allow frameworks to delegate creation decisions.

---

## What Is The Hollywood Principle?

> Don't call us. We'll call you.

---

## Why Is IoC Important?

It enables framework-oriented programming and large-scale software architecture.

---

## Most Important Insight

Factory Method is not merely a creational pattern.

It is one of the first steps toward framework-driven architecture and Inversion of Control.

---
