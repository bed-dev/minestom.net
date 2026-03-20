# Player Capabilities

Minestom provides a direct API to control standard player capabilities like gamemode, flying, and speed.

## GameMode

You can change a player's gamemode using `setGameMode`.

```java
import net.minestom.server.entity.GameMode;

player.setGameMode(GameMode.CREATIVE);
```

## Flying

To allow a player to fly (double-jump) and to set them flying:

```java
// Allow the player to toggle flight
player.setAllowFlying(true);

// Force the player to fly
player.setFlying(true);
```

## Speed

You can adjust walking and flying speeds.

```java
// Default is usually around 0.1 for walking
player.getAttribute(Attribute.MOVEMENT_SPEED).setBaseValue(0.2f);

// Flying speed
player.setFlyingSpeed(0.1f);
```

## Food and Saturation

Hunger management is mostly manual or handled by extensions, but the data storage is built-in.

```java
player.setFood(20);
player.setFoodSaturation(5.0f);
```

### Permission Level

To set the operator permission level (0-4), which affects vanilla command access client-side (and command blocks).

```java
player.setPermissionLevel(4);
```

## Resource Packs

You can send resource packs to players.

```java
import net.minestom.server.resourcepack.ResourcePack;

player.setResourcePack(ResourcePack.forced("https://example.com/pack.zip", "hash"));
```

