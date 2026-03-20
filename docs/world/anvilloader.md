# Anvil Loader

Minestom supports loading worlds from the standard Minecraft "Anvil" format (Region files).

## Usage

To load a world, simply provide an `AnvilLoader` when creating your `InstanceContainer`.

```java
import net.minestom.server.instance.anvil.AnvilLoader;

// Path to the world folder (containing region/ folder)
AnvilLoader loader = new AnvilLoader("worlds/my_world");

InstanceContainer instance = instanceManager.createInstanceContainer(loader);
```

## Features

- **Chunk Loading**: Standard .mca region file parsing.
- **Chunk Saving**: Changes are saved back to disk when chunks are unloaded (or `saveChunksToStorage` is called).
- **Entities**: Entities are NOT loaded or saved by `AnvilLoader` by default in Minestom (unlike vanilla). You generally need to handle entity persistence separately or use an extension that adds this support.

