# Player Skin

You can change a player's skin using the `PlayerSkin` record.

## Creating a Skin

Skins are defined by their texture value and signature (from Mojang's API).

```java
import net.minestom.server.entity.PlayerSkin;

PlayerSkin skin = new PlayerSkin("texture_value_base64", "signature_base64");
```

## Retrieving a Skin

To get a skin from a username, you usually need to query Mojang's session server. Minestom has a utility for this: `PlayerSkin.fromUsername(String)`.

**Note:** This method is blocking (performs HTTP request). Do not call it on the main thread!

```java
import net.minestom.server.entity.PlayerSkin;

// Async fetch
CompletableFuture.runAsync(() -> {
    PlayerSkin skin = PlayerSkin.fromUsername("Notch");
    if (skin != null) {
        player.setSkin(skin);
    }
});
```

## Applying to Player

```java
player.setSkin(skin);
```

This will automatically refresh the player for all viewers (and themselves).

