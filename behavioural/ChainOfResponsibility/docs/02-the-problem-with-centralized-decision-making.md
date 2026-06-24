# The Problem with Centralized Decision Making

## Centralization Feels Efficient

Most systems begin with a single component making all decisions.

Consider an approval service.

```java id="t6m9wp"
public void approve(
        Expense expense) {

    if(expense.amount() < 1000) {
        manager.approve(expense);
    }
    else if(expense.amount() < 10000) {
        director.approve(expense);
    }
    else {
        vicePresident.approve(expense);
    }
}
```

The code is easy to understand.

The flow is explicit.

Initially, there is no obvious problem.

The challenge appears as business rules grow.

---

## The Growth Pattern

Business logic rarely remains stable.

New requirements appear.

```text id="w8p3nk"
Manager Approval

Director Approval

Vice President Approval

Compliance Review

Security Review

Financial Audit
```

The service expands.

```java id="g4k7yr"
if(...) { ... }
else if(...) { ... }
else if(...) { ... }
else if(...) { ... }
else if(...) { ... }
```

The code still works.

The architecture begins to suffer.

---

## The Accumulation Problem

Every new rule is added to the same place.

Over time:

```text id="p9m5vx"
Validation Rules

Authorization Rules

Approval Rules

Audit Rules

Compliance Rules
```

accumulate inside a central component.

The service becomes a decision engine.

Every business change targets the same file.

---

## The Bottleneck Effect

Imagine three teams.

```text id="k3r8wn"
Finance Team

Compliance Team

Security Team
```

Each introduces new processing rules.

All changes flow through:

```text id="a7m2pt"
ApprovalService
```

The service becomes a bottleneck.

Even unrelated changes compete for the same codebase.

---

## The Open-Closed Principle Problem

Suppose business introduces:

```text id="n4v7qx"
Regional Approval
```

The service changes.

Later:

```text id="f8m3wr"
Risk Assessment
```

The service changes again.

Every new rule requires modifying existing code.

The system is not closed for modification.

---

## The Testing Problem

As processing logic accumulates:

```text id="j6k9py"
Rule A

Rule B

Rule C

Rule D
```

the number of interactions increases.

A change to one rule may unexpectedly impact others.

Testing becomes increasingly expensive.

The service becomes harder to trust.

---

## The Ordering Problem

Many systems depend on execution order.

Example:

```text id="u2m5vk"
Authentication
      ↓
Authorization
      ↓
Validation
```

The sequence matters.

When all logic lives in one service:

```text id="h9r4xp"
Execution Order
```

becomes hidden within conditional branches.

Understanding the workflow becomes difficult.

---

## The Reusability Problem

Suppose a validation rule is needed elsewhere.

Current design:

```text id="q5m7wt"
Validation Logic
```

is buried inside:

```text id="x8k2pr"
ApprovalService
```

Reusing it requires duplication or extraction.

The design discourages modularity.

---

## The Architect's Perspective

Developers often see:

```text id="v4m9qn"
Too Many Conditions
```

Architects see:

```text id="t7k3pw"
Too Many Responsibilities
```

The issue is not syntax.

The issue is that multiple business decisions are concentrated inside a single component.

Each rule evolves independently.

Yet all rules are managed together.

---

## The Hidden Coupling

Consider:

```text id="b2r8mx"
Fraud Check
```

and

```text id="m6p4wt"
Compliance Check
```

These concerns are unrelated.

They change for different reasons.

However, centralized decision-making forces them into the same implementation.

This creates unnecessary coupling.

---

## The Scalability Problem

Imagine ten processing steps.

```text id="d8k5vn"
Validation

Authentication

Authorization

Fraud Check

Compliance Check

Risk Assessment

Audit

Approval

Notification

Logging
```

A centralized service owns everything.

As requirements grow:

```text id="f3r9wp"
One Service
       ↓
Many Rules
```

The complexity grows continuously.

---

## Recognizing the Smell

You should become suspicious when:

### One Service Receives Every New Rule

```text id="j8m2qy"
New Requirement
      ↓
Modify Same Class
```

---

### Processing Logic Is Difficult To Reorder

```text id="w4k7pn"
Step A

Step B

Step C
```

are tightly embedded.

---

### Different Teams Modify The Same Component

```text id="v7p5mr"
Security

Compliance

Finance
```

all changing one service.

---

### Business Rules Are Hard To Reuse

Logic cannot easily be extracted and recombined.

---

## The Core Architectural Insight

Each processing rule should own:

```text id="y5m8kt"
Its Decision

Its Responsibility
```

The workflow should not centralize every rule.

Instead, processing steps should be able to evolve independently.

This creates flexibility.

This improves maintainability.

This reduces coupling.

---

## The Real Problem Statement

At this point, the architectural challenge becomes clear:

> How can a request pass through multiple independent processing rules without creating a centralized decision engine?

This is the problem Chain of Responsibility was designed to solve.

The next chapter introduces Chain as a processing pipeline where each handler owns a single responsibility and decides whether to handle or forward the request.
