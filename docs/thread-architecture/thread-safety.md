# Thread Architecture

Minestom is multi-threaded by nature. It uses a "Parallel Tick" architecture where instances (worlds) can run in parallel if they are on different threads.

## Thread Safety

By default, interaction with game objects (`Entity`, `Instance`, `Block`) should be done **synchronously** (on the thread owning that object) or using thread-safe APIs.

- **Global View**: Most get/set methods on `MinecraftServer` or Managers are thread-safe.
- **Instance/Entity View**: Updating an entity or setting a block should be done inside a tick task or a scheduled task.

## Acquirable API

Minestom provides the `Acquirable` API to safe-guard access to objects that might be ticking on another thread.

```java
import net.minestom.server.thread.Acquirable;
import net.minestom.server.entity.Entity;

Acquirable<Entity> acquirableEntity = entity.getAcquirable();

// Sync access (blocks until safer)
acquirableEntity.sync(ent -> {
    ent.teleport(position);
});

// Async access (schedules update)
acquirableEntity.async(ent -> {
    // ...
});
```

However, in modern Minestom versions, you often use the `Scheduler` directly instead of raw Acquirables for logic.

```java
entity.scheduler().scheduleNextTick(ent -> {
    // Safe to modify 'ent' here
});
```

## Tick Threads

Each `Instance` is assigned to a `TickThread`. When you create multiple instances, the `ThreadDispatcher` attempts to balance them across available CPU cores.

- Entities in the same Instance always tick on the same thread.
- Entities in different Instances *may* tick on different threads.

**Do not** block the tick thread (e.g., File I/O, heavy computation, Thread.sleep). Use `ExecutionType.ASYNC` tasks for that.

