# Understanding the Core Idea

## Before Learning Proxy

Most developers memorize:

```text id="g6hx4r"
Client

   ↓

Proxy

   ↓

Real Subject
```

and stop there.

The problem?

They understand the structure.

They do not understand why the structure exists.

Let's derive it naturally.

---

# Step 1: Start With A Simple Object

Suppose we have:

```java id="e0x4l8"
public interface Document {

    void open();
}
```

Real implementation:

```java id="w2d6su"
public class RealDocument
        implements Document {

    @Override
    public void open() {

        System.out.println(
            "Opening document"
        );
    }
}
```

Usage:

```java id="mnn67e"
Document document =
        new RealDocument();

document.open();
```

Simple.

No pattern required.

---

# Step 2: New Requirement Appears

Business says:

```text id="dgmq1z"
Only managers can open
confidential documents.
```

First instinct:

```java id="r9ik4t"
public class RealDocument
```

becomes:

```java id="r08vsv"
public void open() {

    if(currentUserIsManager()) {

        // open document
    }
}
```

Works.

But now the document class handles:

```text id="tzghrk"
Document Logic

Security Logic
```

Responsibilities are mixing.

---

# Another Requirement

Business says:

```text id="84rb84"
Log every access.
```

Now:

```java id="jlwm31"
RealDocument
```

contains:

```text id="jlwm32"
Business Logic

Security Logic

Logging Logic
```

Growing problem.

---

# The Key Observation

The document itself should answer:

```text id="jlwm33"
How do I open?
```

It should not answer:

```text id="jlwm34"
Who may access me?
```

Those are different responsibilities.

---

# Step 3: Introduce A Middleman

Instead of:

```text id="jlwm35"
Client

↓

RealDocument
```

Introduce:

```text id="jlwm36"
Client

↓

DocumentProxy

↓

RealDocument
```

Now:

```text id="jlwm37"
Proxy controls access.

RealDocument performs work.
```

Responsibilities are separated.

---

# Step 4: Why Must Proxy Implement The Same Interface?

This is the most important design decision.

Proxy implements:

```java id="jlwm38"
Document
```

Real object also implements:

```java id="jlwm39"
Document
```

Therefore:

```java id="jlwm40"
Document document;
```

can reference:

```java id="jlwm41"
RealDocument
```

or

```java id="jlwm42"
DocumentProxy
```

interchangeably.

---

# Transparency

Client code:

```java id="jlwm43"
document.open();
```

doesn't know whether:

```text id="jlwm44"
Real Object
```

or

```text id="jlwm45"
Proxy
```

is executing.

This property is called:

```text id="jlwm46"
Transparency
```

A properly designed proxy should be invisible to the client.

---

# Step 5: Protection Proxy

Implementation:

```java id="jlwm47"
public class DocumentProxy
        implements Document {

    private final Document document;

    public DocumentProxy(
            Document document) {

        this.document = document;
    }

    @Override
    public void open() {

        if(currentUserIsManager()) {

            document.open();
        }
        else {

            throw new SecurityException(
                "Access denied"
            );
        }
    }
}
```

Usage:

```java id="jlwm48"
Document document =
    new DocumentProxy(
        new RealDocument()
    );
```

Execution:

```text id="jlwm49"
Security Check

↓

RealDocument
```

---

# Understanding Delegation

A proxy rarely performs business work.

Instead:

```text id="jlwm50"
Checks something

Controls something

Manages something
```

Then delegates.

Example:

```java id="jlwm51"
document.open();
```

Flow:

```text id="jlwm52"
Proxy

↓

Real Object
```

This delegation is fundamental.

---

# Step 6: Virtual Proxy

A very common use case.

Problem:

```text id="jlwm53"
Object creation is expensive.
```

Example:

```java id="jlwm54"
Video
```

requires:

```text id="jlwm55"
5 GB memory

10 second startup
```

Creating it immediately is wasteful.

---

## Without Proxy

```java id="jlwm56"
Video video =
    new HighResolutionVideo();
```

Video loads instantly.

Even if:

```java id="jlwm57"
play()
```

is never called.

---

## With Virtual Proxy

```java id="jlwm58"
public class VideoProxy
        implements Video {

    private RealVideo video;

    @Override
    public void play() {

        if(video == null) {

            video =
                new RealVideo();
        }

        video.play();
    }
}
```

Now:

```text id="jlwm59"
Object created only when needed.
```

This is:

```text id="jlwm60"
Lazy Loading
```

One of the most important Proxy applications.

---

# Step 7: Remote Proxy

Problem:

```text id="jlwm61"
Object lives on another server.
```

Client:

```java id="jlwm62"
weatherService.getForecast();
```

Looks local.

Actually:

```text id="jlwm63"
HTTP Request

Serialization

Authentication

Response Parsing
```

occur behind the scenes.

---

## Conceptual Model

```text id="jlwm64"
Client

↓

WeatherServiceProxy

↓

Remote Service
```

Proxy hides networking complexity.

---

# Step 8: Caching Proxy

Problem:

```text id="jlwm65"
Expensive operation called repeatedly.
```

Example:

```java id="jlwm66"
exchangeRateService
```

Without caching:

```text id="jlwm67"
Database Hit

Database Hit

Database Hit
```

---

## Proxy Solution

```java id="jlwm68"
public class CachedExchangeRateProxy
        implements ExchangeRateService {
}
```

Flow:

```text id="jlwm69"
Check Cache

↓

Return Cached Result

OR

Call Real Service
```

Business code remains unchanged.

---

# The Mental Model

Decorator asks:

```text id="jlwm70"
How can I enrich behavior?
```

Composite asks:

```text id="jlwm71"
How can I model hierarchy?
```

Proxy asks:

```text id="jlwm72"
Should access proceed?
```

or

```text id="jlwm73"
Can I optimize access?
```

Different purpose.

Different mindset.

---

# The Four Most Common Proxy Types

## Protection Proxy

Controls:

```text id="jlwm74"
Security

Authorization

Permissions
```

---

## Virtual Proxy

Controls:

```text id="jlwm75"
Object Creation
```

through lazy loading.

---

## Remote Proxy

Controls:

```text id="jlwm76"
Network Communication
```

---

## Caching Proxy

Controls:

```text id="jlwm77"
Performance
```

through caching.

---

# Common Beginner Mistake

Many developers think:

```text id="jlwm78"
Proxy adds functionality.
```

Not necessarily.

Proxy primarily controls:

```text id="jlwm79"
Access
```

Sometimes performance.

Sometimes security.

Sometimes remoting.

The goal is not enhancement.

The goal is mediation.

---

# Another Common Mistake

Confusing Proxy with Decorator.

Both look similar:

```java id="jlwm80"
class SomethingProxy
        implements Service
```

```java id="jlwm81"
class SomethingDecorator
        implements Service
```

Structure:

```text id="jlwm82"
Nearly identical.
```

Intent:

```text id="jlwm83"
Completely different.
```

We will analyze this deeply later.

---

# The Architectural Insight

Junior developers see:

```java id="jlwm84"
DocumentProxy
```

Architects see:

```text id="jlwm85"
A controlled entry point
to a resource.
```

That resource may be:

* A database
* A remote service
* A secured object
* A cache
* A transaction boundary
* A Hibernate entity

The actual resource is irrelevant.

The pattern is about controlling access.

---

# Key Takeaways

* Proxy stands between client and real object.
* Proxy and real object share the same interface.
* Delegation is central to the pattern.
* Proxy provides controlled access while preserving transparency.
* Common proxy types include Protection, Virtual, Remote, and Caching proxies.
* Proxy focuses on access mediation, not behavior enhancement.
* Many enterprise frameworks are built on proxy concepts.
