# Singleton in Spring Framework

## Introduction

After learning various Singleton implementations, a common question arises:

> If Spring already creates Singleton beans by default, do we still need the Singleton Design Pattern?

In most Spring applications, the answer is **No**.

Spring's IoC container already manages object creation and lifecycle, making manual Singleton implementations unnecessary in most cases.

---

## Traditional Singleton vs Spring Singleton

Traditional Singleton:

```java id="1b3mx4"
public class Singleton {

    private static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

Accessed using:

```java id="09oy0q"
Singleton.getInstance();
```

---

Spring Singleton:

```java id="obdjf3"
@Service
public class UserService {
}
```

Accessed through Dependency Injection:

```java id="lj1bdm"
@Service
public class OrderService {

    private final UserService userService;

    public OrderService(UserService userService) {
        this.userService = userService;
    }
}
```

No `getInstance()` method is required.

---

## What Is a Spring Singleton?

By default, Spring creates one bean instance per IoC container.

```java id="v1ynz3"
@Service
public class UserService {
}
```

Spring creates:

```text id="jlwmf7"
One UserService Instance
       ↓
Shared Across Application
```

This behavior is called **Singleton Scope**.

---

## How Spring Creates Singleton Beans

Application startup:

```text id="xq4vfe"
Application Starts
        ↓
Spring Container Starts
        ↓
Bean Scanning
        ↓
Bean Creation
        ↓
Singleton Beans Stored
```

The container stores bean instances internally.

Whenever a dependency is requested:

```java id="lffn4q"
@Autowired
private UserService userService;
```

Spring returns the same instance.

---

## Demonstration

```java id="m8hywt"
@Service
public class UserService {
}
```

```java id="3o2frb"
@Component
public class TestComponent {

    @Autowired
    private UserService userService1;

    @Autowired
    private UserService userService2;

    @PostConstruct
    public void test() {

        System.out.println(
                userService1.hashCode());

        System.out.println(
                userService2.hashCode());
    }
}
```

Output:

```text id="m4y3uw"
12345678
12345678
```

Both references point to the same object.

---

## Important Difference

Many developers incorrectly assume:

```text id="ktdjgo"
Spring Singleton
      =
GoF Singleton
```

They are not exactly the same.

---

## GoF Singleton

Traditional Singleton guarantees:

```text id="z1a9qa"
One Instance Per JVM
```

Example:

```java id="xy3ihd"
Singleton.getInstance();
```

The class itself controls instance creation.

---

## Spring Singleton

Spring guarantees:

```text id="a1glgx"
One Instance Per Spring Container
```

The container controls instance creation.

---

## Visual Comparison

### Traditional Singleton

```text id="92kzpc"
JVM
 └── Singleton Instance
```

Only one instance exists in the JVM.

---

### Spring Singleton

```text id="85oyih"
Application Context
 └── UserService Instance
```

If multiple Spring contexts exist:

```text id="x4gz6m"
Context A
 └── UserService

Context B
 └── UserService
```

multiple instances can exist.

---

## Why Spring Is Preferred

Instead of:

```java id="mcrv4w"
Logger.getInstance()
      .log("Message");
```

Spring encourages:

```java id="7qk8pk"
@Service
public class UserService {

    private final Logger logger;

    public UserService(Logger logger) {
        this.logger = logger;
    }
}
```

Dependencies become explicit.

---

## Benefits of Spring Singleton

### Dependency Injection

Dependencies are visible.

```java id="x0ym9f"
public UserService(Logger logger)
```

No hidden dependencies.

---

### Better Testability

Testing becomes easier.

```java id="2vjlwm"
Logger mockLogger =
        Mockito.mock(Logger.class);

UserService service =
        new UserService(mockLogger);
```

No need to mock static calls.

---

### Loose Coupling

Classes depend on abstractions rather than global access methods.

```java id="nkg2n5"
UserService
      ↓
Logger Interface
```

instead of:

```java id="7d0az4"
UserService
      ↓
Logger.getInstance()
```

---

### Lifecycle Management

Spring manages:

* Object creation
* Dependency injection
* Initialization
* Destruction

Developers focus on business logic.

---

### AOP Integration

Spring can automatically add:

* Logging
* Security
* Transactions
* Metrics

without modifying the bean.

Example:

```java id="mk7b87"
@Transactional
public void saveUser() {
}
```

---

## Why Manual Singleton Is Discouraged in Spring

Consider:

```java id="c8cqph"
@Service
public class UserService {

    private static final UserService INSTANCE =
            new UserService();

    public static UserService getInstance() {
        return INSTANCE;
    }
}
```

This defeats many Spring features:

* Dependency Injection
* AOP
* Lifecycle Management
* Bean Configuration

The Spring container should manage object creation.

---

## Common Beginner Mistake

Creating manual Singletons inside Spring applications.

Example:

```java id="k0p3mv"
public class ConfigManager {

    private static final ConfigManager INSTANCE =
            new ConfigManager();

    public static ConfigManager getInstance() {
        return INSTANCE;
    }
}
```

In Spring:

```java id="ljvx1h"
@Component
public class ConfigManager {
}
```

is usually sufficient.

Spring already provides Singleton behavior.

---

## When Is Manual Singleton Still Useful?

Manual Singleton implementations remain useful when:

### Plain Java Applications

```java id="hiz6tv"
public static void main(String[] args)
```

without Spring.

---

### Libraries and Frameworks

When Spring is not available.

---

### JVM-Level Singleton Requirements

When the design explicitly requires:

```text id="p5bhlv"
One Instance Per JVM
```

regardless of dependency injection frameworks.

---

## Interview Questions

### Are Spring beans Singleton by default?

Yes.

Spring beans use Singleton scope by default.

---

### Does Spring Singleton mean one instance per JVM?

No.

Spring guarantees one instance per container, not necessarily one per JVM.

---

### Should we implement Singleton manually in Spring Boot?

Generally, no.

Spring already manages Singleton beans.

---

### What is the biggest advantage of Spring Singleton?

Integration with Dependency Injection and IoC.

---

## Architect-Level Perspective

The Singleton Design Pattern was originally created because applications needed a way to ensure a single shared instance.

Modern frameworks such as Spring solve this problem at the container level.

Instead of:

```java id="r3m0yy"
Singleton.getInstance()
```

Spring promotes:

```java id="1twodc"
Constructor Injection
```

This results in:

* Better testability
* Better maintainability
* Lower coupling
* Clear dependencies

For enterprise applications, container-managed singletons are generally preferred over manually implemented Singleton patterns.

---

## Summary

### Traditional Singleton

* Managed by the class itself
* One instance per JVM
* Accessed through `getInstance()`

### Spring Singleton

* Managed by Spring Container
* One instance per container
* Accessed through Dependency Injection

### Recommendation

For Spring Boot applications, prefer Spring-managed singleton beans instead of manually implementing the Singleton Design Pattern.

The framework already provides the benefits of Singleton while avoiding many of the architectural drawbacks associated with global access patterns.
