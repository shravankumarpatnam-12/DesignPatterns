# Double-Checked Locking (DCL) Singleton

## Why Do We Need Double-Checked Locking?

In the previous chapter, we solved the thread-safety problem using synchronization.

```java
public static synchronized SynchronizedSingleton getInstance()
```

Although correct, every call to `getInstance()` acquires a lock.

Even after the Singleton has already been created:

```java
instance != null
```

threads still pay the cost of synchronization.

The goal of Double-Checked Locking (DCL) is to:

* Maintain thread safety
* Support lazy initialization
* Reduce synchronization overhead

---

## Double-Checked Locking Implementation

```java
public final class DCLSingleton {

    private static volatile DCLSingleton instance;

    private DCLSingleton() {
    }

    public static DCLSingleton getInstance() {

        if (instance == null) {

            synchronized (DCLSingleton.class) {

                if (instance == null) {
                    instance = new DCLSingleton();
                }
            }
        }

        return instance;
    }
}
```

---

## Why Is It Called Double-Checked Locking?

The code checks for `null` twice.

### First Check

```java
if (instance == null)
```

This avoids synchronization once the object has been initialized.

---

### Second Check

```java
synchronized (DCLSingleton.class) {

    if (instance == null) {
        instance = new DCLSingleton();
    }
}
```

This prevents multiple threads from creating multiple instances.

---

## Execution Flow

### First Thread

```text
instance == null
       ↓
Acquire Lock
       ↓
instance == null
       ↓
Create Object
       ↓
Release Lock
```

---

### Second Thread

```text
instance != null
       ↓
Return Existing Object
```

No synchronization is required.

---

## Why Is DCL Faster?

Consider the synchronized version:

```java
public static synchronized Singleton getInstance()
```

Every call:

```text
Acquire Lock
      ↓
Check Instance
      ↓
Release Lock
```

With DCL:

```java
if(instance != null)
```

the method immediately returns the object.

```text
Check Instance
      ↓
Return Object
```

No lock acquisition.

No monitor operations.

Much better performance under high concurrency.

---

## The Most Important Question

### Why Is `volatile` Required?

Most interview answers stop at:

> "volatile makes it thread-safe."

This answer is incomplete.

To understand the real reason, we must understand how object creation works inside the JVM.

---

## Is This a Single Operation?

Consider:

```java
instance = new DCLSingleton();
```

Many developers think this is one operation.

It is not.

The JVM performs multiple steps.

```text
1. Allocate Memory
2. Initialize Object
3. Assign Reference
```

---

## What Should Happen?

Expected order:

```text
1. Allocate Memory
2. Execute Constructor
3. Assign Reference
```

Only after construction completes should other threads see the object.

---

## Instruction Reordering

For performance reasons, CPUs and compilers may reorder instructions.

The JVM may execute:

```text
1. Allocate Memory
2. Assign Reference
3. Execute Constructor
```

This optimization is normally safe in single-threaded programs.

However, it becomes dangerous in multi-threaded environments.

---

## Dangerous Scenario

### Thread 1

Starts creating the Singleton.

```text
Allocate Memory
      ↓
Assign Reference
```

At this point:

```java
instance != null
```

But the constructor has not finished executing.

---

### Thread 2

Executes:

```java
if(instance != null)
```

Result:

```text
true
```

Thread 2 receives the object.

---

### Problem

The object is only partially initialized.

Thread 2 may observe inconsistent state.

---

## Example

```java
public class DCLSingleton {

    private List<String> cache;

    private DCLSingleton() {
        cache = new ArrayList<>();
    }
}
```

Thread 2 may see:

```java
cache == null
```

even though the Singleton instance exists.

Possible result:

```java
NullPointerException
```

This bug is extremely difficult to diagnose.

---

## What Does volatile Do?

```java
private static volatile DCLSingleton instance;
```

The `volatile` keyword provides two important guarantees.

---

## Guarantee 1: Visibility

Without `volatile`, changes made by one thread may not be immediately visible to another thread.

```text
Thread A
    ↓
Updates Variable

Thread B
    ↓
May Read Stale Value
```

With `volatile`:

```text
Thread A
    ↓
Writes Value

Main Memory
    ↓
Thread B Reads Latest Value
```

All threads see the most recent value.

---

## Guarantee 2: Prevents Instruction Reordering

This is the critical requirement for DCL.

```java
private static volatile DCLSingleton instance;
```

ensures:

```text
Allocate Memory
      ↓
Execute Constructor
      ↓
Assign Reference
```

Other threads cannot observe a partially constructed object.

---

## Why Was DCL Broken Before Java 5?

Before Java 5, the Java Memory Model had weaker guarantees.

Even with careful implementation, Double-Checked Locking could fail.

Java 5 introduced the updated Java Memory Model (JSR-133), making DCL safe when combined with `volatile`.

This is why modern DCL implementations always use:

```java
private static volatile Singleton instance;
```

---

## Happens-Before Relationship

One of the core concepts of the Java Memory Model is the **Happens-Before Relationship**.

When a thread writes to a volatile variable:

```java
instance = singleton;
```

all writes performed before that operation become visible to threads that subsequently read the variable.

```text
Thread A Writes
        ↓
volatile Write
        ↓
Thread B Reads
```

The JVM guarantees visibility and ordering.

---

## Advantages

### Lazy Initialization

Object is created only when needed.

---

### Thread Safe

Correctly implemented DCL is thread-safe.

---

### Better Performance

Synchronization occurs only during initialization.

After creation:

```java
if(instance != null)
```

returns immediately.

---

### Suitable for High-Concurrency Systems

Reduces lock contention significantly.

---

## Disadvantages

### Complex Implementation

Compared to previous approaches:

```java
private static final Singleton INSTANCE
```

DCL is more difficult to understand and maintain.

---

### Easy to Implement Incorrectly

Common mistakes include:

```java
private static Singleton instance;
```

without `volatile`.

This implementation is broken.

---

### Requires JVM Knowledge

Understanding DCL properly requires knowledge of:

* JVM internals
* Java Memory Model
* CPU caches
* Visibility
* Instruction reordering

---

## Performance Comparison

| Implementation         | Thread Safe | Lazy | Synchronization Cost |
| ---------------------- | ----------- | ---- | -------------------- |
| Lazy Singleton         | No          | Yes  | None                 |
| Synchronized Singleton | Yes         | Yes  | High                 |
| DCL Singleton          | Yes         | Yes  | Low                  |

---

## Interview Questions

### Why do we need two null checks?

The first check avoids synchronization after initialization.

The second check ensures only one thread creates the object.

---

### Why is volatile required?

To prevent instruction reordering and guarantee visibility across threads.

---

### What happens if volatile is removed?

Another thread may observe a partially initialized object.

---

### Is DCL thread-safe?

Yes, when implemented with `volatile` in Java 5 and later.

---

## Summary

### Pros

* Lazy initialization
* Thread-safe
* Better performance than synchronized methods
* Reduced lock contention

### Cons

* More complex implementation
* Easy to implement incorrectly
* Requires understanding of JMM and volatile

### Production Suitability

Double-Checked Locking is a valid production solution and is commonly discussed in interviews. However, many teams prefer simpler alternatives such as the Bill Pugh Singleton because it provides thread safety and lazy initialization without requiring explicit synchronization or volatile variables.
