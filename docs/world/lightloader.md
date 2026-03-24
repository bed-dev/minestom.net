---
description: >-
  This page describes how to add lighting to a minestom instance
---

# Lighting

Minestom includes a performant, asynchronous lighting engine that supports both Sky Light and Block Light updates.

## Enabling Lighting

Lighting is computed per-chunk or per-section.

```java
// When generating a chunk, you can choose to ignore lighting steps if performance is critical
// However, by default, Minestom calculates lighting when chunks are loaded or modified.
```

## Manual Updates

If you place blocks via `instance.setBlock`, lighting updates are usually handled automatically if `doBlockUpdates` is true (default).

```java
// This updates lighting around the block
instance.setBlock(0, 50, 0, Block.TORCH);
```

## Batch Lighting

When generating large structures or terrain, lighting updates can be expensive. The `UnitModifier` in the generator API handles batch updates efficiently.

## Configuration

Lighting behavior is often tied to the `DimensionType`.

```java
DimensionType dimension = DimensionType.builder(NamespaceID.from("minestom:dim"))
    .ambientLight(0.0f) // Darker ambient light makes block light more visible
    .hasSkyLight(true)  // Enable sky light calculations
    .build();
```

## Performance

The lighting engine (`LightCompute`) runs on the shared thread pool by default to avoid blocking the main tick loop.

### Caching

Lighting data is cached within the `LightingChunk`. When saving chunks, this data is persisted to disk so it doesn't need to be recomputed on load.

## Setting the chunk supplier

To use the LightingChunk class, you can call the `InstanceContainer#setChunkSupplier(LightingChunk::new)` method.
By default, lighting will be generated for chunks when they are sent to the client.

An example of using this method:
```java
Instance instance = ...;
instance.setChunkSupplier(LightingChunk::new);
```
## Precalculating Lighting

To load preload chunks and calculate lighting before players join, you can use the following code:

```java
var chunks = new ArrayList<CompletableFuture<Chunk>>();
ChunkRange.chunksInRange(0, 0, 32, (x, z) -> chunks.add(instanceContainer.loadChunk(x, z)));

CompletableFuture.runAsync(() -> {
    CompletableFuture.allOf(chunks.toArray(CompletableFuture[]::new)).join();
    System.out.println("load end");
    LightingChunk.relight(instanceContainer, instanceContainer.getChunks());
    System.out.println("light end");
});
```
