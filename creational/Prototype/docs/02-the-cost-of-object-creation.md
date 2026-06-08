# The Cost of Object Creation

## Why This Chapter Exists

In the previous chapter, we learned:

```text id="p6c6c1"
Prototype Pattern Exists Because

Creating Objects Can Be Expensive
```

At first, this statement sounds strange.

Many developers think:

```java id="5a68yl"
new User();
```

takes virtually no time.

And for simple objects:

```text id="jlwm1j"
They Are Correct
```

Creating a small Java object is extremely cheap.

So why did the Gang of Four create an entire design pattern around avoiding object creation?

The answer is simple:

> Not all object creation is equal.

This chapter explores that idea.

---

# The Common Misconception

When developers hear:

```text id="afqszs"
Object Creation
```

they usually imagine:

```java id="c9s2i8"
public class User {

    private String name;

    private String email;
}
```

Creation:

```java id="vtfmpc"
new User();
```

Cost:

```text id="h95k4j"
Almost Nothing
```

No database.

No network.

No files.

No validation.

No complexity.

---

# The Real Cost Is Rarely The Object

This is one of the most important Prototype insights.

Most of the time:

```text id="z7x4gr"
The Object Is Cheap
```

The expensive part is:

```text id="qv7lc8"
Initialization
```

Developers often confuse the two.

---

# Example: Database Configuration

Imagine:

```java id="cz1rdi"
DatabaseConfiguration config =
        new DatabaseConfiguration();
```

Seems cheap.

But internally:

```text id="r0m3l5"
Load Properties File

Parse Configuration

Validate Settings

Initialize Connection Pools

Build Security Context
```

The constructor performs substantial work.

The cost comes from setup.

Not allocation.

---

# What Really Happens During Creation

For many enterprise objects:

```java id="fb3ryz"
new Something();
```

triggers:

```text id="fjgczh"
Validation

Parsing

Loading

Caching

Resource Allocation
```

The keyword:

```java id="d3g3l9"
new
```

is not expensive.

The work behind it often is.

---

# Cost Category #1: File Loading

Imagine a document editor.

Creating a template requires:

```text id="4gz7d5"
Load Template File

Load Images

Load Fonts

Parse Layout
```

Object:

```java id="4gwql4"
new ReportTemplate();
```

Actual work:

```text id="qb41mn"
Disk Access
```

which is much slower than memory access.

---

# Why Disk Access Is Expensive

CPU:

```text id="t0bnmx"
Nanoseconds
```

Memory:

```text id="q2v4x9"
Tens Of Nanoseconds
```

Disk:

```text id="vv4s6x"
Milliseconds
```

The difference is enormous.

A few milliseconds may not sound significant.

Multiply it by:

```text id="vlzq4i"
Thousands Of Objects
```

and the cost becomes substantial.

---

# Cost Category #2: Database Access

Suppose object creation requires:

```text id="6kh18v"
Configuration Lookup

Metadata Retrieval

Permission Loading
```

Constructor:

```java id="2d3n89"
new UserProfile();
```

Internal operation:

```text id="6h7tt7"
Database Query
```

Network and database access dominate the cost.

Not object allocation.

---

# Cost Category #3: Network Calls

Some systems initialize objects by contacting remote services.

Example:

```java id="1sldv0"
new CloudClient();
```

Internal work:

```text id="krl1ul"
Fetch Credentials

Validate Tokens

Download Configuration
```

Network latency may exceed:

```text id="ndxj6o"
100 Milliseconds
```

or more.

Creating hundreds of such objects becomes expensive.

---

# Cost Category #4: Parsing

Enterprise systems frequently parse:

```text id="zjqlho"
JSON

XML

YAML

CSV
```

Example:

```java id="1pt70x"
new WorkflowDefinition();
```

Internal operations:

```text id="4fhrdb"
Parse XML

Validate Schema

Build Internal Graph
```

Complex parsing often dominates creation cost.

---

# Cost Category #5: Validation

Imagine an insurance policy.

Construction requires:

```text id="ik81m9"
Eligibility Checks

Regulatory Rules

Business Constraints
```

Object:

```java id="v8ntnq"
new InsurancePolicy();
```

Actual work:

```text id="rvtxa4"
Thousands Of Validation Rules
```

The constructor becomes expensive.

---

# Cost Category #6: Building Object Graphs

This is extremely common.

Example:

```java id="ww2jca"
new Organization();
```

Internally:

```text id="1mwbsl"
Departments

Teams

Employees

Permissions

Roles
```

are also created.

The result:

```text id="2qhtm5"
One Constructor

Hundreds Of Objects
```

The cost grows rapidly.

---

# The Hidden Multiplier

A single expensive object may be acceptable.

The real problem appears when creation repeats.

Example:

```text id="jv4wr0"
Create Once
```

Cost:

```text id="7ybjjk"
100ms
```

Acceptable.

---

Example:

```text id="ik2glv"
Create 10,000 Times
```

Cost:

```text id="14kn9j"
~1000 Seconds
```

No longer acceptable.

---

# Real Example: Game Development

Games often contain:

```text id="d14i4o"
Enemies

Weapons

Characters

Effects
```

Each object may require:

```text id="3cg3l0"
Textures

Animations

Audio

AI Data
```

Creating every enemy from scratch wastes resources.

Copying becomes attractive.

This was one of the classic motivations behind Prototype.

---

# Real Example: UI Systems

Complex UI components may require:

```text id="mvkpfv"
Theme Loading

Layout Parsing

Font Initialization

Event Registration
```

Creating every component repeatedly can become expensive.

Frameworks often reuse existing structures.

---

# Real Example: Workflow Engines

Workflow systems frequently:

```text id="xjlwmk"
Parse XML

Build State Machines

Load Metadata
```

for each workflow.

Costly operation.

Better approach:

```text id="6ck9cq"
Build Once

Clone Many Times
```

This is Prototype thinking.

---

# Measuring Creation Cost

Architects rarely guess.

They measure.

Questions:

```text id="yfnc4q"
How Long Does Construction Take?

How Often Does It Occur?

How Much Memory Is Used?
```

Patterns should be driven by evidence.

Not assumptions.

---

# Premature Optimization Warning

This is critical.

Many developers learn Prototype and immediately think:

```text id="h6h9xn"
Avoid All Constructors
```

Wrong.

Most objects are cheap.

Example:

```java id="rjg14m"
new Point();
```

No Prototype needed.

No pattern needed.

Always measure first.

---

# When Creation Cost Matters

Prototype becomes interesting when:

```text id="vjlwm4"
Construction Is Expensive

Creation Happens Frequently

Copies Require Minimal Changes
```

All three conditions usually exist together.

---

# Copying vs Creating

Imagine:

```text id="zxgnyv"
Create New Report Template
```

Cost:

```text id="s7p0sm"
500ms
```

---

Copy Existing Template:

```text id="8s83r6"
5ms
```

Difference:

```text id="4f83r2"
100x Faster
```

This is the economic argument behind Prototype.

---

# Architect Perspective

Junior developers see:

```java id="1jxj7s"
new Object();
```

and think:

```text id="4lwkk5"
Object Allocation
```

Senior developers think:

```text id="feyz0s"
Initialization Cost
```

Architects think:

```text id="h6cn0r"
System-Wide Construction Cost
```

because they understand the cumulative impact across millions of operations.

---

# The Deep Insight

Prototype was never created because:

```text id="w5nby8"
new
```

is expensive.

Prototype exists because:

```text id="tb6nk3"
Initialization
```

can be expensive.

This distinction explains the entire pattern.

Without understanding it, Prototype appears unnecessary.

With understanding it, Prototype becomes obvious.

---

# Key Takeaways

## Is Object Allocation Expensive?

Usually no.

---

## What Is Often Expensive?

Initialization.

---

## Common Sources Of Cost?

```text id="hkgmfw"
File Loading

Database Access

Network Calls

Parsing

Validation

Object Graph Construction
```

---

## When Does Prototype Become Valuable?

When expensive construction occurs repeatedly.

---

## Most Important Insight

Prototype exists to avoid repeating expensive initialization—not merely to avoid object allocation.

---