# Items

Items are a core part of Minecraft, representing objects that can be held, worn, or used. In Minestom, items are represented by the `ItemStack` interface.

## Creating an ItemStack

The primary way to create an `ItemStack` is using the static factory method `ItemStack.of()`.

```java
import net.minestom.server.item.ItemStack;
import net.minestom.server.item.Material;

// Basic item
ItemStack stick = ItemStack.of(Material.STICK);

// Item with amount
ItemStack diamonds = ItemStack.of(Material.DIAMOND, 64);
```

## Modifying ItemStacks

`ItemStack` is immutable. Instead of modifying it in place, you create a new instance with the desired changes using methods like `withAmount`, `withMaterial`, or `withTag`.

```java
ItemStack stack = ItemStack.of(Material.IRON_INGOT);

// Change amount
stack = stack.withAmount(10);

// Use builder for multiple changes
stack = ItemStack.builder(Material.GOLD_INGOT)
    .amount(5)
    .meta(meta -> meta.customName(Component.text("Shiny Gold")))
    .build();
```

## Item Meta

Each `ItemStack` holds metadata (NBT) which stores properties like display name, lore, enchantments, and custom tags.

```java
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.format.NamedTextColor;

ItemStack sword = ItemStack.of(Material.DIAMOND_SWORD)
    .withCustomName(Component.text("Excalibur", NamedTextColor.GOLD))
    .withLore(List.of(Component.text("The legendary sword.")));
```

### Enchantments

You can add enchantments easily.

```java
import net.minestom.server.item.Enchantment;

ItemStack bow = ItemStack.of(Material.BOW)
    .withMeta(meta -> meta.enchantment(Enchantment.POWER, 5));
```

### Custom Model Data

Useful for resource packs.

```java
ItemStack modelItem = ItemStack.of(Material.PAPER)
    .withMeta(meta -> meta.customModelData(12345));
```

## NBT Tags

Items can store arbitrary NBT data using the Tag API.

```java
import net.minestom.server.tag.Tag;

ItemStack keyInfo = ItemStack.of(Material.TRIPWIRE_HOOK)
    .withTag(Tag.String("KeyID"), "secret-key-123");

// Reading the tag later
String id = keyInfo.getTag(Tag.String("KeyID"));
```

## Material

`Material` is an enum-like interface representing item types.

```java
Material mat = Material.STONE;

if (mat.isBlock()) {
    // It can be placed as a block
}
```

## Builders

For complex item creation, the builder pattern is cleaner.

```java
ItemStack powerfulPotion = ItemStack.builder(Material.POTION)
    .meta(PotionMeta.class, meta -> {
        meta.potionType(PotionType.STRONG_HEALING);
    })
    .amount(1)
    .build();
```

