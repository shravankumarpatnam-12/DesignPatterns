# Prototype in Spring Framework

## Why This Chapter Exists

After learning Prototype, many developers ask:

> Does Spring actually use the Prototype Pattern?

The answer is:

```text id="a1x8mv"
Yes

But Not In The Way Most Developers Expect
```

Most developers imagine:

```java id="b2k7pw"
object.clone();
```

However, Spring rarely relies on Java's cloning mechanism.

Instead, Spring applies Prototype thinking through:

```text id="c3v9qn"
Bean Scopes

Object Templates

Configuration Reuse

Context-Based Creation
```

Understanding this chapter is important because many Java developers use Prototype concepts every day through Spring without realizing it.

---

# The Common Misconception

Many developers believe:

```text id="d4k8rw"
Prototype Pattern
      ↓
clone()
```

This is incorrect.

Remember:

```text id="e5v2qx"
Prototype Pattern
      ↓
Create New Objects
Using Existing Definitions
```

The pattern is broader than cloning.

Spring demonstrates this perfectly.

---

# Spring's Object Creation Problem

Spring applications often contain:

```text id="f6k4rp"
Controllers

Services

Repositories

Configurations

Clients
```

Question:

Who creates them?

Without Spring:

```java id="g7v3qn"
new UserService();

new UserRepository();

new PaymentGateway();
```

Creation logic spreads across the application.

Spring centralizes it.

---

# Bean Scope Concept

One of Spring's most important features:

```java id="h8k2pw"
Scope
```

A scope determines:

```text id="i9v7qx"
How Objects Are Created

How Long They Live
```

The most common scopes are:

```text id="j1k4rp"
Singleton

Prototype
```

Notice the second one.

---

# Singleton Scope

Example:

```java id="k2v8qn"
@Component
@Scope("singleton")
public class UserService {
}
```

Behavior:

```text id="l3k7rw"
One Object

Shared Everywhere
```

Spring creates:

```text id="m4v2qx"
Single Instance
```

for the entire application.

---

# Prototype Scope

Example:

```java id="n5k4rp"
@Component
@Scope("prototype")
public class UserService {
}
```

Behavior:

```text id="o6v8qn"
New Object

Every Request
```

Every lookup creates:

```text id="p7k3rw"
Fresh Instance
```

This is where Prototype thinking enters Spring.

---

# Understanding Spring Prototype Scope

Suppose:

```java id="q8v4qx"
ApplicationContext context;
```

Request:

```java id="r9k2rp"
context.getBean(
        UserService.class);
```

Result:

```text id="s1v7qn"
New Instance
```

Request again:

```java id="t2k4pw"
context.getBean(
        UserService.class);
```

Result:

```text id="u3v8qx"
Another New Instance
```

Unlike singleton, objects are not shared.

---

# Why Is It Called Prototype?

Because Spring stores:

```text id="v4k3rp"
Bean Definition
```

and creates:

```text id="w5v7qn"
New Instances
```

from that definition repeatedly.

Conceptually:

```text id="x6k4pw"
Definition
      ↓
Template
      ↓
Instances
```

This closely resembles Prototype thinking.

---

# Important Clarification

Spring Prototype Scope:

```text id="y7v8qx"
Is Inspired By Prototype Ideas
```

but does not necessarily mean:

```java id="z8k2rp"
clone()
```

is used internally.

This distinction is important.

---

# Bean Definitions As Prototypes

Spring stores:

```text id="aa9v7qn"
Class Metadata

Configuration

Dependencies
```

inside:

```text id="ab1k4pw"
Bean Definitions
```

Think of these definitions as:

```text id="ac2v8qx"
Construction Templates
```

for future objects.

---

# Example

Definition:

```java id="ad3k2rp"
UserService
```

contains:

```text id="ae4v7qn"
Dependencies

Configuration

Lifecycle Metadata
```

Spring repeatedly creates objects using this stored knowledge.

This resembles:

```text id="af5k4pw"
Prototype Registry
```

conceptually.

---

# Prototype Registry Comparison

Classic Prototype:

```java id="ag6v8qx"
Map<String, Prototype>
```

stores:

```text id="ah7k2rp"
Object Instances
```

---

Spring:

```text id="ai8v7qn"
Bean Registry
```

stores:

```text id="aj9k4pw"
Bean Definitions
```

Different implementation.

Same philosophy:

```text id="ak1v8qx"
Reuse Existing Knowledge
```

---

# Real Enterprise Example

Suppose:

```java id="al2k2rp"
ReportGenerator
```

contains:

```text id="am3v7qn"
Templates

Configuration

Formatting Rules
```

Every report generation requires:

```text id="an4k4pw"
Fresh Instance
```

Using:

```java id="ao5v8qx"
@Scope("prototype")
```

Spring creates new instances automatically.

---

# Stateful Objects

Prototype scope is especially useful for:

```text id="ap6k2rp"
Stateful Components
```

Example:

```java id="aq7v7qn"
ShoppingCart
```

contains:

```text id="ar8k4pw"
Items

Totals

Session Data
```

Sharing a singleton would be dangerous.

Prototype scope provides isolation.

---

# Why Singleton Is Usually Preferred

Important observation.

Most Spring beans are:

```text id="as9v8qx"
Singletons
```

Why?

Because:

```text id="at1k2rp"
Services

Repositories

Controllers
```

are generally:

```text id="au2v7qn"
Stateless
```

No need for repeated creation.

Prototype introduces extra cost.

---

# When Prototype Scope Makes Sense

Use when:

```text id="av3k4pw"
Object Contains Mutable State

Object Must Be Isolated

Instances Cannot Be Shared
```

Examples:

```text id="aw4v8qx"
Workflow Execution

Document Generation

Shopping Carts

Request Contexts
```

---

# Spring And Object Templates

Many Spring applications naturally use:

```text id="ax5k2rp"
Template → Copy → Customize
```

workflow.

Examples:

```text id="ay6v7qn"
Email Templates

Report Templates

Configuration Templates
```

These are Prototype concepts.

---

# Prototype Thinking In Spring Boot

Consider configuration classes.

```java id="az7k4pw"
@ConfigurationProperties
```

loads:

```text id="ba8v8qx"
Defaults

Settings

Policies
```

These configurations frequently act as:

```text id="bb9k2rp"
Master Templates
```

for runtime objects.

Again:

```text id="bc1v7qn"
Prototype Thinking
```

appears.

---

# Hibernate Example

Hibernate stores:

```text id="bd2k4pw"
Entity Metadata

Mappings

Configuration
```

once.

Then creates:

```text id="be3v8qx"
Entity Instances
```

repeatedly.

The framework emphasizes:

```text id="bf4k2rp"
Reuse Existing Knowledge
```

instead of repeated discovery.

---

# Spring Batch Example

Batch processing frequently creates:

```text id="bg5v7qn"
Job Definitions

Step Definitions
```

Once.

Execution generates:

```text id="bh6k4pw"
Runtime Job Instances
```

Many enterprise batch systems effectively use Prototype-style architecture.

---

# Spring Security Example

Security configurations define:

```text id="bi7v8qx"
Authentication Rules

Authorization Rules

Policies
```

These definitions act as reusable templates for runtime processing.

Again:

```text id="bj8k2rp"
Configuration Reuse
```

is the key idea.

---

# Prototype And Dependency Injection

A common question:

> If Prototype beans exist, how do they work with Singleton beans?

Example:

```java id="bk9v7qn"
@Service
public class OrderService {

    @Autowired
    private ShoppingCart cart;
}
```

Suppose:

```java id="bl1k4pw"
ShoppingCart
```

is prototype scoped.

Question:

Will OrderService receive a new cart every time?

Answer:

```text id="bm2v8qx"
No
```

This surprises many developers.

---

# The Prototype Injection Problem

Spring injects dependencies when the singleton is created.

Therefore:

```text id="bn3k2rp"
Prototype Bean
```

becomes:

```text id="bo4v7qn"
Single Instance
```

inside that singleton.

This is a famous Spring interview question.

---

# Solutions

Common solutions:

```text id="bp5k4pw"
ObjectFactory

Provider

ApplicationContext Lookup
```

These allow obtaining fresh prototype instances.

---

# Example

```java id="bq6v8qx"
@Autowired
private ObjectFactory<
        ShoppingCart> factory;
```

Usage:

```java id="br7k2rp"
ShoppingCart cart =
        factory.getObject();
```

Each call creates:

```text id="bs8v7qn"
Fresh Prototype
```

---

# Architect Perspective

Junior developers think:

```text id="bt9k4pw"
Prototype
      ↓
clone()
```

Spring developers think:

```text id="bu1v8qx"
Prototype
      ↓
Bean Scope
```

Architects think:

```text id="bv2k2rp"
Prototype
      ↓
Reusable Definitions

Reusable Knowledge

Reusable Configuration
```

The implementation changes.

The philosophy remains.

---

# Prototype Pattern vs Spring Prototype Scope

| Feature             | GoF Prototype        | Spring Prototype                |
| ------------------- | -------------------- | ------------------------------- |
| Core Idea           | Copy Existing Object | Create From Reusable Definition |
| clone() Required    | Sometimes            | No                              |
| State Reuse         | Yes                  | Yes                             |
| Configuration Reuse | Yes                  | Yes                             |
| Object Isolation    | Yes                  | Yes                             |

Different mechanisms.

Similar goals.

---

# The Deep Insight

Spring rarely uses Prototype because cloning is useful.

Spring uses Prototype ideas because:

```text id="bw3v7qn"
Reusable Definitions

↓

Reusable Instances
```

is a powerful architectural model.

The framework focuses on:

```text id="bx4k4pw"
Knowledge Reuse
```

rather than:

```text id="by5v8qx"
Object Recreation
```

This is the same fundamental idea that motivated the Prototype Pattern decades ago.

---

# Key Takeaways

## Does Spring Use Prototype?

Yes, primarily through prototype bean scope and reusable bean definitions.

---

## Does Spring Use clone()?

Generally no.

---

## Why Is Prototype Scope Useful?

For stateful objects requiring isolated instances.

---

## What Is The Biggest Spring Insight?

Spring reuses definitions rather than object instances.

---

## Most Important Insight

Spring demonstrates that Prototype is not about cloning APIs.

It is about reusing existing knowledge to create new objects efficiently.

---