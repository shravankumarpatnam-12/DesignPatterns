# Bill Pugh Singleton

## Introduction

The Bill Pugh Singleton is one of the most elegant Singleton implementations in Java.

It provides:

* Lazy Initialization
* Thread Safety
* No Synchronization Overhead
* No Explicit Locking
* No `volatile` Keyword

It achieves this by leveraging the JVM's class loading mechanism.

For this reason, many Java developers prefer it over Double-Checked Locking.

---

## Why Do We Need Another Singleton Implementation?

Let's compare previous approaches:

| Implementation         | Lazy | Thread Safe | Performance |
| ---------------------- | ---- | ----------- | ----------- |
| Eager Singleton        | ❌    | ✅           | ✅           |
| Lazy Singleton         | ✅    | ❌           | ✅           |
| Synchronized Singleton | ✅    | ✅           | ❌           |
| Double-Checked Locking | ✅    | ✅           | ✅           |

Although DCL solves the thread-safety and performance problems, it introduces complexity.

```java id="3v3c1s"
private static volatile Singleton instance;
```

Developers must understand:

* Java Memory Model
* Visibility
* Happens-Before
* Instruction Reordering

The Bill Pugh approach avoids all of this complexity.

---

## Implementation

```java id="fkt3ic"
public final class BillPughSingleton {

    private BillPughSingleton() {
    }

    private static class SingletonHolder {

        private static final BillPughSingleton INSTANCE =
                new BillPughSingleton();
    }

    public static BillPughSingleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

---

## Understanding the Core Idea

The key concept is:

```java id="7eek2l"
private static class SingletonHolder
```

The Singleton instance is moved into a static inner class.

```java id="0v7mmb"
private static final BillPughSingleton INSTANCE =
        new BillPughSingleton();
```

The JVM loads this inner class only when it is first accessed.

---

## Why Is It Lazy?

When the outer class is loaded:

```java id="nzb9qx"
BillPughSingleton
```

the inner class is **not loaded**.

```text id="j2gr6h"
Application Starts
        ↓
BillPughSingleton Loaded
        ↓
SingletonHolder Not Loaded
        ↓
No Object Created
```

The object is created only when:

```java id="0jz06r"
BillPughSingleton.getInstance();
```

is called.

---

## Execution Flow

### Step 1

Application starts.

```text id="gj2mch"
BillPughSingleton Loaded
```

No instance exists yet.

---

### Step 2

First call:

```java id="zb4t2w"
BillPughSingleton.getInstance();
```

---

### Step 3

The JVM loads:

```java id="9r0z7v"
SingletonHolder
```

---

### Step 4

Static initialization occurs:

```java id="2eab3o"
private static final BillPughSingleton INSTANCE =
        new BillPughSingleton();
```

Object is created.

---

### Step 5

The instance is returned.

```java id="pq5jkl"
return SingletonHolder.INSTANCE;
```

---

## JVM Class Initialization Guarantee

The Bill Pugh pattern relies on a powerful JVM guarantee:

> Class initialization is thread-safe.

The JVM ensures that a class is initialized exactly once.

If multiple threads attempt to initialize a class simultaneously:

```text id="h3zv8j"
Thread 1
Thread 2
Thread 3
```

only one thread performs initialization.

The remaining threads wait until initialization completes.

---

## Why Is It Thread Safe?

Suppose two threads execute:

```java id="qvwh4g"
BillPughSingleton.getInstance();
```

simultaneously.

Both attempt to access:

```java id="rqcnlf"
SingletonHolder.INSTANCE
```

The JVM guarantees:

```text id="zqf4bg"
Load SingletonHolder
        ↓
Initialize Once
        ↓
Create One Object
```

Only one instance can ever be created.

---

## Why No Synchronization Is Needed?

Unlike:

```java id="jld3g0"
public synchronized static Singleton getInstance()
```

the Bill Pugh implementation does not use explicit synchronization.

The JVM's class initialization process already provides the required thread safety.

---

## Why No volatile Is Needed?

Double-Checked Locking requires:

```java id="0ihr2g"
private static volatile Singleton instance;
```

to prevent instruction reordering.

Bill Pugh avoids this entirely because:

```java id="5wv10w"
private static final BillPughSingleton INSTANCE
```

is initialized during class initialization.

The JVM guarantees proper object construction before exposing the reference.

---

## Performance Characteristics

After initialization:

```java id="74j5mu"
BillPughSingleton.getInstance();
```

simply returns:

```java id="ng5cq8"
SingletonHolder.INSTANCE
```

No:

* Lock acquisition
* Monitor operations
* Synchronization overhead
* Volatile reads

This makes it extremely efficient.

---

## Memory Considerations

Like Lazy Singleton:

```text id="g2cn0k"
Object Created Only When Needed
```

Memory is not consumed until the instance is first requested.

This makes it more memory-efficient than Eager Initialization.

---

## Advantages

### Lazy Initialization

The object is created only when needed.

---

### Thread Safe

Thread safety is provided by JVM class initialization.

---

### No Synchronization Overhead

No locks are required.

---

### No volatile Required

Avoids Java Memory Model complexity.

---

### Simple and Elegant

Implementation is concise and easy to maintain.

---

## Disadvantages

### Less Intuitive

Many developers are unfamiliar with the static inner class mechanism.

At first glance:

```java id="ckw4w6"
private static class SingletonHolder
```

may appear unusual.

---

### Reflection Vulnerability

Like most Singleton implementations, it can still be broken using reflection.

---

### Serialization Vulnerability

Without additional protection:

```java id="1b8q24"
ObjectInputStream
```

can create a new object during deserialization.

---

## Comparison with Double-Checked Locking

| Feature              | DCL       | Bill Pugh |
| -------------------- | --------- | --------- |
| Lazy Initialization  | ✅         | ✅         |
| Thread Safe          | ✅         | ✅         |
| Uses volatile        | ✅         | ❌         |
| Uses Synchronization | ✅         | ❌         |
| Easy to Understand   | ❌         | ✅         |
| Performance          | Excellent | Excellent |

---

## When Should You Use Bill Pugh Singleton?

Good candidates:

* Configuration Managers
* Cache Managers
* Utility Services
* Shared Resource Managers

Any situation where:

```text id="yw5m9v"
One Instance
      +
Lazy Loading
      +
Thread Safety
```

is required.

---

## Interview Questions

### Why is Bill Pugh Singleton thread-safe?

Because JVM class initialization is guaranteed to be thread-safe.

---

### Why is it lazy?

The inner class is loaded only when `getInstance()` is called.

---

### Why does it not require synchronization?

Thread safety is provided by JVM class loading semantics.

---

### Why does it not require volatile?

Class initialization guarantees proper object construction and visibility.

---

## Summary

### Pros

* Lazy Initialization
* Thread Safe
* No Synchronization Overhead
* No volatile Required
* Clean Implementation

### Cons

* Reflection can break Singleton
* Serialization can break Singleton
* Less familiar to some developers

### Production Suitability

Bill Pugh Singleton is widely considered one of the best traditional Singleton implementations in Java. It combines lazy loading, thread safety, and excellent performance while avoiding the complexity of Double-Checked Locking.

For most plain Java applications, it is often preferred over DCL due to its simplicity and reliability.
