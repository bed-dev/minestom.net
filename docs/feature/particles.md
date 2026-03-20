# Particles

Minestom provides a robust particle system that allows sending standard and custom particles to players.

## The Particle Interface

All particles are represented by the `Particle` sealed interface. This interface contains constants for all vanilla particles (via `Particles`) and specific records for complex particles (like `Dust`, `Block`, `Item`).

### Standard Particles

For simple particles without extra data, use the constants directly:

```java
import net.minestom.server.particle.Particle;

Particle particle = Particle.FLAME;
Particle explosion = Particle.EXPLOSION;
```

### Complex Particles

Some particles require additional data, such as color, block state, or item stack.

#### Redstone Dust

Use `Particle.Dust` to create colored dust particles.

```java
import net.minestom.server.color.Color;

// Create red dust (using RGB values)
Particle dust = Particle.DUST.withColor(new Color(255, 0, 0));

// Create dust with specific size
Particle largeDust = Particle.DUST.withScale(2.0f);
```

#### Block Particles

Used for block cracking or falling dust effects.

```java
import net.minestom.server.instance.block.Block;

// Create particles of a breaking stone block
Particle blockCrack = Particle.BLOCK.withBlock(Block.STONE);
```

#### Item Particles

Used for item breaking effects (like splash potions or tool breaking).

```java
import net.minestom.server.item.ItemStack;
import net.minestom.server.item.Material;

// Create particles of a splashing potion
Particle potionSplash = Particle.ITEM.withItem(ItemStack.of(Material.SPLASH_POTION));
```

## Sending Particles

To spawn particles for players, you need to send a `ParticlePacket`. There isn't a direct "world.spawnParticle" method by default in Minestom (unless you use an extension), so you send the packet to viewers.

### Creating the Packet

The `ParticlePacket` constructor requires several parameters to define the behavior.

```java
import net.minestom.server.network.packet.server.play.ParticlePacket;

ParticlePacket packet = new ParticlePacket(
    particle,             // The particle type
    false,                // specific visibility (usually false)
    x, y, z,             // Position (double)
    offsetX, offsetY, offsetZ, // Random offset (float)
    speed,                // Particle speed (float)
    count                 // Particle count (int)
);
```

### Sending to Players

You can send the packet to a specific player or all players in an instance or chunk.

```java
// Send to a single player
player.sendPacket(packet);

// Send to all players in an instance
instance.sendGroupedPacket(packet);

// Send to all players viewing a specific chunk
chunk.sendPacketToViewers(packet);

// Send to all players tracking an entity
entity.sendPacketToViewers(packet);
```

### Example: Spawning a Circle

```java
public void spawnCircle(Instance instance, Point center, double radius) {
    Particle particle = Particle.FLAME;
    
    for (int i = 0; i < 360; i += 10) {
        double angle = Math.toRadians(i);
        double x = center.x() + radius * Math.cos(angle);
        double z = center.z() + radius * Math.sin(angle);
        
        ParticlePacket packet = new ParticlePacket(
            particle,
            false,
            x, center.y(), z,
            0, 0, 0,
            0, 1
        );
        
        // Use a method to send to nearby players for efficiency
        instance.getPlayers().forEach(p -> {
            if (p.getPosition().distanceSquared(center) < 400) { // 20 blocks
                p.sendPacket(packet);
            }
        });
    }
}
```

## Particle Data Types

Here is a list of particles that require specific data:

| Particle Type | Data Class | Description |
| :--- | :--- | :--- |
| `DUST` | `Particle.Dust` | Colored dust with size |
| `DUST_COLOR_TRANSITION` | `Particle.DustColorTransition` | Dust that changes color |
| `BLOCK` | `Particle.Block` | Block breaking particles |
| `BLOCK_MARKER` | `Particle.BlockMarker` | Barrier/Light block particles |
| `FALLING_DUST` | `Particle.FallingDust` | Dust falling from a block |
| `ITEM` | `Particle.Item` | Item breaking particles |
| `VIBRATION` | `Particle.Vibration` | Sculk sensor vibration |
| `SCULK_CHARGE` | `Particle.SculkCharge` | Sculk catalyst charge |
| `SHRIEK` | `Particle.Shriek` | Sculk shrieker sound |
