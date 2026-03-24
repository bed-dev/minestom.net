# Tags

## Overview

A `Tag` represents a key-value pair that can be attached to any `TagReadable` object (like `Entity`, `ItemStack`, `Block`, `Instance`). This data is backed by NBT and is automatically serialized/deserialized.

Using Tags is the standard way to attach custom data to objects in Minestom.

```java
import net.minestom.server.tag.Tag;

Tag<Integer> moneyTag = Tag.Integer("money");
player.setTag(moneyTag, 100);

int currentMoney = player.getTag(moneyTag);
```

## Creating Tags

All tags are created via static factory methods in the `Tag` class.

### Basic Types

```java
Tag<Integer> intTag = Tag.Integer("my_int");
Tag<String> stringTag = Tag.String("my_string");
Tag<Double> doubleTag = Tag.Double("my_double");
Tag<Boolean> boolTag = Tag.Boolean("my_bool"); // Stored as byte 0/1
```

### Complex Types (NBT)

```java
// Stores an entire NBT Compound
Tag<CompoundBinaryTag> nbtTag = Tag.NBT("my_nbt");

// Stores a list of strings
Tag<List<String>> listTag = Tag.String("my_list_strings").list();
```

### Custom Types (Mapping)

You can map any complex object to a simpler tag type (like NBT or String).

```java
public class MyObject {
    public String name;
    public int value;

    // Serialization logic
    public static MyObject fromNBT(CompoundBinaryTag nbt) { ... }
    public CompoundBinaryTag toNBT() { ... }
}

Tag<MyObject> myObjectTag = Tag.Structure(
    "my_object",
    context -> MyObject.fromNBT(context), // Reader
    (context, obj) -> obj.toNBT()         // Writer
);
```

Or simpler mapping:

```java
// Map UUID <-> String
Tag<UUID> uuidTag = Tag.String("my_uuid")
    .map(UUID::fromString, UUID::toString);
```

## Tag Handlers

You can create standalone `TagHandler` objects to store tags without attaching them to a game object.

```java
import net.minestom.server.tag.TagHandler;

TagHandler handler = TagHandler.newHandler();
handler.setTag(Tag.String("key"), "value");

// Convert to NBT
CompoundBinaryTag nbt = handler.asCompound();
```

## Default Values

You can provide a default value to be returned if the tag is missing.

```java
Tag<Integer> scoreTag = Tag.Integer("score").defaultValue(0);

// Returns 0 if "score" is not set
int score = player.getTag(scoreTag);
```

## Structure Tags

Structures allow you to group multiple tags under a single namespace (NBT Compound).

```java
// Define a structure
Tag<CompoundBinaryTag> stats = Tag.Structure("stats", 
    Tag.Integer("kills"),
    Tag.Integer("deaths")
);

// This creates an NBT structure like:
// { "stats": { "kills": 10, "deaths": 5 } }
```
