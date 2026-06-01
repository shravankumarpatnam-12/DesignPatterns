# Singleton Design Pattern in Java

## What is a Singleton?

Singleton is a **Creational Design Pattern** that ensures:

1. A class has only one instance throughout the application lifecycle.
2. A global access point is provided to access that instance.

The Singleton pattern belongs to the Gang of Four (GoF) Creational Design Patterns and is one of the most commonly discussed patterns in Java interviews and enterprise applications.

---

## Why Do We Need Singleton?

Consider a configuration management system.

```java
public class ConfigurationManager {

    private String databaseUrl;

    public ConfigurationManager() {
        loadConfiguration();
    }

    private void loadConfiguration() {
        System.out.println("Loading configuration...");
    }
}
```

If multiple parts of the application create separate instances:

```java
ConfigurationManager config1 = new ConfigurationManager();
ConfigurationManager config2 = new ConfigurationManager();
ConfigurationManager config3 = new ConfigurationManager();
```

Output:

```text
Loading configuration...
Loading configuration...
Loading configuration...
```

Problems:

* Unnecessary memory consumption
* Repeated expensive initialization
* Inconsistent state across objects
* Difficult resource management

Instead, we can maintain a single shared instance.

```java
ConfigurationManager config =
        ConfigurationManager.getInstance();
```

Now every component accesses the same object.

---

## Real-World Examples

Singleton is commonly used when exactly one instance is required.

### Configuration Manager

```java
ConfigurationManager.getInstance()
```

Stores application properties and configuration.

---

### Logger

```java
Logger.getInstance()
```

Provides centralized logging.

---

### Cache Manager

```java
CacheManager.getInstance()
```

Maintains shared cache state.

---

### Feature Flag Manager

```java
FeatureFlagManager.getInstance()
```

Controls application features.

---

### Thread Pool Manager

```java
ThreadPoolManager.getInstance()
```

Maintains shared thread pools.

---

## Core Characteristics

A Singleton implementation generally contains:

### Private Constructor

```java
private Singleton() {
}
```

Prevents external object creation.

---

### Static Instance

```java
private static Singleton instance;
```

Stores the single object reference.

---

### Public Access Method

```java
public static Singleton getInstance()
```

Provides global access.

---

## Basic Structure

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {

        if (instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Although this implementation looks correct, it is not thread-safe and should not be used in production.

We will examine this problem in later chapters.

---

## UML Representation

```text
+-----------------------------+
|         Singleton           |
+-----------------------------+
| - instance : Singleton      |
+-----------------------------+
| - Singleton()               |
| + getInstance() : Singleton |
+-----------------------------+
```

---

## Benefits of Singleton

### Reduced Memory Consumption

Only one object exists.

```text
Without Singleton
-----------------
Config A
Config B
Config C

With Singleton
--------------
Shared Config
```

---

### Controlled Resource Usage

Expensive resources can be managed centrally.

Examples:

* Database connections
* Cache objects
* Logging frameworks

---

### Consistent Application State

All consumers use the same object.

```java
Config config1 =
        Config.getInstance();

Config config2 =
        Config.getInstance();
```

Both references point to the same object.

---

### Global Accessibility

The instance can be accessed from anywhere.

```java
Singleton.getInstance();
```

---

## Drawbacks of Singleton

Despite its popularity, Singleton has several disadvantages.

---
## Hidden Dependencies

One of the most significant architectural drawbacks of the Singleton pattern is the introduction of **hidden dependencies**.

A dependency is considered *hidden* when a class requires another component to function, but that requirement is not visible through the class's public API, constructor, or method signatures.

Consider the following example:

```java
public class UserService {

    public void createUser(User user) {

        Logger logger = Logger.getInstance();

        logger.log("Creating User");

        // Business Logic
    }
}
```

At first glance, creating an instance of `UserService` appears straightforward:

```java
UserService service = new UserService();
```

However, the class implicitly depends on a `Logger` singleton.

This dependency is not visible to consumers of the class and can only be discovered by inspecting the implementation.

### Why Is This a Problem?

When dependencies are hidden:

* The class becomes harder to understand.
* The required collaborators are not immediately obvious.
* Maintenance becomes more difficult.
* Testing becomes more complicated.
* Coupling between components increases.

A developer looking at the following code:

```java
UserService service = new UserService();
```

cannot easily determine whether `UserService` depends on:

* A logger
* A database connection
* A cache manager
* A message queue
* A configuration service

The implementation details are concealed within the class.

### Explicit Dependencies vs Hidden Dependencies

A better approach is to make dependencies explicit using constructor injection.

```java
public class UserService {

    private final Logger logger;

    public UserService(Logger logger) {
        this.logger = logger;
    }

    public void createUser(User user) {
        logger.log("Creating User");
    }
}
```

Now the dependency is immediately visible:

```java
UserService service =
        new UserService(logger);
```

Anyone reading the code can instantly understand that `UserService` requires a `Logger` to operate.

This is known as an **explicit dependency**.

### Hidden Dependency Explosion

The problem becomes more severe in large enterprise systems.

```java
public class PaymentService {

    public void processPayment() {

        Logger.getInstance();

        ConfigManager.getInstance();

        CacheManager.getInstance();

        MetricsManager.getInstance();

        NotificationManager.getInstance();
    }
}
```

Although the class depends on five separate services, its constructor still appears as:

```java
new PaymentService();
```

The actual dependencies remain hidden.

As the application grows, understanding and maintaining such code becomes increasingly difficult.

### Impact on Unit Testing

Hidden dependencies make testing significantly harder.

Consider the following implementation:

```java
public class UserService {

    public void createUser() {

        Logger.getInstance()
              .log("Creating User");
    }
}
```

A unit test automatically uses the real singleton instance:

```java
@Test
void shouldCreateUser() {

    UserService service =
            new UserService();

    service.createUser();
}
```

The test has no straightforward way to replace the logger with a mock implementation.

---

### Difficult Unit Testing

Singleton state may persist across test cases.

```java
Singleton.getInstance()
         .setValue("TEST");
```

The next test may unexpectedly observe the same value.

This causes test pollution.

---

### Global Mutable State

If the Singleton contains mutable data:

```java
singleton.setValue("A");
```

Any part of the application can modify it.

This can lead to unpredictable behavior.

---

### Tight Coupling

Tight coupling occurs when a class depends directly on a concrete implementation instead of an abstraction.

Singletons often introduce tight coupling because classes directly access dependencies using `getInstance()`.

#### Example

```java
public class UserService {

    public void createUser() {

        Logger.getInstance()
              .log("User created");

        // Business Logic
    }
}
```

Here, `UserService` is tightly coupled to the `Logger` Singleton. If the logging implementation changes (for example, from console logging to file logging), the application becomes harder to modify and test.

#### Better Approach: Dependency Injection

```java
public interface Logger {
    void log(String message);
}

public class ConsoleLogger implements Logger {

    @Override
    public void log(String message) {
        System.out.println(message);
    }
}

public class UserService {

    private final Logger logger;

    public UserService(Logger logger) {
        this.logger = logger;
    }

    public void createUser() {
        logger.log("User created");
    }
}
```

Now `UserService` depends on the `Logger` abstraction rather than a specific implementation, making the code easier to test, maintain, and extend.

**Key Takeaway:** Singleton-based global access often creates tight coupling, whereas Dependency Injection promotes loose coupling and better software design.

---

## Common Interview Question

### Is Singleton an Anti-Pattern?

The answer is:

**Not necessarily.**

Singleton becomes problematic when:

* It stores mutable global state.
* It is used everywhere.
* It hides dependencies.
* It makes testing difficult.

Singleton is appropriate when:

* Exactly one instance is required.
* The object is stateless or immutable.
* Resource sharing is necessary.

---

## Challenges in Implementing Singleton

A production-grade Singleton must address:

### Thread Safety

Can multiple threads create multiple instances?

---

### Lazy Initialization

Should the object be created only when needed?

---

### Performance

Can synchronization become a bottleneck?

---

### Reflection Attacks

Can reflection create additional instances?

---

### Serialization Attacks

Can deserialization create new objects?

---

### Clone Attacks

Can cloning break the Singleton guarantee?

---

## Evolution of Singleton Implementations

Throughout this repository we will study multiple implementations.

```text
1. Eager Initialization
2. Lazy Initialization
3. Synchronized Singleton
4. Double Checked Locking
5. Bill Pugh Singleton
6. Enum Singleton
```

Each implementation attempts to solve specific problems.

---
