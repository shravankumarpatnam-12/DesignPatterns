# Flyweight vs Singleton vs Cache vs Object Pool

## Why This Chapter Matters

Flyweight is one of the most misunderstood patterns.

Many developers see:

```java
Map<String, Object>
```

and immediately think:

```text
Cache
```

Others see:

```java
String Pool
```

and think:

```text
Cache
```

This creates confusion.

The reason is simple:

```text
Several patterns use
Maps, Registries, and Factories.

But they solve completely
different problems.
```

Architects classify patterns by:

```text
Intent
```

not implementation.

---

# The Golden Rule

Ask:

```text
Why does this object exist?
```

Not:

```text
How is it stored?
```

The answer determines the pattern.

---

# Flyweight

## Purpose

Reduce memory consumption by sharing immutable state.

Question:

```text
Can multiple logical objects
share the same data?
```

---

## Mental Model

```text
Share State
```

---

## Example

```java
String s1 = "Hello";
String s2 = "Hello";
```

Both references point to:

```text
One String Object
```

stored in the String Pool.

---

## Why?

Because:

```text
"Hello"
```

is immutable.

There is no reason to create:

```text
String("Hello")
String("Hello")
String("Hello")
```

multiple times.

One instance can be shared safely.

---

## Flyweight Formula

```text
Shared Immutable State

+

External Context
```

---

## Examples

### String Pool

```java
String s1 = "Hello";
String s2 = "Hello";
```

Flyweight.

---

### Integer Cache

```java
Integer a = 100;
Integer b = 100;
```

Flyweight.

---

### Enum Constants

```java
Status.ACTIVE
```

Flyweight-like sharing.

---

### Game Tree Definitions

```text
Oak Tree Definition
```

shared by millions of trees.

---

# Singleton

## Purpose

Guarantee exactly one instance.

Question:

```text
Should only one object exist?
```

---

## Mental Model

```text
One Instance
```

---

## Example

```java
public final class ConfigManager {

    private static final ConfigManager
            INSTANCE =
            new ConfigManager();

}
```

---

## Difference From Flyweight

Singleton:

```text
One Object
```

for entire application.

---

Flyweight:

```text
Many Shared Objects
```

Example:

```text
String("Hello")

String("World")

String("Java")
```

Multiple flyweights.

Not one.

---

## Architect Shortcut

Singleton answers:

```text
How many instances?
```

Flyweight answers:

```text
How much state can be shared?
```

---

# Cache

## Purpose

Avoid expensive computation or retrieval.

Question:

```text
Can we reuse a previously
computed result?
```

---

## Mental Model

```text
Reuse Results
```

---

## Example

```java
Product product =
        cache.get(productId);
```

Avoid:

```text
Database Query
```

---

## Examples

### Redis

```text
Product Data
```

---

### Caffeine

```text
Query Results
```

---

### Exchange Rate Cache

```text
External API Response
```

---

# Flyweight vs Cache

This is the most important distinction.

---

## Flyweight Stores

```text
Shared Immutable State
```

Example:

```text
String("Hello")
```

---

## Cache Stores

```text
Expensive Results
```

Example:

```text
Product Query Result
```

---

## Flyweight Goal

```text
Reduce Memory Usage
```

---

## Cache Goal

```text
Reduce Work
```

---

## Important Clarification

String Pool internally behaves like:

```java
Map<String, String>
```

conceptually.

But it is still:

```text
Flyweight
```

because its primary purpose is:

```text
Sharing immutable strings.
```

The internal registry is merely an implementation detail.

---

# Object Pool

## Purpose

Reuse expensive objects.

Question:

```text
Can we avoid repeatedly
creating and destroying objects?
```

---

## Mental Model

```text
Reuse Objects
```

---

## Example

Database connections.

Creating:

```java
Connection
```

is expensive.

Instead:

```text
Borrow

Use

Return
```

---

## Examples

### HikariCP

Database Connections.

---

### Thread Pools

Worker Threads.

---

### HTTP Connection Pools

Reusable network connections.

---

# Flyweight vs Object Pool

Object Pool:

```text
Share Objects Over Time
```

---

Flyweight:

```text
Share State Simultaneously
```

---

Example:

### Flyweight

```text
1 RestaurantIcon
```

shared by:

```text
1 Million Map Markers
```

at the same time.

---

### Object Pool

```text
1 Database Connection
```

used by:

```text
Thread A

then

Thread B

then

Thread C
```

over time.

---

# Prototype

## Purpose

Create objects by cloning.

Question:

```text
Can I copy an existing object?
```

---

## Mental Model

```text
Clone Existing Object
```

---

## Example

```java
Employee clone =
        original.clone();
```

---

# Flyweight vs Prototype

Prototype:

```text
Create New Object
```

through cloning.

---

Flyweight:

```text
Avoid Creating New Object
```

through sharing.

---

They solve opposite problems.

---

# Visual Summary

## Singleton

```text
One Instance
```

---

## Flyweight

```text
Shared Immutable State
```

---

## Cache

```text
Stored Results
```

---

## Object Pool

```text
Reusable Objects
```

---

## Prototype

```text
Cloned Objects
```

---

# Recognition Framework

Ask:

### One Instance?

```text
Singleton
```

---

### Shared Immutable State?

```text
Flyweight
```

---

### Reuse Expensive Results?

```text
Cache
```

---

### Reuse Expensive Objects?

```text
Object Pool
```

---

### Clone Existing Objects?

```text
Prototype
```

---

# What Architects Notice

Junior developers see:

```java
String s = "Hello";
```

Architects see:

```text
Flyweight
```

because immutable state is shared.

---

Junior developers see:

```java
cache.get(productId);
```

Architects see:

```text
Cache
```

because expensive work is avoided.

---

Junior developers see:

```java
dataSource.getConnection();
```

Architects see:

```text
Object Pool
```

because object creation cost is being amortized.

---

# The Bigger Insight

Many patterns use:

```java
HashMap

ConcurrentHashMap

Factory
```

internally.

That does not make them the same pattern.

The distinguishing factor is always:

```text
Intent
```

---

# The Architectural Insight

The easiest way to identify Flyweight is to ask:

```text
Are we sharing immutable state
across many logical objects
to reduce memory consumption?
```

If yes:

```text
Flyweight
```

If not, it is probably a different optimization strategy.

---

# Key Takeaways

* Flyweight shares immutable state.
* String Pool is a classic Flyweight example.
* Integer Cache is also Flyweight in intent.
* Singleton controls instance count.
* Cache avoids expensive computation or retrieval.
* Object Pool reuses expensive objects over time.
* Prototype creates copies.
* Intent matters more than implementation details.
