# Real-World Java Examples

## Why This Chapter Matters

One of the biggest misconceptions about Composite is:

> "It is only useful for folders and files."

In reality, Composite appears whenever a system models:

```text
Hierarchy

Tree Structure

Parent-Child Relationships

Recursive Domains
```

Once you learn to recognize these signals, you'll start seeing Composite everywhere.

---

# Example 1: File Systems

This is the textbook example.

Structure:

```text
Root
│
├── Documents
│     ├── Resume.pdf
│     └── Notes.txt
│
└── Projects
      ├── App1
      └── App2
```

Common abstraction:

```java
FileSystemItem
```

Leaf:

```java
File
```

Composite:

```java
Directory
```

Directory contains:

```java
List<FileSystemItem>
```

not:

```java
List<File>
```

This allows arbitrary nesting.

---

## Operations

Examples:

```text
Calculate Size

Delete

Move

Copy

Search
```

Every operation recursively traverses the tree.

This is classic Composite.

---

# Example 2: HTML DOM

Every web page is essentially a Composite structure.

Example:

```html
<html>
    <body>
        <div>
            <button>Save</button>
        </div>
    </body>
</html>
```

Visualized:

```text
html
 |
 body
 |
 div
 |
 button
```

---

## Why DOM Is Composite

Every node implements:

```java
Node
```

Examples:

```java
Element

Text

Comment

Document
```

Containers hold:

```java
List<Node>
```

Operations:

```text
Render

Search

Validate

Traverse
```

all work recursively.

---

# Example 3: UI Frameworks

Most GUI frameworks are built around Composite.

Example:

```text
Window
│
├── Panel
│      ├── Button
│      └── TextField
│
└── Menu
```

---

## Swing Example

Common abstraction:

```java
Component
```

Examples:

```java
JButton

JLabel

JPanel
```

A panel contains:

```java
Component
```

objects.

A button is also a component.

Uniform treatment.

Classic Composite.

---

# Example 4: Organization Structures

Enterprise HR systems often model:

```text
CEO
│
├── VP Engineering
│      ├── Engineering Manager
│      │       ├── Developer
│      │       └── Developer
│
└── VP Sales
```

Abstraction:

```java
OrganizationUnit
```

Leaf:

```java
Employee
```

Composite:

```java
Department
```

Operations:

```text
Total Salary

Employee Count

Reporting Structure

Access Rules
```

become recursive traversals.

---

# Example 5: Product Catalogs

E-commerce systems use Composite extensively.

Example:

```text
Electronics
│
├── Laptops
│      ├── MacBook
│      └── ThinkPad
│
└── Phones
```

---

## Why Composite Fits

Categories contain:

```text
Products

Subcategories
```

Operations:

```text
Search

Pricing

Inventory Aggregation

Reporting
```

naturally traverse the hierarchy.

---

# Example 6: Permission Trees

Enterprise applications often model permissions like:

```text
Admin
│
├── User Management
│
├── Product Management
│
└── Reports
```

Permissions may contain:

```text
Sub-Permissions
```

Operations:

```text
Grant

Revoke

Validate

Audit
```

are recursive.

Composite becomes a natural solution.

---

# Example 7: Menu Systems

Application menus:

```text
File
│
├── New
├── Open
└── Save

Edit
│
├── Copy
└── Paste
```

Abstraction:

```java
MenuComponent
```

Leaf:

```java
MenuItem
```

Composite:

```java
Menu
```

Client code treats both uniformly.

---

# Example 8: Workflow Engines

Workflow systems often define:

```text
Workflow
│
├── Approval Step
│
├── Validation Step
│
└── Sub Workflow
        │
        ├── Step A
        └── Step B
```

Notice:

```text
Workflow contains Workflow
```

This recursive relationship strongly suggests Composite.

---

# Example 9: Cloud Infrastructure

Infrastructure tools frequently model resources hierarchically.

Example:

```text
Account
│
├── Region
│      ├── VPC
│      ├── Database
│      └── Queue
│
└── Region
```

Operations:

```text
Provision

Destroy

Monitor

Cost Calculation
```

often traverse the hierarchy recursively.

---

# Example 10: JSON and XML Documents

Consider JSON:

```json
{
  "user": {
    "address": {
      "city": "Berlin"
    }
  }
}
```

Tree representation:

```text
Root
 |
 User
 |
 Address
 |
 City
```

Parser implementations often model documents as Composite structures.

---

# Example 11: Spring Bean Definitions

While not a textbook Composite implementation, many Spring configuration structures are hierarchical.

Examples:

```text
Application Context

Bean Definitions

Dependency Graphs
```

Internally, recursive traversal and tree-like processing are common.

Understanding Composite helps understand such frameworks.

---

# Example 12: AST (Abstract Syntax Trees)

Compilers heavily use Composite.

Example:

```java
a + b * c
```

Tree:

```text
      +
     / \
    a   *
       / \
      b   c
```

Common abstraction:

```java
Expression
```

Leaf:

```java
Variable

Literal
```

Composite:

```java
BinaryExpression
```

Operations:

```text
Evaluate

Optimize

Compile

Transform
```

all use recursive traversal.

---

# The Pattern Hidden Everywhere

Whenever you encounter:

```text
Parent

Children

Recursive Structure
```

ask yourself:

```text
Can parent and child share
the same abstraction?
```

If the answer is yes:

```text
Composite is likely a candidate.
```

---

# What Architects Notice

Junior developers see:

```text
Folder

Subfolder

File
```

Architects see:

```text
Recursive Domain Model
```

The actual domain is irrelevant.

The structure is what matters.

Whether it's:

* Files
* Menus
* Employees
* Categories
* Workflows
* Permissions

the underlying pattern remains the same.

---

# Recognition Checklist

When you see:

✅ Tree structures

✅ Parent-child relationships

✅ Recursive traversal

✅ Aggregation operations

✅ Uniform treatment requirements

Think:

```text
Composite Pattern
```

---

# The Architectural Insight

Most enterprise systems eventually contain hierarchies.

The challenge isn't storing them.

The challenge is:

```text
Representing them in a way
that remains extensible
and easy to traverse.
```

Composite solves exactly that problem.

It provides a recursive object model that mirrors the structure of the real world.

That is why it appears repeatedly across frameworks, libraries, operating systems, compilers, and enterprise applications.

---

# Key Takeaways

* Composite is widely used in real-world systems.
* File systems, DOM trees, ASTs, menus, permissions, and workflows are classic examples.
* The pattern is fundamentally about recursive domain modeling.
* Parent and child share a common abstraction.
* Recursive operations become simple and consistent.
* Architects recognize Composite by structure, not by domain.
