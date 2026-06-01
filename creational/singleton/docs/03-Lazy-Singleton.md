# Lazy Initialization Singleton

## What is Lazy Initialization?

Lazy Initialization delays object creation until it is actually needed.

Unlike Eager Initialization, the Singleton instance is not created during class loading. Instead, it is created the first time `getInstance()` is called.

```java
public final class LazySingleton {

    private static LazySingleton instance;

    private LazySingleton() {
    }

    public static LazySingleton getInstance() {

        if (instance == null) {
            instance = new LazySingleton();
        }

        return instance;
    }
}
```

---

## How It Works

When the application starts:

```text
Application Starts
        ↓
No Singleton Object Created
```

When the first call occurs:

```java
LazySingleton.getInstance();
```

The condition becomes:

```java
instance == null
```

A new object is created:

```java
instance = new LazySingleton();
```

All future calls return the same instance.

```text
First Call
    ↓
Object Created

Second Call
    ↓
Existing Object Returned

Third Call
    ↓
Existing Object Returned
```

---

## Why Is It Called "Lazy"?

Because object creation is postponed until it is actually required.

Instead of:

```text
Create Now
Use Later
```

it follows:

```text
Use First
Create Then
```

This can reduce startup time and memory consumption.

---

## Advantages

### Lazy Loading

The object is created only when needed.

```java
LazySingleton.getInstance();
```

If the method is never called, the object is never created.

---

### Reduced Memory Usage

Memory is consumed only when the Singleton is actually used.

This is beneficial for:

* Large caches
* Expensive resources
* Heavy initialization logic

---

### Faster Application Startup

Application startup is not delayed by Singleton creation.

```text
Application Start
       ↓
No Singleton Initialization
```

This can improve startup performance.

---

## The Major Problem: Not Thread Safe

The implementation appears correct in a single-threaded application.

However, it breaks in a multi-threaded environment.

Consider two threads executing simultaneously.

### Thread 1

```java
if (instance == null)
```

Result:

```text
true
```

Before creating the object, Thread 1 gets paused.

---

### Thread 2

```java
if (instance == null)
```

Result:

```text
true
```

Thread 2 creates the object.

```java
instance = new LazySingleton();
```

---

### Thread 1 Resumes

Thread 1 still believes:

```java
instance == null
```

So it creates another object.

```java
instance = new LazySingleton();
```

---

## Result

Two different objects are created.

```text
Thread 1 → Object A

Thread 2 → Object B
```

Singleton guarantee is broken.

---

## Race Condition

This issue is known as a **Race Condition**.

A race condition occurs when multiple threads access shared data concurrently and the final result depends on the timing of execution.

In our case:

```java
private static LazySingleton instance;
```

is shared by all threads.

Multiple threads compete to create the instance.

---

## Visual Representation

```text
Thread 1                    Thread 2

instance == null
                             instance == null

true                         true

Create Object A
                             Create Object B

Return Object A
                             Return Object B
```

Two instances now exist.

---

## Why Does This Happen?

The operation:

```java
if (instance == null) {
    instance = new LazySingleton();
}
```

is not atomic.

It consists of multiple steps:

```text
1. Read instance
2. Check for null
3. Create object
4. Assign reference
```

Another thread can interrupt between these steps.

---

## Demonstration

```java
public class Test {

    public static void main(String[] args) {

        Runnable task = () -> {
            LazySingleton singleton =
                    LazySingleton.getInstance();

            System.out.println(singleton.hashCode());
        };

        for (int i = 0; i < 1000000; i++) {
            new Thread(task).start();
        }
    }
}
```

Possible output:

```text
12345678
12345678
12345678
12345678
.....
.....
.....
98765432
```

Different hash codes indicate multiple instances.

---

## Why Is It Still Important?

Although this implementation is not suitable for production, it is extremely important from a learning perspective.

It teaches:

* Lazy Loading
* Shared State
* Race Conditions
* Thread Safety Problems

Most advanced Singleton implementations are attempts to solve this exact problem.

---

## How Can We Fix It?

One approach is to synchronize access.

```java
public static synchronized LazySingleton getInstance()
```

This ensures only one thread can create the object at a time.

However, synchronization introduces performance overhead.

We will discuss this in the next chapter.

---

## Interview Questions

### Is Lazy Singleton thread-safe?

No.

Multiple threads can create multiple instances simultaneously.

---

### What is the main advantage of Lazy Singleton?

Object creation is delayed until it is actually needed.

---

### What is the biggest drawback?

It is not thread-safe.

---

### What problem does it introduce?

Race conditions during object creation.

---

## Summary

### Pros

* Lazy loading
* Reduced memory usage
* Faster startup time
* Simple implementation

### Cons

* Not thread-safe
* Multiple instances can be created
* Race conditions occur
* Not suitable for production in multi-threaded applications

### Production Suitability

The basic Lazy Singleton implementation should generally be used only for educational purposes. Real-world applications require additional mechanisms such as synchronization, double-checked locking, or other thread-safe approaches.
