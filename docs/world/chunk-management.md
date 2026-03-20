# Chunk Management

Minestom handles chunks dynamically.

## Loading Chunks

By default, chunks are loaded automatically when players move (if `enableAutoChunkLoad(true)` is set on the instance settings).

You can also manually load chunks.

```java
CompletableFuture<Chunk> future = instance.loadChunk(chunkX, chunkZ);
future.join(); // Block until loaded (avoid on main thread)
```

## Unloading Chunks

Chunks are also unloaded automatically to save memory. You can force unload a chunk if needed.

```java
instance.unloadChunk(chunk);
```

## Custom Chunk Systems

You can override how chunks are supplied by implementing `ChunkSupplier`.

```java
instance.setChunkSupplier((instance, chunkX, chunkZ) -> new MyCustomChunk(instance, chunkX, chunkZ));
```

