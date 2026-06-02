# Factory Method in Spring Framework

## Why This Chapter Exists

Many developers study Factory Method as a design pattern and assume it belongs only in textbooks.

Then they open a Spring Boot project and write:

```java
@Bean
public PaymentGateway paymentGateway() {
    return new StripeGateway();
}
```

without realizing they have just implemented a Factory Method.

One of the biggest misconceptions about design patterns is:

> Design patterns are academic.

The reality is:

> Most successful frameworks are built using design patterns.

Spring is one of the best examples.

Understanding how Spring uses Factory Method will help you move from:

```text
Pattern User
```

to

```text
Framework Thinker
```

---

# The Traditional Approach

Without Spring:

```java
public class OrderService {

    private final PaymentGateway gateway =
            new StripeGateway();

    public void process() {

        gateway.pay();
    }
}
```

Problems:

* Tight coupling
* Difficult testing
* Hardcoded dependencies
* No lifecycle management

Every class creates its own dependencies.

---

# Spring's Observation

The creators of Spring noticed a common problem.

Applications contained thousands of lines like:

```java
new Something()
```

everywhere.

Example:

```java
new DataSource()

new EmailSender()

new UserRepository()

new PaymentGateway()
```

Object creation was scattered across the application.

This created:

```text
Tight Coupling
Difficult Testing
Complex Dependency Graphs
Poor Maintainability
```

Spring's solution was radical for its time:

> Move object creation responsibility into a container.

---

# What Is The Spring Container?

The Spring Container is responsible for:

```text
Creating Objects
Managing Objects
Injecting Dependencies
Managing Lifecycle
Destroying Objects
```

Instead of application code creating objects,

Spring creates them.

---

# The First Factory Method You Ever Wrote

Consider:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentGateway paymentGateway() {

        return new StripeGateway();
    }
}
```

Most developers see:

```java
return new StripeGateway();
```

and stop.

Architects see:

```java
public PaymentGateway paymentGateway()
```

as a Factory Method.

Why?

Because the method creates and returns an object.

---

# Mapping To GoF Terminology

Classic GoF Factory Method:

```java
protected abstract Product createProduct();
```

Spring version:

```java
@Bean
public PaymentGateway paymentGateway();
```

Both:

```text
Encapsulate Creation
Return Abstraction
Hide Concrete Type
```

The concepts are nearly identical.

---

# What Happens Internally?

Application startup:

```java
@SpringBootApplication
public class Application {
}
```

Spring begins scanning.

It finds:

```java
@Bean
public PaymentGateway paymentGateway() {
    return new StripeGateway();
}
```

Spring executes:

```java
paymentGateway();
```

and stores the result inside the container.

Notice something important.

You never call:

```java
paymentGateway();
```

Spring does.

This is IoC in action.

---

# Who Controls Execution?

Traditional Java:

```text
Application
      ↓
Creates Objects
```

Spring:

```text
Spring Container
      ↓
Creates Objects
```

Control has shifted.

This is why Spring is fundamentally an IoC container.

---

# Factory Method + IoC

Look closely.

```java
@Bean
public PaymentGateway paymentGateway() {

    return new StripeGateway();
}
```

You provide:

```text
Creation Logic
```

Spring provides:

```text
Execution
Lifecycle
Caching
Dependency Wiring
```

This is exactly what we discussed in the previous chapter.

---

# Bean Lifecycle

Suppose:

```java
@Bean
public PaymentGateway paymentGateway() {

    return new StripeGateway();
}
```

Spring does much more than simply create the object.

Lifecycle:

```text
Create Bean
Inject Dependencies
Apply Proxies
Run Post Processors
Initialize Bean
Store Bean
Provide Bean
Destroy Bean
```

The Factory Method creates the object.

The container manages the object.

---

# Singleton Scope

By default:

```java
@Bean
public PaymentGateway paymentGateway() {
    return new StripeGateway();
}
```

creates:

```text
One Object
```

for the entire application.

Even if called hundreds of times:

```java
@Autowired
PaymentGateway gateway;
```

Spring returns the same instance.

This is significantly more powerful than a traditional Factory Method.

---

# Factory Method Without Spring

```java
public class GatewayFactory {

    public PaymentGateway create() {

        return new StripeGateway();
    }
}
```

Caller:

```java
factory.create();
factory.create();
factory.create();
```

Multiple objects.

---

# Factory Method With Spring

```java
@Bean
public PaymentGateway paymentGateway() {
    return new StripeGateway();
}
```

Caller:

```java
@Autowired
PaymentGateway gateway;
```

Spring controls creation frequency.

Spring controls lifecycle.

The pattern evolves into a container.

---

# Dependency Injection Through Factory Methods

Consider:

```java
@Bean
public PaymentGateway paymentGateway() {

    return new StripeGateway();
}
```

and:

```java
@Bean
public OrderService orderService(
        PaymentGateway gateway) {

    return new OrderService(gateway);
}
```

Spring performs:

```text
Create PaymentGateway
       ↓
Create OrderService
       ↓
Inject Dependency
```

This creates an object graph automatically.

---

# Visualizing The Dependency Graph

```text
OrderService
      │
      ▼
PaymentGateway
      │
      ▼
StripeGateway
```

Without Spring:

Developers build the graph manually.

With Spring:

The container builds the graph.

---

# BeanFactory

The name itself reveals Spring's roots.

One of Spring's earliest interfaces:

```java
BeanFactory
```

Why not:

```java
BeanManager
```

or

```java
BeanRegistry
```

?

Because Spring originated from factory concepts.

The framework's entire purpose was:

```text
Centralized Object Creation
```

The name reflects this heritage.

---

# FactoryBean

Spring contains another advanced concept:

```java
FactoryBean<T>
```

Many developers never use it.

But it demonstrates how deeply Factory patterns are embedded in Spring.

Example:

```java
public class PaymentGatewayFactoryBean
        implements FactoryBean<PaymentGateway> {

    @Override
    public PaymentGateway getObject() {

        return new StripeGateway();
    }
}
```

Instead of exposing the factory itself,

Spring exposes the created object.

---

# Why FactoryBean Exists

Some objects require:

```text
Complex Creation
Configuration
Initialization
Resource Allocation
```

A normal constructor may not be sufficient.

FactoryBean encapsulates that complexity.

---

# Real World Example: DataSource

Creating a database connection pool involves:

```text
Driver Loading
Pool Initialization
Connection Validation
Configuration
Monitoring Setup
```

Spring often uses factory mechanisms internally to hide this complexity.

---

# Spring's Evolution Beyond Factory Method

Early Java applications:

```java
new Object()
```

Simple Factory:

```java
Factory.create()
```

Factory Method:

```java
createObject()
```

Spring:

```java
Container Managed Creation
```

Spring didn't replace Factory Method.

It scaled Factory Method to application level.

---

# The Hidden Architectural Insight

Many developers believe Spring invented Dependency Injection.

Not exactly.

Spring combined existing ideas:

```text
Factory Method
Template Method
Inversion Of Control
Dependency Injection
```

into a unified framework.

Understanding Factory Method helps explain why Spring looks the way it does.

---

# Advantages Of Spring's Approach

## Centralized Creation

Creation logic is not scattered.

---

## Better Testability

Dependencies can be replaced easily.

---

## Lifecycle Management

Objects can be managed consistently.

---

## Dependency Graph Construction

Spring builds complex object graphs automatically.

---

## Reduced Coupling

Applications depend on abstractions.

---

# Trade-Offs

## More Framework Dependency

Application behavior becomes tied to Spring.

---

## Hidden Execution Flow

Object creation is less obvious.

---

## Learning Curve

Understanding container behavior requires experience.

---

# Common Misunderstanding

Many developers think:

```java
@Bean
```

is just configuration.

Architecturally:

```java
@Bean
```

is a specialized Factory Method.

The container simply automates the execution and lifecycle management around it.

---

# Architect Perspective

Junior developers see:

```java
@Bean
public PaymentGateway paymentGateway()
```

and think:

```text
Configuration
```

Architects see:

```text
Factory Method
        +
IoC
        +
Dependency Injection
        +
Lifecycle Management
```

The same simple method participates in several architectural concepts simultaneously.

---

# The Deep Insight

Factory Method answered:

> Who creates the object?

Spring answers:

> Who creates the entire application object graph?

Spring's answer is:

```text
The Container
```

This is why Spring can manage applications containing:

```text
Hundreds Of Modules
Thousands Of Beans
Millions Of Requests
```

while keeping creation logic centralized.

---

# Key Takeaways

## Does Spring Use Factory Method?

Yes.

`@Bean` methods are specialized Factory Methods.

---

## What Does Spring Add?

* Lifecycle management
* Dependency injection
* Object graph construction
* Scoping
* Container management

---

## Why Is BeanFactory Named That Way?

Because Spring's origins are deeply rooted in factory-based object creation.

---

## What Is FactoryBean?

A Spring abstraction that encapsulates complex object creation.

---

## Most Important Insight

Spring did not eliminate Factory Method.

It evolved Factory Method into a full application container capable of managing entire dependency graphs.

---