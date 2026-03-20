# Events

Minestom's event system is designed to be highly scalable and flexible, utilizing an event graph structure where events propagate through "Nodes".

## Understanding the Event Graph

Events traverse through a tree structure. The root of this tree is the `GlobalEventHandler`.

- **GlobalEventHandler**: The top-level handler. All events start here (unless scoped specifically).
- **EventNode**: A node in the graph. It can have children and listeners.
- **EventFilter**: Defines what *type* of events a node accepts (e.g., `EventFilter.PLAYER`, `EventFilter.ENTITY`).

When an event is fired, it starts at the Global handler and flows down to any child nodes whose filter matches the event type and whose condition (if any) is met.

## Listening to Events

The simplest way is to register a listener on the global handler.

```java
import net.minestom.server.MinecraftServer;
import net.minestom.server.event.GlobalEventHandler;
import net.minestom.server.event.player.PlayerLoginEvent;

GlobalEventHandler globalEventHandler = MinecraftServer.getGlobalEventHandler();

globalEventHandler.addListener(PlayerLoginEvent.class, event -> {
    event.setSpawningInstance(myInstance);
    // event.getPlayer() refers to the player logging in
});
```

## Determining Scope (EventContext)

Minestom events are "context-aware". This means an event knows if it belongs to a specific instance or entity.

- **Instance Scope**: Events related to blocks or world interactions are scoped to an `Instance`.
- **Entity Scope**: Events related to damage, movement, or interaction are scoped to an `Entity`.

This allows objects like `Instance` and `Entity` to have their own event handlers that only receive relevant events.

```java
// This listener will ONLY receive block break events happening in 'myInstance'
myInstance.eventNode().addListener(PlayerBlockBreakEvent.class, event -> {
    // ...
});

// This listener will ONLY receive damage events for 'myEntity'
myEntity.eventNode().addListener(EntityDamageEvent.class, event -> {
    // ...
});
```

## Creating Complex Event Structures

You can create your own `EventNode`s to organize your plugin/server logic. This is highly recommended for performance and organization.

```java
import net.minestom.server.event.EventNode;
import net.minestom.server.event.EventFilter;

// Create a node that only processes events for players in Creative Mode
EventNode<PlayerEvent> creativeNode = EventNode.value("creative-mode-filter", EventFilter.PLAYER, Player::isCreative);

// Add a listener to this node
creativeNode.addListener(PlayerBlockBreakEvent.class, event -> {
    // This code only runs if the player is in creative mode
});

// Attach the node to the global handler
MinecraftServer.getGlobalEventHandler().addChild(creativeNode);
```

### Why use Nodes?

1.  **Performance**: If the node condition (`Player::isCreative`) returns false, Minestom skips checking all listeners inside that node.
2.  **Organization**: You can group all listeners for a specific minigame or feature under one node and register/unregister the whole node at once.

## Custom Events

You can create your own events.

```java
import net.minestom.server.event.trait.CancellableEvent;

public class GameStartEvent implements CancellableEvent {
    private boolean cancelled;
    private final String gameData;

    public GameStartEvent(String gameData) {
        this.gameData = gameData;
    }

    public String getGameData() { return gameData; }

    @Override
    public boolean isCancelled() { return cancelled; }

    @Override
    public void setCancelled(boolean cancel) { this.cancelled = cancel; }
}
```

Calling the event:

```java
GameStartEvent event = new GameStartEvent("Spleef");
MinecraftServer.getGlobalEventHandler().call(event);

if (!event.isCancelled()) {
    // Start the game
}
```

## Listener Builder

For one-off listeners with expiration or specific filters:

```java
var listener = EventListener.builder(PlayerMoveEvent.class)
    .expireCount(1) // Run only once
    .handler(event -> System.out.println("First move!"))
    .build();

globalEventHandler.addListener(listener);
```

