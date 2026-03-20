# Batch

When manipulating a lot of blocks, it is wiser to make use of a Batch to update all the chunks at once. There are 3 types of batches: `ChunkBatch`, `AbsoluteBlockBatch`, `RelativeBlockBatch`.

All batches allow you to set blocks without triggering updates immediately.

## ChunkBatch

Contains changes completely contained within 1 chunk. Coordinates are relative to the chunk (0-15).

```java
import net.minestom.server.instance.batch.ChunkBatch;
import net.minestom.server.instance.block.Block;

ChunkBatch batch = new ChunkBatch();
batch.setBlock(0, 0, 0, Block.STONE);
batch.setBlock(15, 60, 15, Block.GRASS_BLOCK);

// Apply to a specific chunk in an instance
batch.apply(instance, chunk);
```

## AbsoluteBlockBatch

Represents a set of block changes at specific world coordinates.

```java
import net.minestom.server.instance.batch.AbsoluteBlockBatch;

AbsoluteBlockBatch batch = new AbsoluteBlockBatch();
batch.setBlock(100, 40, -200, Block.GOLD_BLOCK);

// Apply to the instance (it knows where the blocks go)
batch.apply(instance, () -> System.out.println("Finished!"));
```

## RelativeBlockBatch

Represents a set of block changes relative to an arbitrary origin. Useful for schematics or structures that you want to paste in multiple places.

```java
import net.minestom.server.instance.batch.RelativeBlockBatch;
import net.minestom.server.coordinate.Vec;

RelativeBlockBatch batch = new RelativeBlockBatch();
// 0,0,0 relative to wherever we paste it
batch.setBlock(0, 0, 0, Block.OAK_LOG); 
// 1 block above
batch.setBlock(0, 1, 0, Block.OAK_LOG);

// Apply at specific coordinates
batch.apply(instance, new Vec(50, 64, 50), null);
```

## Why use Batch?

1.  **Performance**: Block changes are grouped and processed together, reducing overhead.
2.  **Network Efficiency**: Clients receive grouped chunk updates rather than individual block packets (where possible).
3.  **Thread Safety**: Batches handle synchronization internally during application.

