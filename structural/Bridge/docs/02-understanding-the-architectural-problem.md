# Understanding the Architectural Problem

## The Real Enemy: Independent Dimensions of Change

Many software designs begin with a simple hierarchy.

Consider a notification system.

```java
abstract class Notification {
    abstract void send();
}

class EmailNotification extends Notification {
    public void send() {
        System.out.println("Sending Email");
    }
}

class SmsNotification extends Notification {
    public void send() {
        System.out.println("Sending SMS");
    }
}
```

Everything looks clean.

The design is easy to understand.

The hierarchy is small.

Then reality arrives.

Business introduces priorities.

Notifications can now be:

* High Priority
* Low Priority

A common instinct is to use inheritance.

```text
Notification
│
├── EmailNotification
├── SmsNotification
│
├── HighPriorityEmailNotification
├── LowPriorityEmailNotification
├── HighPrioritySmsNotification
└── LowPrioritySmsNotification
```

Still manageable.

Then another requirement arrives.

Notifications must support:

* Email
* SMS
* Push

Combined with:

* High Priority
* Low Priority

The hierarchy becomes:

```text
HighPriorityEmailNotification
LowPriorityEmailNotification

HighPrioritySmsNotification
LowPrioritySmsNotification

HighPriorityPushNotification
LowPriorityPushNotification
```

The problem is not the number of classes.

The problem is why those classes exist.

## Detecting the Architectural Smell

Whenever you notice that two separate concerns are being combined through inheritance, ask:

> Do these concerns change independently?

In our example:

### Concern 1

Notification Type

* Email
* SMS
* Push

### Concern 2

Notification Priority

* High
* Low

A new notification type should not require changing priority logic.

A new priority should not require changing notification delivery logic.

Yet inheritance forces them together.

This creates coupling between unrelated decisions.

## The Mathematics of Class Explosion

Suppose we have:

```text
3 Notification Types
4 Priority Levels
5 Delivery Strategies
```

Inheritance requires:

```text
3 × 4 × 5 = 60 Classes
```

Add one more dimension:

```text
3 × 4 × 5 × 2 = 120 Classes
```

This growth is exponential.

Every new dimension multiplies complexity.

Architects recognize this immediately.

The issue is not code duplication.

The issue is that the design no longer scales.

## The Core Architectural Insight

Inheritance works best when there is only one dimension of variation.

For example:

```text
Animal
├── Dog
├── Cat
└── Bird
```

One hierarchy.

One reason to change.

Simple.

Problems begin when multiple dimensions evolve independently.

```text
Type
 ×
Priority
 ×
Strategy
 ×
Platform
```

Inheritance tries to represent all combinations.

Architecture seeks to separate them.

The moment you identify multiple independent dimensions of change, you should stop thinking about inheritance and start thinking about composition.

This realization is the foundation upon which the Bridge Pattern is built.
