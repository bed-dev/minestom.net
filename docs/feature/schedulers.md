# Schedulers

Minestom has a powerful scheduling API designed to work in sync with the server tick loop.

## Overview

The `Scheduler` allows you to schedule tasks that run either immediately, after a delay, or repeatedly. Each entity and instance has its own scheduler, and there is also a global server scheduler.

```java
import net.minestom.server.timer.TaskSchedule;
import net.minestom.server.MinecraftServer;

// Global scheduler
MinecraftServer.getSchedulerManager().buildTask(() -> {
    System.out.println("Hello from scheduler!");
}).schedule();
```

## Scheduling Tasks

Tasks are configured using `TaskSchedule` which defines when they run.

### Delayed Task

```java
// Run after 5 seconds
scheduler.buildTask(() -> {
    System.out.println("Delayed task");
}).delay(TaskSchedule.seconds(5)).schedule();
```

### Repeating Task

```java
// Run every tick (50ms)
scheduler.buildTask(() -> {
    System.out.println("Tick task");
}).repeat(TaskSchedule.tick(1)).schedule();
```

### Combined (Delay + Repeat)

```java
// Wait 10 seconds, then run every minute
scheduler.buildTask(() -> {
    System.out.println("Recurring task");
})
.delay(TaskSchedule.seconds(10))
.repeat(TaskSchedule.minutes(1))
.schedule();
```

## Task Ownership

It is best practice to use the scheduler of the object you are modifying.

- **Entity Scheduler**: Tasks are automatically cancelled if the entity is removed.
- **Instance Scheduler**: Tasks are cancelled if the instance is unregistered.

```java
entity.scheduler().buildTask(() -> {
    // This is safe! If entity is removed, this task stops.
    entity.teleport(position);
}).repeat(TaskSchedule.tick(1)).schedule();
```

## Task Management

When you schedule a task, you get a `Task` object which allows you to cancel it or check its status.

```java
Task task = scheduler.buildTask(...).schedule();

// Cancel later
task.cancel();

// Check if alive
boolean alive = task.isAlive();
```

## Execution Type

By default, scheduled tasks run on the server thread (sync). You can run tasks async using `ExecutionType.ASYNC`.

```java
import net.minestom.server.timer.ExecutionType;

scheduler.buildTask(() -> {
    // Heavy calculation off the main thread
})
.executionType(ExecutionType.ASYNC)
.schedule();
```
