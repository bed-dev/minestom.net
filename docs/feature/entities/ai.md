# AI

## Overview

Entity AI in Minestom is built around the concept of **Goals** and **Targets**. An `EntityCreature` (or subclass) can have multiple `EntityAIGroup`s, each containing a set of logic to dictate behavior.

## AI Groups

An `EntityAIGroup` manages:
- **Goal Selectors**: Defines *what* the entity should do (e.g., attack, stroll, look around).
- **Target Selectors**: Defines *who* the entity should target (e.g., nearest player, last damager).

Calls to `addAIGroup` on an entity allow you to define priority-based behaviors. Groups are checked in order; if a goal from the first group is active, subsequent groups might be ignored depending on configuration.

## Example Configuration

Here is a complete example of a Zombie that attacks players, walks around randomly, and looks at things.

```java
import net.minestom.server.entity.EntityCreature;
import net.minestom.server.entity.EntityType;
import net.minestom.server.entity.Player;
import net.minestom.server.entity.ai.goal.*;
import net.minestom.server.entity.ai.target.*;
import net.minestom.server.utils.time.TimeUnit;
import java.util.List;

public class MyZombie extends EntityCreature {

    public MyZombie() {
        super(EntityType.ZOMBIE);

        // Add an AI group with Goals and Targets
        addAIGroup(
            List.of(
                // Priorities are implicit by order (first valid goal is chosen)
                new MeleeAttackGoal(this, 1.6, 20, TimeUnit.SERVER_TICK),
                new RandomStrollGoal(this, 10), // Walk around nearby
                new RandomLookAroundGoal(this, 20)
            ),
            List.of(
                new LastEntityDamagerTarget(this, 32),
                new ClosestEntityTarget(this, 32, entity -> entity instanceof Player)
            )
        );
    }
}
```

## Available Goals

Minestom provides several built-in goals:

- `MeleeAttackGoal`: Moves to the current target and attacks it.
- `RangedAttackGoal`: Shoots projectiles at the current target.
- `CombinedAttackGoal`: Switches between melee and ranged attacks based on distance/conditions.
- `FollowTargetGoal`: Follows the current target without attacking.
- `RandomStrollGoal`: Moves to a random nearby position.
- `RandomLookAroundGoal`: Rotates head randomly to simulate looking around.
- `DoNothingGoal`: Self-explanatory placeholder.

## Available Targets

- `ClosestEntityTarget`: Targets the nearest entity matching a filter (e.g., Players).
- `LastEntityDamagerTarget`: Targets the last entity that damaged this creature (revenge behavior).

## Custom Goals

You can implement `GoalSelector` to create custom behavior.

```java
import net.minestom.server.entity.ai.GoalSelector;

public class JumpGoal extends GoalSelector {
    public JumpGoal(EntityCreature entityCreature) {
        super(entityCreature);
    }

    @Override
    public boolean shouldStart() {
        // Condition to start the goal
        return entityCreature.isOnGround() && Math.random() < 0.05;
    }

    @Override
    public void start() {
        // Logic when the goal starts
        entityCreature.jump(10);
    }

    @Override
    public void tick(long time) {
        // Logic per tick while active
    }

    @Override
    public boolean shouldEnd() {
        // Should we stop?
        return entityCreature.isOnGround();
    }

    @Override
    public void end() {
        // Cleanup when goal ends
    }
}
```

## Custom Targets

Implement `TargetSelector` to define how entities find targets.

```java
import net.minestom.server.entity.Entity;
import net.minestom.server.entity.ai.TargetSelector;

public class SpecificPlayerTarget extends TargetSelector {
    private final String username;

    public SpecificPlayerTarget(EntityCreature entityCreature, String username) {
        super(entityCreature);
        this.username = username;
    }

    @Override
    public Entity findTarget() {
        // Logic to find a target. Return null if none found.
        return MinecraftServer.getConnectionManager().getOnlinePlayer(username);
    }
}
```

