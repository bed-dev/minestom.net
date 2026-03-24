# Entities

## Overview

In Minestom, all entities must extend `Entity` (or one of its subclasses). The `Entity` class provides the core server-side API (position, velocity, bounding box, etc.), while `EntityMeta` handles client-side synchronization (metadata).

## Entity Classes

Depending on your needs, you can extend different base classes:

- `Entity`: The most basic entity. Supports physics, metadata, and basic interaction.
- `LivingEntity`: Extends `Entity`. Adds health, attributes, and equipment support.
- `EntityCreature`: Extends `LivingEntity`. Adds AI, pathfinding, and goal selectors.
- `ItemEntity`: Represents a dropped item.
- `Player`: Represents a connected player.

### Spawning an Entity

To spawn an entity, you must set its instance.

```java
Instance instance = ...;
Pos spawnPosition = new Pos(0, 42, 0);

EntityCreature zombie = new EntityCreature(EntityType.ZOMBIE);
zombie.setInstance(instance, spawnPosition);
```

### Removing an Entity

To remove an entity from the world (despawn):

```java
zombie.remove();
```

## Entity Meta

Minestom separates server logic from client display using `EntityMeta`. Every entity type has a corresponding Meta class (e.g., `ZombieMeta`, `ArmorStandMeta`).

You can access and modify metadata to change how the entity looks to players.

```java
// Cast to the specific meta class
ZombieMeta meta = (ZombieMeta) zombie.getEntityMeta();

// Modify properties
meta.setBaby(true);
meta.setCustomName(Component.text("Baby Zombie"));
meta.setCustomNameVisible(true);
```

### Batch Updates

Updates to metadata are sent immediately by default. For multiple changes, it's better to batch them.

```java
meta.setNotifyAboutChanges(false);

// These changes won't be sent immediately
meta.setBaby(true);
meta.setCustomName(Component.text("Baby Zombie"));
meta.setCustomNameVisible(true);

meta.setNotifyAboutChanges(true); // Sends a single packet with all changes
```

## Physics and Movement

Minestom has a built-in physics engine.

```java
import net.minestom.server.coordinate.Vec;
import net.minestom.server.entity.Aerodynamics;

// Set velocity (blocks per tick related)
entity.setVelocity(new Vec(0, 10, 0));

// Toggle physics (gravity, collision)
entity.setNoGravity(true);
entity.setAerodynamics(new Aerodynamics(0.02, 0.91, 0.98)); // Custom air resistance
```

### Bounding Boxes

You can change the collision box of an entity, which affects interactions and physics.

```java
entity.setBoundingBox(0.6, 1.8, 0.6); // Width, Height, Depth
```

## Viewable (Tracking)

Entities are automatically viewable by players within range. You can control this behavior.

```java
// Disable auto-viewing (you must manually add viewers)
entity.setAutoViewable(false);

// Manually add a viewer
entity.addViewer(player);
```

## Events

Entities have their own `EventNode` which allows listening to events specific to that entity.

```java
import net.minestom.server.event.entity.EntityDamageEvent;

entity.eventNode().addListener(EntityDamageEvent.class, event -> {
    event.setCancelled(true); // Cancel damage
});
```

## Custom Entities

You can create custom entities by extending `Entity` or `EntityCreature`.

```java
public class MyRobot extends EntityCreature {
    public MyRobot() {
        super(EntityType.IRON_GOLEM);
        
        // Custom initialization
        setBoundingBox(1, 2, 1);
    }
    
    @Override
    public void update(long time) {
        super.update(time);
        
        // Custom tick logic (e.g., spin around)
        this.setRotation(getPosition().yaw() + 5, 0);
    }
}
```

## Scheduling

You can schedule tasks directly on the entity using the `Scheduler` API.

```java
import net.minestom.server.timer.TaskSchedule;

entity.scheduler().buildTask(() -> {
    entity.remove();
}).delay(TaskSchedule.seconds(10)).schedule();
```
