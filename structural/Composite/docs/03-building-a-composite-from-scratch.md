# Building a Composite From Scratch

## The Goal

Most tutorials stop after showing:

```java
Folder
 ├── File
 └── Folder
```

But production systems need much more:

* Child management
* Tree traversal
* Search operations
* Aggregation operations
* Validation rules
* Performance considerations

Let's build a Composite the way it appears in real systems.

---

# Step 1: Define the Component

Suppose we are building a product catalog.

Every catalog item should support:

```java
public interface CatalogComponent {

    String getName();

    double getPrice();

    void display();
}
```

This becomes the common abstraction.

Everything depends on it.

---

# Step 2: Create a Leaf

Leaf represents the smallest indivisible object.

Example:

```java
public class Product
        implements CatalogComponent {

    private final String name;
    private final double price;

    public Product(
            String name,
            double price) {

        this.name = name;
        this.price = price;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public double getPrice() {
        return price;
    }

    @Override
    public void display() {

        System.out.println(
            name + " : " + price
        );
    }
}
```

A Product has:

```text
No children
```

Therefore it is a Leaf.

---

# Step 3: Create the Composite

A category contains:

```text
Products

Subcategories
```

Both implement:

```java
CatalogComponent
```

Therefore:

```java
private List<CatalogComponent> children;
```

becomes possible.

---

# Composite Implementation

```java
public class Category
        implements CatalogComponent {

    private final String name;

    private final List<CatalogComponent>
            children = new ArrayList<>();

}
```

Notice:

```text
Category IS-A CatalogComponent

Category HAS CatalogComponents
```

This dual relationship is the essence of Composite.

---

# Step 4: Child Management

Composite usually provides:

```java
public void add(
        CatalogComponent component) {

    children.add(component);
}
```

```java
public void remove(
        CatalogComponent component) {

    children.remove(component);
}
```

```java
public List<CatalogComponent> getChildren() {

    return children;
}
```

Now hierarchy becomes dynamic.

---

# Building a Tree

```java
Category electronics =
        new Category("Electronics");

Category laptops =
        new Category("Laptops");

Product macbook =
        new Product("MacBook", 2500);

Product thinkpad =
        new Product("ThinkPad", 1800);

laptops.add(macbook);
laptops.add(thinkpad);

electronics.add(laptops);
```

Structure:

```text
Electronics
      |
      v
   Laptops
    /   \
   /     \
MacBook ThinkPad
```

---

# Step 5: Recursive Display

Composite delegates work to children.

```java
@Override
public void display() {

    System.out.println(name);

    for(CatalogComponent child : children) {

        child.display();
    }
}
```

Execution:

```text
Electronics

Laptops

MacBook

ThinkPad
```

No special handling.

Recursion does the work.

---

# Step 6: Recursive Aggregation

Calculate total price.

Leaf:

```java
@Override
public double getPrice() {

    return price;
}
```

Composite:

```java
@Override
public double getPrice() {

    double total = 0;

    for(CatalogComponent child : children) {

        total += child.getPrice();
    }

    return total;
}
```

Usage:

```java
electronics.getPrice();
```

Output:

```text
4300
```

The client doesn't know whether:

```text
Electronics

or

MacBook
```

is being processed.

Uniform treatment works perfectly.

---

# Understanding Recursive Execution

Consider:

```text
Electronics
    |
    +---- Laptops
             |
             +---- MacBook
             |
             +---- ThinkPad
```

Call:

```java
electronics.getPrice();
```

Execution:

```text
Electronics

    asks

Laptops

    asks

MacBook

returns 2500

    asks

ThinkPad

returns 1800

Laptops returns 4300

Electronics returns 4300
```

This is recursive aggregation.

A common Composite operation.

---

# Step 7: Tree Traversal

Architects often need:

```text
Search

Aggregation

Validation

Reporting
```

All are tree traversals.

Example:

Find product.

```java
public CatalogComponent find(
        String name) {

    if(this.name.equals(name)) {
        return this;
    }

    for(CatalogComponent child : children) {

        if(child instanceof Category category) {

            CatalogComponent found =
                    category.find(name);

            if(found != null) {
                return found;
            }
        }

        if(child.getName().equals(name)) {
            return child;
        }
    }

    return null;
}
```

This is depth-first traversal.

---

# Step 8: Enforcing Business Rules

Real composites often enforce rules.

Example:

```text
Maximum category depth = 5
```

Before adding child:

```java
add(component)
```

validate:

```text
Current depth

Allowed depth

Hierarchy rules
```

Composite often becomes the guardian of structural integrity.

---

# Transparent vs Safe Composite

This is a classic design decision.

---

## Transparent Composite

Component interface contains:

```java
add()

remove()

getChildren()
```

Example:

```java
public interface CatalogComponent {

    void add(...);

    void remove(...);
}
```

Problem:

Leaf objects receive methods that don't make sense.

Example:

```java
Product.add(...)
```

A product cannot contain children.

---

## Safe Composite

Only Composite contains:

```java
add()

remove()
```

Leaf stays simple.

Example:

```java
Product
```

has no child management methods.

Most enterprise systems prefer this approach.

---

# Common Mistake #1

Treating Composite Like Inheritance

Bad thinking:

```text
Category extends Product
```

Composite is not inheritance modeling.

Composite models hierarchy.

---

# Common Mistake #2

Using Concrete Types

Bad:

```java
List<Product>
```

Good:

```java
List<CatalogComponent>
```

The abstraction enables recursion.

Without it, Composite breaks.

---

# Common Mistake #3

Infinite Recursion

Dangerous:

```java
Category A

contains

Category B

contains

Category A
```

Now:

```java
display()
```

never terminates.

Real systems often validate against cycles.

---

# Common Mistake #4

Huge Recursive Trees

Imagine:

```text
100,000 Nodes
```

Deep recursion may cause:

```text
StackOverflowError
```

Large systems sometimes use:

```text
Iterative Traversal

Breadth-First Traversal

Streaming Traversal
```

instead of recursive methods.

---

# Performance Considerations

Simple operation:

```java
root.getPrice();
```

Complexity:

```text
O(n)
```

Every node is visited.

For:

```text
10 Nodes
```

trivial.

For:

```text
10 Million Nodes
```

significant.

Architects always evaluate tree size.

---

# Composite and Recursion

The most important realization:

Composite is fundamentally a recursion pattern.

The pattern works because:

```text
Node

contains

Node
```

through a shared abstraction.

Without recursion:

```text
No Composite
```

---

# The Architectural Insight

Junior developers see:

```java
List<CatalogComponent>
```

Architects see:

```text
A recursive domain model
capable of representing
arbitrarily deep hierarchies.
```

The implementation is simple.

The modeling power is enormous.

This is why Composite appears repeatedly in:

* File Systems
* DOM Trees
* Organization Structures
* Product Catalogs
* Menus
* Permission Trees
* Workflow Definitions

---

# Key Takeaways

* Composite consists of Leaf and Composite objects sharing the same abstraction.
* Composite contains children of its own abstraction.
* Most operations become recursive traversals.
* Aggregation and search naturally fit Composite structures.
* Safe Composite is usually preferred in enterprise systems.
* Recursive structures must guard against cycles and deep recursion.
* Composite is one of the most powerful patterns for modeling hierarchical domains.
