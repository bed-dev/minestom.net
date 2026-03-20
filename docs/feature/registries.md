# Registries

Minestom has a full implementation of Minecraft's Registry system, allowing you to define custom biomes, dimensions, and other registry-driven content.

## Dynamic Registry

The `DynamicRegistry` system allows you to add or modify registry entries.

### Dimensions

Creating custom dimensions is a common use case.

```java
import net.minestom.server.registry.DynamicRegistry;
import net.minestom.server.world.DimensionType;
import net.minestom.server.utils.NamespaceID;

// Create a dimension type builder
DimensionType customDimension = DimensionType.builder(NamespaceID.from("minestom:custom_dim"))
    .ambientLight(0.5f)
    .fixedTime(6000L)
    .hasSkyLight(true)
    .hasCeiling(false)
    .ultrawarm(false)
    .natural(true)
    .coordinateScale(1.0)
    .height(384)
    .minY(-64)
    .logicalHeight(384)
    .build();

// Register it
MinecraftServer.getDimensionTypeRegistry().register(customDimension);
```

### Biomes

You can also register custom biomes.

```java
import net.minestom.server.world.biome.Biome;
import net.minestom.server.world.biome.BiomeEffects;

Biome customBiome = Biome.builder()
    .name(NamespaceID.from("minestom:custom_biome"))
    .effects(BiomeEffects.builder()
        .fogColor(0x00FF00)
        .skyColor(0x0000FF)
        .waterColor(0x00FFFF)
        .waterFogColor(0x008888)
        .build())
    .precipitation(Biome.Precipitation.RAIN)
    .temperature(0.8f)
    .downfall(0.4f)
    .build();

MinecraftServer.getBiomeRegistry().register(customBiome);
```

## Static Registry

Static registries (like `Material`, `Block`, `EntityType`) are hardcoded based on the Minecraft version protocol and cannot be modified at runtime (except via resource pack/client modification which is out of scope for server API). However, you can retrieve data from them.

```java
// Access via static method
Material mat = Material.STONE;
int id = mat.id();

// Access via registry
Registry.MaterialEntry entry = MinecraftServer.getMaterialRegistry().getEntry(id);
```

## Tags (Registry Tags)

Registry tags (like `#minecraft:logs`) can be defined for items, blocks, fluids, etc.

These are managed via `MinecraftServer.getTagManager()`.

```java
import net.minestom.server.gamedata.tags.Tag;
import net.minestom.server.item.Material;

// Get a tag
Tag logs = MinecraftServer.getTagManager().getTag(Tag.BasicType.BLOCKS, "minecraft:logs");

// Check if a material is in the tag
boolean isLog = logs.contains(Material.OAK_LOG.namespace());
```

