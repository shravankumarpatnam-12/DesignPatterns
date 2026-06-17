# Bridge as an Architectural Separation Strategy

## The Fundamental Idea

In the previous chapter, we discovered the real problem:

> Multiple dimensions of change are evolving independently, but inheritance forces them into a single hierarchy.

The Bridge Pattern solves this by separating those dimensions into independent hierarchies.

Instead of representing combinations through inheritance, it connects abstractions through composition.

This small shift changes everything.

## From Inheritance to Composition

Consider our notification example.

Inheritance tries to model every combination.

```text
HighPriorityEmailNotification
LowPriorityEmailNotification

HighPrioritySmsNotification
LowPrioritySmsNotification

HighPriorityPushNotification
LowPriorityPushNotification
```

Every new notification type multiplies priority classes.

Every new priority multiplies notification classes.

The hierarchy grows endlessly.

The Bridge Pattern asks a different question:

> Why are Notification Type and Priority in the same hierarchy?

They represent different concerns.

They should evolve independently.

## Separating the Two Dimensions

Instead of one hierarchy, create two.

### Hierarchy 1: Abstraction

Responsible for business behavior.

```text
Notification
├── HighPriorityNotification
└── LowPriorityNotification
```

### Hierarchy 2: Implementation

Responsible for delivery mechanism.

```text
MessageSender
├── EmailSender
├── SmsSender
└── PushSender
```

Notice what happened.

Priority no longer knows how messages are delivered.

Delivery mechanisms no longer care about priority.

Both dimensions became independent.

## The Bridge

The connection between these hierarchies is called the Bridge.

```text
Notification
      |
      | has-a
      ▼
MessageSender
```

Instead of inheriting delivery behavior, notifications delegate it.

```text
HighPriorityNotification
        |
        ▼
    EmailSender
```

or

```text
HighPriorityNotification
        |
        ▼
     SmsSender
```

or

```text
LowPriorityNotification
        |
        ▼
    PushSender
```

The combinations are created dynamically.

No new classes are required.

## Why This Scales Better

Let's compare.

### Inheritance Approach

```text
3 Notification Types
4 Priority Levels

Total Classes = 12
```

Add one more notification type:

```text
4 Notification Types
4 Priority Levels

Total Classes = 16
```

Growth is multiplicative.

### Bridge Approach

```text
Notification Hierarchy = 4 Classes

Sender Hierarchy = 4 Classes
```

Total:

```text
8 Classes
```

Add a new sender:

```text
Notification Hierarchy = 4 Classes

Sender Hierarchy = 5 Classes
```

Total:

```text
9 Classes
```

Growth becomes additive rather than multiplicative.

This is the architectural advantage of the Bridge Pattern.

## The Architect's Mental Model

Most developers see Bridge as:

> A design pattern that avoids class explosion.

Architects see something deeper:

> A technique for separating independent dimensions of change.

The pattern itself is not the goal.

The goal is reducing coupling between decisions that evolve for different reasons.

Whenever you encounter:

```text
Feature A changes independently

Feature B changes independently

But inheritance combines them
```

You have a strong signal that Bridge may be appropriate.

## Before Looking at Code

At this point, focus on the architecture, not the implementation.

The most important takeaway is:

> The Bridge Pattern separates abstraction from implementation so that both can evolve independently.

Everything else—the interfaces, classes, and UML diagrams—is simply a way of expressing this idea in code.
