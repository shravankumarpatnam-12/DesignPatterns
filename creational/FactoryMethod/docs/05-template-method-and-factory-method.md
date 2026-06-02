# Template Method and Factory Method

## Why This Chapter Exists

Most developers learn Factory Method and Template Method as two separate design patterns.

Textbooks present them independently.

Interviews ask separate questions about them.

Developers study them separately.

But experienced architects know something important:

> Factory Method and Template Method are frequently used together.

In fact, many classic Factory Method examples are actually Template Method examples with a Factory Method embedded inside them.

Understanding this relationship is one of the biggest jumps from design-pattern knowledge to framework-design knowledge.

---

# A Quick Refresher

Before we connect the patterns, let's briefly review them.

## Factory Method

Factory Method answers:

```text
Who creates the object?
```

Example:

```java
protected abstract Document createDocument();
```

Subclasses decide which object gets created.

---

## Template Method

Template Method answers:

```text
Who controls the workflow?
```

Example:

```java
public final void process() {

    step1();

    step2();

    step3();
}
```

The algorithm is fixed.

Subclasses customize specific steps.

---

# The Problem Framework Developers Face

Imagine you are building a document-processing framework.

Every document must follow the same workflow:

```text
Create Document
      ↓
Validate Document
      ↓
Open Document
      ↓
Generate Report
```

This workflow must never change.

However:

```text
PDF Documents
Word Documents
Excel Documents
```

require different document objects.

Question:

How do we keep the workflow fixed while allowing object creation to vary?

---

# The Naive Solution

Developers often start with conditionals.

```java
public void process(String type) {

    Document document;

    if(type.equals("PDF")) {
        document = new PdfDocument();
    }
    else {
        document = new WordDocument();
    }

    document.open();

    generateReport(document);
}
```

Problems:

* Violates OCP
* Tight coupling
* Hard to extend
* Difficult to maintain

We already solved this problem with Factory Method.

But now another insight appears.

---

# The Hidden Structure

Consider this code:

```java
public abstract class Application {

    protected abstract Document createDocument();

    public void process() {

        Document document =
                createDocument();

        document.open();

        generateReport(document);
    }
}
```

At first glance this looks like Factory Method.

But look carefully.

The method:

```java
process()
```

contains a fixed workflow.

```text
createDocument()
      ↓
open()
      ↓
generateReport()
```

This is Template Method.

---

# The First Big Insight

Inside:

```java
process()
```

there is a step:

```java
createDocument();
```

that varies.

That varying step is implemented using Factory Method.

Meaning:

```text
Template Method
        ↓
Uses
        ↓
Factory Method
```

This is one of the most common GoF combinations.

---

# Understanding Template Method

Let's isolate the Template Method part.

```java
public abstract class DataImporter {

    public final void importData() {

        readFile();

        validate();

        transform();

        save();
    }

    protected abstract void readFile();

    protected abstract void transform();

    protected void validate() {
        System.out.println("Common Validation");
    }

    protected void save() {
        System.out.println("Save To Database");
    }
}
```

The algorithm:

```text
read
validate
transform
save
```

never changes.

Only certain steps vary.

---

# Where Factory Method Appears

Now suppose importing requires different parser objects.

CSV:

```java
CsvParser
```

XML:

```java
XmlParser
```

JSON:

```java
JsonParser
```

We introduce:

```java
protected abstract Parser createParser();
```

Now:

```java
public final void importData() {

    Parser parser =
            createParser();

    parser.parse();

    validate();

    save();
}
```

Template Method controls workflow.

Factory Method controls object creation.

---

# Why GoF Loved This Combination

Factory Method alone answers:

```text
What object gets created?
```

Template Method answers:

```text
What process gets executed?
```

Together they answer:

```text
What process executes?
What object participates?
```

This creates highly extensible frameworks.

---

# Framework Thinking

This is where architectural thinking begins.

Most business applications think:

```text
Create Object
Call Method
Done
```

Frameworks think differently.

Frameworks say:

```text
I control execution.
You customize specific pieces.
```

Example:

```java
framework.run();
```

The framework owns the workflow.

Developers only provide extension points.

This is the essence of modern frameworks.

---

# Real World Example: Servlet API

When an HTTP request arrives:

```java
protected void doGet(...)
```

gets called.

But who called it?

Not your code.

The servlet container did.

Workflow:

```text
Receive Request
Create Objects
Authenticate
Route Request
Call doGet()
Build Response
Send Response
```

You only customize:

```java
doGet()
```

The framework controls everything else.

This is Template Method thinking.

---

# Real World Example: Spring Framework

Spring startup process:

```text
Load Configuration
Create Beans
Inject Dependencies
Apply Proxies
Initialize Context
```

Developers can customize parts:

```java
@Bean
```

```java
@BeanPostProcessor
```

```java
@EventListener
```

The workflow belongs to Spring.

Customization belongs to the application.

Exactly the same philosophy.

---

# Real World Example: JUnit

You write:

```java
@BeforeEach
```

```java
@Test
```

```java
@AfterEach
```

JUnit controls:

```text
Create Test Instance
Run Setup
Run Test
Run Cleanup
Generate Results
```

You provide only extension points.

This is Template Method in spirit.

---

# The Birth of Inversion of Control

Before these patterns:

```text
Application
      ↓
Calls Framework
```

After these patterns:

```text
Framework
      ↓
Calls Application
```

This reversal is known as:

# Inversion of Control (IoC)

Factory Method and Template Method were among the earliest patterns that encouraged IoC.

---

# Why Architects Care

Small systems rarely need this.

Large frameworks absolutely do.

Imagine:

```text
Spring Framework
Hibernate
JUnit
Tomcat
Kafka Connect
Maven
Gradle
```

All require:

```text
Stable Workflow
Customizable Behavior
```

Template Method provides the workflow.

Factory Method provides customization.

---

# Visualizing The Relationship

```text
Template Method

process()
    │
    │
    ▼

createProduct()
    │
    │
    ▼

Factory Method
```

Or:

```text
Fixed Algorithm
        │
        ▼

Variable Creation Step
        │
        ▼

Factory Method
```

The Template Method owns the process.

The Factory Method owns the creation decision.

---

# Advantages of Combining Them

## Advantage 1

Strong extensibility.

---

## Advantage 2

Framework remains stable.

---

## Advantage 3

Supports OCP.

---

## Advantage 4

Encourages IoC.

---

## Advantage 5

Simplifies framework evolution.

---

# Disadvantages

## More Abstraction

Developers must understand:

```text
Template Method
Factory Method
Inheritance Hierarchy
```

instead of simple procedural code.

---

## Harder Debugging

Execution flow often jumps through multiple classes.

Understanding runtime behavior becomes harder.

---

## Inheritance Dependency

Both patterns rely heavily on inheritance.

Modern systems often prefer composition when possible.

---

# Common Misunderstanding

Many developers think:

> Factory Method is primarily about object creation.

That is only partially true.

In large frameworks, Factory Method is often an extension mechanism embedded inside a Template Method.

The goal is not merely to create objects.

The goal is to allow customization without changing framework code.

---

# Architect Perspective

When architects design frameworks, they ask:

```text
What parts should remain stable?
```

and

```text
What parts should be customizable?
```

Stable parts become:

```text
Template Method
```

Customizable creation points become:

```text
Factory Method
```

This combination has powered software frameworks for decades.

---

# The Deep Insight

Template Method controls:

```text
When
```

Factory Method controls:

```text
What
```

Together they allow a framework to say:

> I decide when things happen. You decide what participates.

That simple idea became the foundation of framework-oriented programming.

---

# Key Takeaways

## What Does Template Method Control?

The workflow.

---

## What Does Factory Method Control?

Object creation.

---

## How Are They Related?

Factory Method is often a customizable step inside a Template Method.

---

## Why Is This Important?

It enables framework extensibility while preserving stability.

---

## Most Important Insight

Many famous Factory Method examples are actually Template Method examples that use Factory Method as an extension point.

Understanding this relationship is essential for understanding how modern frameworks are built.

---