# Composite vs Decorator vs Visitor

## Why This Chapter Matters

Many developers learn patterns in isolation.

Real architects do not.

When designing a system, the question is rarely:

```text id="l38xgx"
How do I implement Composite?
```

The real question is:

```text id="dr9znq"
Why Composite instead of
Decorator, Visitor, Iterator,
or a simple hierarchy?
```

Understanding these tradeoffs is what separates pattern memorization from design expertise.

---

# Composite vs Decorator

These two patterns are often confused because both involve:

```text id="prh9e5"
Object Composition
```

But they solve completely different problems.

---

## Composite Goal

Composite models:

```text id="ec4svq"
Hierarchy
```

Example:

```text id="q4z4pq"
Folder
    ├── File
    └── Folder
```

Question being solved:

```text id="l6y1xt"
How do I represent
tree structures?
```

---

## Decorator Goal

Decorator models:

```text id="72t2nq"
Additional Responsibilities
```

Example:

```java id="cl7r1t"
new LoggingDecorator(
    new RetryDecorator(
        service
    )
);
```

Question being solved:

```text id="w4ms1w"
How do I add behavior
without modifying code?
```

---

## Structural Difference

Composite:

```text id="ltabv9"
Parent

contains

Many Children
```

Decorator:

```text id="z5h3ww"
Wrapper

contains

One Component
```

---

## Visual Comparison

Composite:

```text id="7hryo0"
Folder
│
├── File
│
└── Folder
```

Tree.

---

Decorator:

```text id="5rw0f2"
Logging

    ↓

Retry

    ↓

Service
```

Chain.

---

## Architect Shortcut

If you're modeling:

```text id="j14sdy"
Parent-Child Relationships
```

Think Composite.

If you're modeling:

```text id="yd8s8u"
Optional Behaviors
```

Think Decorator.

---

# Composite vs Inheritance

Another common confusion.

Example:

```text id="8mqen7"
Vehicle

Car

Bike

Truck
```

Looks hierarchical.

But it is NOT Composite.

---

## Why?

Composite requires:

```text id="c5axcv"
Recursive Containment
```

Example:

```text id="gu87i6"
Folder contains Folder
```

or

```text id="vovp5i"
Department contains Department
```

---

Inheritance only models:

```text id="u4jlzh"
Type Hierarchy
```

No recursive structure exists.

---

## Architect Rule

Ask:

```text id="mjlwm4"
Can parent contain itself
through the abstraction?
```

If yes:

```text id="0dxg4m"
Composite candidate.
```

If no:

```text id="cv25lx"
Probably inheritance.
```

---

# Composite vs Visitor

This is the most important comparison.

They frequently appear together.

---

## Composite Problem

Composite solves:

```text id="tvvcq5"
How do I represent
the hierarchy?
```

Example:

```text id="8tr90o"
Organization Tree
```

---

## Visitor Problem

Visitor solves:

```text id="9n7dgw"
How do I add operations
without changing the hierarchy?
```

---

## Example

Suppose we have:

```java id="f7hn2u"
Employee

Department
```

Composite already exists.

Now business asks for:

```text id="qepj9i"
Salary Report

Excel Export

JSON Export

Audit Report

Visualization
```

---

Option 1:

Modify:

```java id="04ff77"
Employee

Department
```

every time.

Bad.

---

Option 2:

Create visitors.

```java id="sgd3uw"
SalaryVisitor

AuditVisitor

ExportVisitor
```

Hierarchy remains unchanged.

Operations evolve independently.

---

## Architect Insight

Composite manages:

```text id="qqtwmp"
Node Growth
```

Visitor manages:

```text id="5twg8z"
Operation Growth
```

---

## Easy Way To Remember

If new node types appear frequently:

```text id="4a5w6l"
Composite works better.
```

If new operations appear frequently:

```text id="f7t8we"
Visitor becomes attractive.
```

---

# Composite + Visitor Together

This combination appears in:

* Compilers
* AST Processing
* IDEs
* Query Engines

Example:

```text id="z86xkd"
Expression Tree
```

Composite:

```text id="o1v6ka"
Literal

Addition

Multiplication
```

Visitor:

```text id="g8v3z1"
Evaluate

Optimize

Generate SQL

Generate Bytecode
```

This is a common architect-level design.

---

# Composite vs Iterator

These patterns often cooperate.

---

## Composite

Stores hierarchy.

Example:

```text id="jlwmgj"
Folder

Subfolder

File
```

---

## Iterator

Traverses hierarchy.

Example:

```java id="nmyr06"
iterator.next();
```

---

## Relationship

Composite answers:

```text id="3nhc1s"
How is data organized?
```

Iterator answers:

```text id="mjlwmz"
How do I navigate it?
```

---

## Real Example

File System:

Composite:

```text id="j8b2n6"
Folder

File
```

Iterator:

```text id="97gmrw"
Depth First Traversal

Breadth First Traversal
```

Both patterns often appear together.

---

# Composite vs Chain of Responsibility

Many developers confuse these because both can resemble trees.

---

## Composite

Represents:

```text id="gg17a2"
Structure
```

---

## Chain

Represents:

```text id="5jgj4n"
Request Processing
```

---

Composite:

```text id="2g1e97"
Organization Chart
```

Chain:

```text id="35rw3q"
Approval Workflow
```

Different goals.

Different designs.

---

# Composite vs Graph

This distinction becomes important in large systems.

---

## Composite

Requires:

```text id="1rj7ce"
Tree
```

Properties:

```text id="d4wq6w"
Single Parent

No Cycles
```

---

## Graph

Allows:

```text id="axlj7m"
Multiple Parents

Cycles
```

Example:

```text id="1bsh8f"
Social Network
```

A person can be connected to many people.

Composite is not appropriate.

---

# Architect Decision Framework

Ask these questions.

---

## Question 1

Am I modeling:

```text id="hncgs5"
Hierarchy
```

If yes:

```text id="pcz9g9"
Composite candidate.
```

---

## Question 2

Am I modeling:

```text id="mjlwm1"
Optional Behaviors
```

If yes:

```text id="4lxm6w"
Decorator candidate.
```

---

## Question 3

Am I adding:

```text id="o6xj17"
New Operations
```

without changing nodes?

If yes:

```text id="mjlwm2"
Visitor candidate.
```

---

## Question 4

Am I navigating:

```text id="ax13lz"
Existing Structure
```

If yes:

```text id="jjlwm3"
Iterator candidate.
```

---

## Question 5

Am I modeling:

```text id="7bw6dy"
Type Hierarchy
```

If yes:

```text id="rjlwm4"
Inheritance candidate.
```

---

# What Architects Actually Notice

Junior developers see:

```java id="t1dnhv"
List<Component>
```

Architects ask:

```text id="mjlwm5"
What kind of variability
exists in this system?
```

Variability in:

```text id="zjlwm6"
Structure
```

→ Composite

Variability in:

```text id="yjlwm7"
Behavior
```

→ Decorator

Variability in:

```text id="xjlwm8"
Operations
```

→ Visitor

Understanding the variability is more important than knowing the pattern names.

---

# The Bigger Insight

Most design patterns solve one of three problems:

```text id="wjlwm9"
Structure

Behavior

Creation
```

Composite is fundamentally a:

```text id="vjlwm0"
Structural Pattern
```

Its purpose is not adding behavior.

Its purpose is not object creation.

Its purpose is:

```text id="k8m2xl"
Representing recursive structures
in a uniform and extensible way.
```

Everything else is a consequence of that decision.

---

# Key Takeaways

* Composite models hierarchies; Decorator models capabilities.
* Composite manages structure; Visitor manages operations.
* Composite stores data; Iterator navigates data.
* Composite requires recursive containment.
* Composite is a tree pattern, not a graph pattern.
* Understanding system variability is the key to choosing the right pattern.
* Architects focus on the problem being solved, not the pattern name.
