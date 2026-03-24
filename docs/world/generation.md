# World Generation

Minestom provides a flexible API to generate worlds, ranging from flat worlds to complex terrain with biomes.

## The Generator Interface

The entry point for world generation is the `Generator` interface.

```java
@FunctionalInterface
public interface Generator {
    void generate(GenerationUnit unit);
}
```

This functional interface receives a `GenerationUnit`, which represents a section (16x16x16 chunk area) or a full chunk that needs to be generated.

### Basic Example: Flat World

```java
import net.minestom.server.instance.generator.Generator;
import net.minestom.server.instance.generator.GenerationUnit;
import net.minestom.server.instance.block.Block;

public class FlatGenerator implements Generator {
    @Override
    public void generate(GenerationUnit unit) {
        unit.modifier().fillHeight(0, 64, Block.GRASS_BLOCK);
    }
}
```

## GenerationUnit and Modifiers

The `GenerationUnit` allows you to request a `UnitModifier` via `unit.modifier()`. This modifier provides optimized methods for batch block and biome placement.

### Modifying Blocks

```java
UnitModifier modifier = unit.modifier();

// Fill entire unit
modifier.fill(Block.STONE);

// Fill up to specific height (useful for terrain)
modifier.fillHeight(0, 60, Block.STONE);

// Set single block relative to unit start
modifier.setRelative(0, 0, 0, Block.BEDROCK);

// Set all blocks based on a function (x, y, z -> Block)
modifier.setAll((x, y, z) -> {
    if (y < 10) return Block.BEDROCK;
    if (y < 60) return Block.STONE;
    return Block.AIR;
});
```

### Modifying Biomes

You can also set biomes for the unit.

```java
import net.minestom.server.world.biome.Biome;

// Fill entire unit with a single biome
modifier.fillBiome(Biome.PLAINS);

// Set biome based on function (x, y, z -> Biome)
modifier.setAllBiomes((x, y, z) -> {
    return (x > 100) ? Biome.DESERT : Biome.PLAINS;
});
```

*Note: Since biome data is stored in 4x4x4 quart-blocks (in 1.18+), precision might be lower than block-level.*

## Subdivision

For large-scale generation, a `GenerationUnit` can be subdivided into smaller units.

```java
List<GenerationUnit> subUnits = unit.subdivide();
for (GenerationUnit sub : subUnits) {
    // Process sub-unit independently (can be parallelized)
}
```

## Assigning a Generator to an Instance

You must set the generator when creating the `InstanceContainer`.

```java
InstanceContainer instance = instanceManager.createInstanceContainer();
instance.setGenerator(new FlatGenerator());
```

## Custom Noise and Terrain

Minestom does not provide noise libraries (like Perlin or Simplex) by default in the core, but you can use external libraries or implement your own math.

```java
public class NoiseGenerator implements Generator {
    @Override
    public void generate(GenerationUnit unit) {
        Point start = unit.absoluteStart();
        UnitModifier modifier = unit.modifier();

        modifier.setAll((x, y, z) -> {
            int absX = start.blockX() + x;
            int absY = start.blockY() + y;
            int absZ = start.blockZ() + z;

            // Simplified noise logic
            double height = Math.sin(absX * 0.1) * 10 + 64; 
            
            if (absY < height) {
                return Block.STONE;
            } else if (absY < 64) {
                 return Block.WATER;
            }
            return Block.AIR;
        });
    }
}
```
