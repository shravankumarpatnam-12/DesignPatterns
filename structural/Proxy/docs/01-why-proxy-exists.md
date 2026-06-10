# Why Proxy Pattern Exists

## The Real Problem

Most design patterns were created because developers repeatedly encountered the same design problem.

Proxy exists because direct access to an object is often undesirable.

The object may be:

```text
Expensive

Remote

Sensitive

Unavailable

Restricted
```

In such cases, we need something that stands between:

```text
Client

and

Real Object
```

That "something" is the Proxy.

---

# The Simplest Example

Suppose we have:

```java
public interface Image {

    void display();

}
```

Real implementation:

```java
public class HighResolutionImage
        implements Image {

    public HighResolutionImage() {

        loadFromDisk();
    }

    @Override
    public void display() {

        System.out.println("Displaying image");
    }
}
```

Usage:

```java
Image image =
    new HighResolutionImage();
```

Problem:

```text
Image loads immediately.
```

Even if:

```text
display()
```

is never called.

This wastes:

* Memory
* CPU
* Disk I/O

---

# The Deeper Problem

The client assumes:

```text
Direct access is always safe.
```

Reality:

Sometimes access should be:

```text
Delayed

Controlled

Secured

Cached

Logged

Remote
```

Direct access gives us no control.

---

# Example 1: Security Problem

Suppose we have:

```java
public interface BankAccount {

    void withdraw(double amount);

}
```

Implementation:

```java
public class RealBankAccount
        implements BankAccount {
}
```

Client:

```java
account.withdraw(1000);
```

Question:

```text
Who is allowed to withdraw?
```

Without access control:

```text
Anyone
```

This is dangerous.

---

# Example 2: Remote Service Problem

Imagine:

```java
WeatherService
```

actually resides on another server.

Client:

```java
weatherService.getForecast();
```

Reality:

Behind the scenes:

```text
Network Call

Serialization

Authentication

Retry Logic
```

The client shouldn't know these details.

Something should manage them.

---

# Example 3: Expensive Object Problem

Consider:

```java
Video
```

that requires:

```text
2 GB Memory

10 Seconds Loading
```

Creating it immediately is inefficient.

We want:

```text
Load only when needed.
```

This is called:

```text
Lazy Loading
```

One of the most common Proxy use cases.

---

# What We Really Need

Instead of:

```text
Client

↓

Real Object
```

we want:

```text
Client

↓

Proxy

↓

Real Object
```

The proxy becomes a gatekeeper.

---

# The Key Insight

Proxy and Real Object share the same contract.

Example:

```java
public interface Image {
    void display();
}
```

Real object:

```java
HighResolutionImage
```

Proxy:

```java
ImageProxy
```

Both implement:

```java
Image
```

This means:

```java
Image image;
```

can reference either.

Client remains unaware.

This transparency is critical.

---

# Why Not Modify The Real Object?

Suppose we add:

```text
Security

Caching

Lazy Loading

Remote Communication
```

directly into:

```java
RealBankAccount
```

Now it becomes responsible for:

```text
Business Logic

Security

Performance

Networking
```

Violation of:

```text
Single Responsibility Principle
```

Proxy keeps those concerns separate.

---

# The Core Responsibility

Decorator asks:

```text
How can I add behavior?
```

Proxy asks:

```text
Should access be allowed?
```

Decorator focuses on:

```text
Enhancement
```

Proxy focuses on:

```text
Control
```

This distinction is extremely important.

---

# Types Of Problems Proxy Solves

## Virtual Proxy

Problem:

```text
Object is expensive.
```

Solution:

```text
Load later.
```

Example:

```text
Images

Videos

Large Documents
```

---

## Protection Proxy

Problem:

```text
Unauthorized access.
```

Solution:

```text
Security checks.
```

Example:

```text
Bank Accounts

Admin Features

Sensitive Data
```

---

## Remote Proxy

Problem:

```text
Object lives on another machine.
```

Solution:

```text
Hide network communication.
```

Example:

```text
REST Clients

RPC

gRPC
```

---

## Caching Proxy

Problem:

```text
Repeated expensive operations.
```

Solution:

```text
Cache results.
```

Example:

```text
Product Lookup

Exchange Rates

Configuration Data
```

---

# Design Principle Behind Proxy

Proxy is built on:

```text
Program To Interfaces
```

The client depends on:

```java
Image
```

not:

```java
HighResolutionImage
```

This allows:

```java
ImageProxy
```

to stand in front of the real object.

---

# Visualizing The Pattern

Without Proxy:

```text
Client
   |
   v
Real Object
```

With Proxy:

```text
Client
   |
   v
Proxy
   |
   v
Real Object
```

The client doesn't know the difference.

And that's exactly the point.

---

# Where This Appears In Real Life

Proxy concepts appear everywhere:

### Spring AOP

```text
Transaction Proxy

Security Proxy
```

### Hibernate

```text
Lazy Loading Proxy
```

### REST Clients

```text
Remote Proxy
```

### API Gateways

```text
Protection Proxy
```

### Caching Layers

```text
Caching Proxy
```

Understanding Proxy helps explain large parts of modern Java frameworks.

---

# The Core Insight

Decorator taught us:

```text
How to attach capabilities.
```

Composite taught us:

```text
How to model hierarchies.
```

Proxy teaches us:

```text
How to control access
to an object without changing it.
```

That simple idea powers security, lazy loading, transactions, caching, remoting, and many framework internals.

---

# Key Takeaways

* Proxy controls access to another object.
* Client communicates with Proxy instead of the real object.
* Proxy and Real Object share the same interface.
* Proxy can provide lazy loading, security, caching, remoting, or access control.
* Proxy preserves Single Responsibility Principle.
* Spring, Hibernate, RPC frameworks, and API gateways heavily use Proxy concepts.
* The essence of Proxy is controlled access, not behavior enhancement.
