# Blocks

## Overview

A `Block` in Minestom is an **immutable** object. This means if you want to change a property of a block, you get a *new* `Block` object.

A block contains:
- **Registry Data**: The vanilla block type (Namespace & ID).
- **Properties**: Block states like `facing=north` or `waterlogged=true`.
- **NBT/Tags**: Custom data attached to the block (Tile Entity data).
- **BlockHandler**: Custom server-side logic (interaction, placement, tick).

## Basic Usage

```java
import net.minestom.server.instance.block.Block;

// Setting a simple block
instance.setBlock(0, 40, 0, Block.STONE);

// Modifying block state properties
Block tnt = Block.TNT.withProperty("unstable", "true");
instance.setBlock(0, 41, 0, tnt);
```

### Retrieving Blocks

```java
Block block = instance.getBlock(0, 40, 0);
if (block.compare(Block.STONE)) {
    // It is stone (ignoring extra data like NBT or handlers)
}
```

## Block Handlers

`BlockHandler` allows you to attach custom logic to blocks. This is how you implement custom blocks or override vanilla behavior (like signs, chests, or machines).

### Creating a Handler

```java
import net.minestom.server.instance.block.BlockHandler;
import net.kyori.adventure.key.Key;

public class CustomBlockHandler implements BlockHandler {

    @Override
    public Key getNamespaceId() {
        return Key.key("minestom", "custom_block");
    }

    @Override
    public void onPlace(Placement placement) {
        System.out.println("Block placed at " + placement.getBlockPosition());
    }

    @Override
    public void onDestroy(Destroy destroy) {
        System.out.println("Block destroyed");
    }

    @Override
    public boolean onInteract(Interaction interaction) {
        // Return true to cancel the interaction (e.g. preventing block placement if item in hand)
        interaction.getPlayer().sendMessage("You touched the block!");
        return false; // False means interaction was successful/handled, true usually cancels generic item use
    }
}
```

### Assigning a Handler

```java
BlockHandler myHandler = new CustomBlockHandler();

// We need to register the handler first so Minestom knows it exists for persistence
MinecraftServer.getBlockManager().registerHandler(myHandler.getNamespaceId(), () -> myHandler);

// Now apply it to a block
Block customBlock = Block.GOLD_BLOCK.withHandler(myHandler);

instance.setBlock(10, 50, 10, customBlock);
```

## Tags (NBT)

You can attach arbitrary NBT data to blocks using the Tag system. This data is saved with the chunk.

```java
import net.minestom.server.tag.Tag;

Tag<Integer> countTag = Tag.Integer("click_count");

// Increment a counter inside the block
Block block = instance.getBlock(x, y, z);
int current = block.getTag(countTag); // Returns 0 (default) if not present
block = block.withTag(countTag, current + 1);

instance.setBlock(x, y, z, block);
```

*Note: Since blocks are immutable, `withTag` returns a new block object. You MUST call `setBlock` to update the world.*

## Block Placement Rules

`BlockPlacementRule` defines how a block state is determined when placed (e.g., stairs facing the player).

```java
import net.minestom.server.instance.block.rule.BlockPlacementRule;

public class MyStairRule extends BlockPlacementRule {
    public MyStairRule(Block block) {
        super(block);
    }

    @Override
    public Block blockPlace(PlacementState placementState) {
        // Calculate facing based on player yaw
        // Return correct block state
        return block.withProperty("facing", "north");
    }
}
```

This must be registered in the `BlockManager`.

```java
MinecraftServer.getBlockManager().registerBlockPlacementRule(new MyStairRule(Block.OAK_STAIRS));
```

