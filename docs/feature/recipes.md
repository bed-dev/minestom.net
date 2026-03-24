# Recipes

Minestom's recipe system allows you to define custom recipes and sync them to clients using `RecipeManager`.

## Creating a Recipe

A `Recipe` is an interface that defines how a recipe is displayed and what it crafts. In Minestom, recipes are defined by their `RecipeDisplay`.

Since `Recipe` is an interface, you can implement it directly, or create simple wrapper classes.

### Example: Shaped Crafting Recipe

```java
import net.minestom.server.recipe.Recipe;
import net.minestom.server.recipe.display.RecipeDisplay;
import net.minestom.server.recipe.display.SlotDisplay;
import net.minestom.server.item.Material;
import net.minestom.server.item.ItemStack;
import java.util.List;

public class MyShapedRecipe implements Recipe {
    @Override
    public List<RecipeDisplay> createRecipeDisplays() {
        SlotDisplay stick = SlotDisplay.item(ItemStack.of(Material.STICK));
        SlotDisplay diamond = SlotDisplay.item(ItemStack.of(Material.DIAMOND));
        SlotDisplay sword = SlotDisplay.item(ItemStack.of(Material.DIAMOND_SWORD));
        SlotDisplay air = SlotDisplay.empty();

        // 3x3 Grid
        // D | - | -
        // D | - | -
        // S | - | -
        
        List<SlotDisplay> ingredients = List.of(
            diamond, air, air,
            diamond, air, air,
            stick, air, air
        );

        RecipeDisplay display = new RecipeDisplay.CraftingShaped(
            3, 3, // Width, Height
            ingredients,
            sword, // Result
            SlotDisplay.item(ItemStack.of(Material.CRAFTING_TABLE)) // Station icon
        );

        return List.of(display);
    }
}
```

## Registering Recipes

Once you have a recipe class (or implementation), register it with the `RecipeManager`.

```java
import net.minestom.server.MinecraftServer;

MinecraftServer.getRecipeManager().addRecipe(new MyShapedRecipe());
```

## Recipe Types (Displays)

Minestom supports all vanilla recipe types via records in `RecipeDisplay`:

- `RecipeDisplay.CraftingShaped`
- `RecipeDisplay.CraftingShapeless`
- `RecipeDisplay.Furnace`
- `RecipeDisplay.Blasting` (Smoker/Blast Furnace)
- `RecipeDisplay.Stonecutter`
- `RecipeDisplay.SmithingTransform` (Netherite upgrade)
- `RecipeDisplay.SmithingTrim` (Armor trims)

## Ingredients

Ingredients in `RecipeDisplay` are defined using `SlotDisplay`.

- `SlotDisplay.item(ItemStack)`: Matches a specific item stack.
- `SlotDisplay.tag(Tag)`: Matches items with a tag (unimplemented fully in vanilla protocol for custom tags potentially).
- `SlotDisplay.empty()`: Represents an empty slot.

## Crafting Logic

**Important:** The `RecipeManager` primarily handles sending the recipe structure to the client for the Recipe Book and client-side prediction. Minestom **does not** enforce crafting logic automatically in the `Inventory` by default.

You must handle the `CraftItemEvent` or inventory click interactions to actually verify ingredients and produce the result, OR use an extension/library that provides a crafting system implementation. The `Recipe` interface is purely for the definition and client sync.

