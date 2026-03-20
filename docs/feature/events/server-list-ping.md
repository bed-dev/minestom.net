# Server List Ping

Minestom handles Server List Ping (SLP) events, allowing you to customize what users see in their server list (MOTD, Player Count, Version, Favicon).

## Handling the Event

The `ServerListPingEvent` is triggered when a client pings the server.

```java
import net.minestom.server.event.server.ServerListPingEvent;
import net.minestom.server.ping.ResponseData;

MinecraftServer.getGlobalEventHandler().addListener(ServerListPingEvent.class, event -> {
    ResponseData response = event.getResponseData();
    
    // Set MOTD
    response.setDescription(Component.text("My Cool Minestom Server"));
    
    // Set player count
    response.setOnline(5);
    response.setMaxPlayer(100);
    
    // Set version text (hover over connection bars)
    response.setVersion("Minestom 1.21.4");
    
    // Set favicon (Base64 string)
    // response.setFavicon("data:image/png;base64,...");
});
```

## ResponseData

`ResponseData` is mutable and changes made to it will be reflected in the response sent to the client.

- `setDescription(Component)`: Sets the MOTD.
- `setOnline(int)`: Sets the online player count displayed.
- `setMaxPlayer(int)`: Sets the max player count displayed.
- `setProtocol(int)`: Sets the protocol version (useful for fooling older clients or proxies).
- `setVersion(String)`: Sets the version string.
- `setFavicon(String)`: Sets the server icon.
- `addPlayer(String)`: Adds a player name to the hover list.
- `clearPlayers()`: Clears the hover list.

