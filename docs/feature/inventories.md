# Inventory

Inventories in Minestom are containers for items. They can be viewed by players and come in various types (Chest, Hopper, Player Inventory, etc.).

Minestom has two main inventory implementations:
- `PlayerInventory`: A player's personal inventory (Hotbar, Main Inventory,Armor, Offhand).
- `Inventory`: A generic inventory (Chest, Furnace, etc.) that can be viewed by multiple players.

## Player Inventory

Each `Player` has a personal inventory, accessible via `player.getInventory()`.

```java
import net.minestom.server.inventory.PlayerInventory;
import net.minestom.server.item.ItemStack;
import net.minestom.server.item.Material;

PlayerInventory inventory = player.getInventory();

// Set an item in the main hand
inventory.setItemInMainHand(ItemStack.of(Material.DIAMOND_SWORD));

// Set armor
inventory.setHelmet(ItemStack.of(Material.DIAMOND_HELMET));
inventory.setChestplate(ItemStack.of(Material.DIAMOND_CHESTPLATE));
```

## Creating a GUI (Generic Inventory)

You can create custom inventories (chests, furnaces, etc.) and show them to players.

```java
import net.minestom.server.inventory.Inventory;
import net.minestom.server.inventory.InventoryType;
import net.kyori.adventure.text.Component;

// Create a chest inventory with a title
Inventory myInventory = new Inventory(InventoryType.CHEST_3_ROW, Component.text("My Loot"));

// Open it for a player
player.openInventory(myInventory);
```

### Adding Items

Items are set using slot indices, which start at 0.

```java
myInventory.setItemStack(0, ItemStack.of(Material.GOLD_INGOT));
```

### Click Handling

You can listen to clicks globally or per inventory. The most common way is via `addInventoryCondition`.

```java
myInventory.addInventoryCondition((player, slot, clickType, result) -> {
    // Cancel all clicks in this inventory to make it read-only
    result.setCancel(true);
    
    // Check specific slot action
    if (slot == 0) {
        player.sendMessage("You clicked the gold!");
        player.closeInventory();
    }
});
```

## Inventory Types

Minestom supports all vanilla inventory types:

- **Chests:** `CHEST_1_ROW`, `CHEST_2_ROW`, `CHEST_3_ROW`, `CHEST_4_ROW`, `CHEST_5_ROW`, `CHEST_6_ROW`
- **Utility:** `HOPPER`, `BREWING_STAND`, `BEACON`
- **Station:** `ANVIL`, `SMITHING`, `CARTOGRAPHY`, `GRINDSTONE`, `STONECUTTER`, `LOOM`, `CRAFTING`
- **Furnace:** `FURNACE`, `BLAST_FURNACE`, `SMOKER`
- **Dispenser/Dropper:** `WINDOW_3X3`
- **Merchant:** `VILLAGER`

## Listeners

You can add listeners for when the inventory is opened or closed by players.

```java
myInventory.addViewer(player); // Only adds them to viewers list (usually handled by openInventory)

// Callbacks
myInventory.addOpenListener(player -> {
    // Player opened inventory
});

myInventory.addCloseListener(player -> {
    // Player closed inventory
});
```

## Updating the Inventory

Similar to blocks and scoreboards, modifications to the inventory are immediately sent to all viewers.

```java
// Updates for everyone looking at it
myInventory.setItemStack(5, ItemStack.of(Material.APPLE));
```

