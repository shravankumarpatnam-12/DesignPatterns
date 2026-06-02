# Factory Method Pattern

## Why Simple Factory Was Not Enough

In the previous chapter, we built a Simple Factory.

```java
PaymentGateway gateway =
        PaymentGatewayFactory.create(country);
```

This solved:

* Scattered creation logic
* Duplicate conditionals
* Tight coupling

But introduced a new problem.

Every time a new payment provider was added:

```java
AmazonPayGateway
```

the factory had to be modified.

```java
if(type.equals("AMAZON")) {
    return new AmazonPayGateway();
}
```

The factory became a bottleneck.

This violates one of the most important principles in software design:

> Open for Extension, Closed for Modification

The Gang of Four recognized this problem and introduced:

# Factory Method Pattern

---

# The Core Idea

Instead of:

```text
One Factory
     ↓
Creates Everything
```

Factory Method says:

```text
Each Concrete Type
     ↓
Knows How To Create Itself
```

Creation becomes polymorphic.

This is the key architectural shift.

---

# Understanding The Problem

Imagine a document processing framework.

Version 1 supports:

```text
PDF
```

Version 2 adds:

```text
WORD
```

Version 3 adds:

```text
EXCEL
```

Simple Factory:

```java
public class DocumentFactory {

    public Document create(String type) {

        if(type.equals("PDF"))
            return new PdfDocument();

        if(type.equals("WORD"))
            return new WordDocument();

        if(type.equals("EXCEL"))
            return new ExcelDocument();

        throw new IllegalArgumentException();
    }
}
```

Every new document type changes existing code.

The framework becomes increasingly difficult to maintain.

---

# GoF Solution

Move creation responsibility to subclasses.

Instead of:

```java
new PdfDocument()
```

inside a giant factory,

each subclass participates in object creation.

---

# Structure

Factory Method introduces two concepts:

## Product

Object being created.

```java
public interface Document {

    void open();
}
```

---

## Creator

Declares factory method.

```java
public abstract class Application {

    public abstract Document createDocument();

    public void process() {

        Document document =
                createDocument();

        document.open();
    }
}
```

Notice something important.

```java
createDocument();
```

is abstract.

The framework knows:

```text
A Document will exist
```

but does not know:

```text
Which Document
```

This is inversion of control.

---

# Concrete Products

```java
public class PdfDocument
        implements Document {

    @Override
    public void open() {
        System.out.println("PDF Opened");
    }
}
```

```java
public class WordDocument
        implements Document {

    @Override
    public void open() {
        System.out.println("Word Opened");
    }
}
```

---

# Concrete Creators

PDF application:

```java
public class PdfApplication
        extends Application {

    @Override
    public Document createDocument() {

        return new PdfDocument();
    }
}
```

WORD application:

```java
public class WordApplication
        extends Application {

    @Override
    public Document createDocument() {

        return new WordDocument();
    }
}
```

---

# Client Code

```java
Application app =
        new PdfApplication();

app.process();
```

Framework execution:

```text
process()
    ↓
createDocument()
    ↓
PdfDocument
```

The framework never knew which document was created.

This is polymorphic object creation.

---

# Why This Is Powerful

Simple Factory:

```text
New Type
    ↓
Modify Factory
```

Factory Method:

```text
New Type
    ↓
Add New Subclass
```

Existing code remains untouched.

This satisfies Open/Closed Principle.

---

# UML View

```text
                 Application
                       │
                       │
             createDocument()
                       │
        ┌──────────────┴──────────────┐
        │                             │
        │                             │
PdfApplication              WordApplication
        │                             │
        │                             │
        ▼                             ▼
 PdfDocument                 WordDocument
```

Creation responsibility is distributed.

No giant conditional block.

---

# The Hidden Genius

Most developers think:

> Factory Method creates objects.

That's only partially true.

The deeper purpose is:

> Allow frameworks to delegate object creation to extensions.

This idea appears everywhere in enterprise software.

---

# Relationship With Template Method Pattern

This is often missed in interviews.

Look carefully:

```java
public void process() {

    Document document =
            createDocument();

    document.open();
}
```

The algorithm:

```text
process()
```

is fixed.

Only one step varies:

```text
createDocument()
```

This is exactly how Template Method works.

Factory Method is frequently embedded inside a Template Method.

---

# Real World Example: Spring Framework

Consider:

```java
@Bean
public PaymentGateway paymentGateway() {

    return new StripeGateway();
}
```

Spring later calls:

```java
paymentGateway();
```

to create an object.

Your configuration method acts like a factory method.

You provide creation logic.

Spring controls the lifecycle.

---

# Real World Example: JDBC Drivers

Application code:

```java
Connection connection =
        DriverManager.getConnection(url);
```

Internally:

```text
MySQL Driver
PostgreSQL Driver
Oracle Driver
```

each participates in creation.

New drivers can be added without modifying existing JDBC code.

This follows the same extensibility philosophy.

---

# Real World Example: Logging Frameworks

Logging APIs:

```java
Logger logger =
        LoggerFactory.getLogger(...);
```

Eventually resolve to:

```text
Logback
Log4j
JUL
```

through extensible creation mechanisms.

Factories and Factory Methods work together extensively.

---

# Enterprise Use Case

Imagine an insurance platform.

Different countries require different policies.

```text
India Policy
US Policy
Germany Policy
Japan Policy
```

Simple Factory:

```java
if(country.equals(...))
```

becomes massive.

Factory Method:

```java
IndiaPolicyCreator
USPolicyCreator
GermanyPolicyCreator
JapanPolicyCreator
```

Each country extends independently.

Different teams can work without conflicts.

---

# Open/Closed Principle Achieved

Adding a new type:

```java
public class ExcelApplication
        extends Application {

    @Override
    public Document createDocument() {
        return new ExcelDocument();
    }
}
```

No existing code changes.

Extension replaces modification.

This is one of the strongest benefits of Factory Method.

---

# Trade-Offs

Every design pattern solves one problem by introducing another.

Factory Method is no exception.

---

## Advantage 1

Excellent extensibility.

---

## Advantage 2

Supports framework development.

---

## Advantage 3

Reduces large conditional statements.

---

## Advantage 4

Aligns with Open/Closed Principle.

---

## Disadvantage 1

More classes.

Simple Factory:

```text
1 Factory
```

Factory Method:

```text
Abstract Creator
Concrete Creator A
Concrete Creator B
Concrete Creator C
```

Class count grows.

---

## Disadvantage 2

More abstraction.

Developers must navigate:

```text
Creator
↓
Concrete Creator
↓
Product
```

Understanding code requires more effort.

---

## Disadvantage 3

Can Become Overengineered

For a small application:

```text
3 payment providers
```

Factory Method may be excessive.

Simple Factory might be sufficient.

Patterns should solve problems, not create them.

---

# Architect Perspective

Simple Factory centralizes creation.

Factory Method decentralizes creation.

This distinction becomes critical in large organizations.

Imagine:

```text
100 teams
500 modules
2000 implementations
```

One central factory becomes impossible to manage.

Factory Method allows teams to extend behavior independently.

This is why most frameworks prefer extensibility over centralized control.

---

# Evolution Of Object Creation

Historically, software evolved like this:

Stage 1:

```java
new Something()
```

Stage 2:

```java
Simple Factory
```

Stage 3:

```java
Factory Method
```

Stage 4:

```java
Abstract Factory
```

Stage 5:

```java
Dependency Injection Container
```

Every stage attempts to reduce coupling further.

---

# Key Takeaways

## What Problem Does Factory Method Solve?

Simple Factory's inability to scale.

---

## Main Idea

Move object creation responsibility into subclasses.

---

## Biggest Benefit

New functionality can be added through extension rather than modification.

---

## Most Important Architectural Insight

Factory Method is not primarily about object creation.

It is about building extensible frameworks where the framework controls execution while allowing applications to control object creation.

That idea later became one of the foundations of modern frameworks like Spring.

---