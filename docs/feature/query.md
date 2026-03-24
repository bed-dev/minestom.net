# Query Protocol (GS4)

Minestom supports the GameSpy 4 (GS4) Query protocol, which allows external services (like server lists) to query your server status over UDP.

## Enabling Query

```java
// Start the query server on the same port as the main server (or custom)
// usually local address, port, and whether to print debug info
boolean success = Query.start(
    "0.0.0.0",
    25565,
    Logger.getLogger("Query")
);
```

## Creating a Query Response

You can listen for query events to customize the response.

```java
// Basic Query (Short stats)
MinecraftServer.getGlobalEventHandler().addListener(BasicQueryEvent.class, event -> {
    event.setMotd("My Minestom Server");
    event.setMap("Minestom Map");
    event.setMaxPlayer(100);
});

// Full Query (Detailed info)
MinecraftServer.getGlobalEventHandler().addListener(FullQueryEvent.class, event -> {
    event.setPlugins("Minestom");
    event.setVersion("1.21.4");
    // Add player list manually if needed, though default usually handles it
    event.setPlayers(List.of("Notch", "Jeb_"));
});
```



