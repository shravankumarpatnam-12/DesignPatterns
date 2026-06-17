# Bridge Design Pattern

## Before Learning the Pattern, Understand the Pain

Most developers learn design patterns incorrectly.

They start with UML diagrams.

Architects start with problems.

The Bridge Pattern was created to solve a specific architectural problem:

> Two dimensions of change evolve independently, but inheritance forces them into a single hierarchy.

This creates class explosion.

Consider a notification system.

Today you support:

* Email Notification
* SMS Notification

Tomorrow business asks for:

* High Priority Notification
* Low Priority Notification

Using inheritance:

```text
EmailNotification
SmsNotification

HighPriorityEmailNotification
LowPriorityEmailNotification

HighPrioritySmsNotification
LowPrioritySmsNotification
```

Everything still looks manageable.

Then business adds:

* Push Notification

Now:

```text
HighPriorityEmailNotification
LowPriorityEmailNotification

HighPrioritySmsNotification
LowPrioritySmsNotification

HighPriorityPushNotification
LowPriorityPushNotification
```

The number of classes grows multiplicatively.

This is not a coding problem.

This is an architecture problem.

The Bridge Pattern exists because inheritance couples multiple dimensions of change.

The goal is simple:

> Separate things that change for different reasons.

This idea is the foundation of the Bridge Pattern.
