# Instances

## What is an instance

Instances are what replace "worlds" from vanilla Minecraft. They are lightweight and offer similar properties. There are multiple instance implementations, currently `InstanceContainer` and `SharedInstance` (both are explained below).

All instances can be accessed by using the `InstanceManager` or by getting an entity's instance.

```java
InstanceManager instanceManager = MinecraftServer.getInstanceManager();
// Or
Instance instance = entity.getInstance();
```

Internally, the default `Instance` class has its own sets to store the entities in it, but all chunk-based methods are abstract and meant to be implemented by a subclass.

## InstanceContainer

"Container" here means that this is an instance that can store chunks. Like every instance, it has its own set of entities.

You can create an `InstanceContainer` by calling:

```java
InstanceContainer instanceContainer = instanceManager.createInstanceContainer();
```

### Generators

In order to have a valid world generation, you need to specify which `Generator` the instance should use. Without it, no chunk can be generated. (check [here](https://minestom.net/docs/world/generation) to make your own)

```java
instanceContainer.setGenerator(YOUR_GENERATOR);
```

### Chunk Loading

`InstanceContainer` manages chunk loading and saving.

```java
// Set a chunk loader (e.g., AnvilLoader for standard Minecraft worlds)
instanceContainer.setChunkLoader(new AnvilLoader("path/to/world"));

// Enable/Disable auto chunk load (loading chunks when a player moves near them)
instanceContainer.enableAutoChunkLoad(true);
```

## SharedInstance

A `SharedInstance` needs to have an `InstanceContainer` linked to it. The "Shared" means that this is an instance that takes all of its chunks from its parent instance container.

What does this mean? If you break or place a block in the instance container, the shared instance will also reflect the change (same if you place a block using the shared instance methods, changes will be reflected in the instance container and all of its shared instances). However, **entities are separate**. This is perfect for lobby systems where you want players to be in the "same" world but not see each other, or for minigames where the map is static but entities differ.

You can create a `SharedInstance` using:

```java
SharedInstance sharedInstance = instanceManager.createSharedInstance(instanceContainer);
```

### Checking for Linked Instances

You can check if two instances share the same chunk data:

```java
boolean linked = SharedInstance.areLinked(instance1, instance2);
```

## Persistence and Saving

Instances can be saved using their `ChunkLoader`.

```java
// Save all chunks in the instance
instanceContainer.saveChunksToStorage();

// Save a specific chunk
instanceContainer.saveChunkToStorage(chunk);
```

To load a world from disk, you usually provide the loader during creation or set it immediately after.

```java
InstanceContainer instance = instanceManager.createInstanceContainer(new AnvilLoader("my-world"));
```

## Custom Instance Implementation

You are able to create your own class extending `Instance` and add entities to it.
In this case, the only thing you need to be aware of is that all instances need to be registered manually in the instance manager.

```java
instanceManager.registerInstance(YOUR_CUSTOM_INSTANCE);
```

This method is ONLY required if you instantiate your instance object manually. `InstanceManager#createInstanceContainer` and `InstanceManager#createSharedInstance` already register the instance internally.

## Time and Weather

Each instance has its own time and weather state.

```java
// Set time
instance.setTime(6000); // Noon

// Set time rate (0 to stop time or speed it up)
instance.setTimeRate(0); 

// Set weather
instance.setWeather(Weather.RAIN);
```

## World Border

You can set and manipulate the world border per instance.

```java
import net.minestom.server.instance.WorldBorder;

WorldBorder border = instance.getWorldBorder();
border.setDiameter(100);
border.setCenter(0, 0);
```

