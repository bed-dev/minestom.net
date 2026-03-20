# Player UUID

Players are identified by their UUID.

## UUID Source

When a player connects, their UUID is determined by the `UuidProvider` configured in the `ConnectionManager`.

By default, if the server is in online mode (velocity/bungee forwarding enabled), it uses the real UUID. In offline mode, it might generate an offline UUID based on the username.

## Configuration

You can customize UUID generation:

```java
MinecraftServer.getConnectionManager().setUuidProvider((playerConnection, username) -> {
    // Return a custom UUID based on connection or username
    return UUID.nameUUIDFromBytes(("OfflinePlayer:" + username).getBytes());
});
```

## Retrieving Players

You can look up a player by UUID.

```java
Player player = MinecraftServer.getConnectionManager().getPlayer(uuid);
```

