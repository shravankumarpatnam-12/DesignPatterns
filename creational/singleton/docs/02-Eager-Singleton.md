# Eager Initialization Singleton

## What is Eager Initialization?

Eager Initialization is the simplest way to implement the Singleton pattern.

The singleton instance is created during class loading, even before it is requested by the application.

```java
public final class EagerSingleton {

    private static final EagerSingleton INSTANCE =
            new EagerSingleton();

    private EagerSingleton() {
    }

    public static EagerSingleton getInstance() {
        return INSTANCE;
    }
}
```

---

## How It Works

The instance is created when the JVM loads and initializes the class.

```java
private static final EagerSingleton INSTANCE =
        new EagerSingleton();
```

The JVM guarantees that class initialization occurs only once per class loader.

As a result, only one instance of the singleton is ever created.

---

## JVM Class Loading Lifecycle

When the class is first referenced, the JVM performs:

```text
Loading
   ↓
Linking
   ↓
Initialization
   ↓
INSTANCE Created
```

During the initialization phase, static fields are initialized.

```java
private static final EagerSingleton INSTANCE =
        new EagerSingleton();
```

This ensures the object is created exactly once.

---

## Why Is It Thread Safe?

The JVM specification guarantees that class initialization is thread-safe.

If multiple threads attempt to access the class simultaneously:

```java
EagerSingleton.getInstance();
```

only one thread performs class initialization while other threads wait.

After initialization completes, all threads receive the same instance.

```text
Thread 1
   ↓
Class Initialization
   ↓
INSTANCE Created

Thread 2
   ↓
Uses Existing INSTANCE

Thread 3
   ↓
Uses Existing INSTANCE
```

No explicit synchronization is required.

---

## Advantages

### Simple Implementation

Easy to understand and implement.

```java
private static final EagerSingleton INSTANCE =
        new EagerSingleton();
```

No synchronization logic is needed.

---

### Thread Safe

Thread safety is provided by the JVM.

No need for:

* synchronized
* volatile
* locks
* double-checked locking

---

### Fast Access

Accessing the instance is extremely fast.

```java
EagerSingleton.getInstance();
```

The method simply returns a reference.

```java
return INSTANCE;
```

No locking overhead exists.

---

### Easy to Maintain

The implementation is straightforward and difficult to implement incorrectly.

---

## Disadvantages

### No Lazy Loading

The instance is created whether it is used or not.

Consider:

```java
public class Application {

    public static void main(String[] args) {
        System.out.println("Application Started");
    }
}
```

Even if `getInstance()` is never called, the object is created when the class is loaded.

This may waste memory and initialization time.

---

### Expensive Object Creation

Suppose the singleton loads configuration files, database connections, or large caches.

```java
private EagerSingleton() {
    loadConfiguration();
    connectDatabase();
    initializeCache();
}
```

The application pays this cost during startup even if the singleton is never used.

---

### Increased Startup Time

Large singleton objects may slow application startup.

This becomes important in:

* Cloud-native applications
* Serverless functions
* Microservices

where startup time matters.

---

## Memory Considerations

If the singleton is lightweight:

```java
private String applicationName;
```

the memory overhead is negligible.

However, if it manages:

* Large caches
* Connection pools
* Heavy resources

creating it eagerly may be inefficient.

---

## When Should You Use Eager Singleton?

Eager initialization is a good choice when:

* The singleton is lightweight.
* The instance is always required.
* Simplicity is preferred.
* Startup overhead is acceptable.

Examples:

* Application configuration
* Constants manager
* Lightweight utility services

---

## When Should You Avoid It?

Avoid eager initialization when:

* Object creation is expensive.
* Memory usage is critical.
* The singleton may never be used.
* Startup performance is important.

---

## Interview Question

### Why is Eager Singleton thread-safe?

Because JVM class initialization is guaranteed to be thread-safe. Static fields are initialized only once during class loading, ensuring that only a single instance is created.

---

## Summary

### Pros

* Simple implementation
* JVM-provided thread safety
* No synchronization overhead
* Fast access

### Cons

* No lazy loading
* Potential memory waste
* Increased startup time
* Expensive initialization occurs even if unused

### Production Suitability

Eager Singleton is suitable when the object is lightweight and guaranteed to be used throughout the application's lifetime. For expensive objects that may not always be required, lazy initialization approaches are usually preferred.
