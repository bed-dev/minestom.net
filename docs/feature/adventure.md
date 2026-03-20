# Adventure

Minestom uses [Adventure](https://docs.advntr.dev/) natively for all text and chat components. This means you have full access to the powerful component API for formatting, events, and translatability.

## Creating Components

The core of Adventure is the `Component` interface.

```java
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.format.NamedTextColor;
import net.kyori.adventure.text.format.TextDecoration;

Component text = Component.text("Hello ", NamedTextColor.GRAY)
    .append(Component.text("World!", NamedTextColor.RED, TextDecoration.BOLD));
```

## Sending Messages

You can send components to any `Audience` (Player, Console, or custom groups).

```java
player.sendMessage(text);
// Console support varies based on implementation, 
// but most Minestom servers support basic logging.
```

## Action Bar

Sending an action bar message is just as easy.

```java
player.sendActionBar(Component.text("Loading...", NamedTextColor.YELLOW));
```

## Titles

Titles require a `Title` object which bundles the main title, subtitle, and times.

```java
import net.kyori.adventure.title.Title;
import java.time.Duration;

// Setup fade-in, stay, and fade-out times
Title.Times times = Title.Times.times(Duration.ofMillis(500), Duration.ofMillis(3000), Duration.ofMillis(1000));

// Create title object
Title title = Title.title(
    Component.text("Main Title", NamedTextColor.GOLD),
    Component.text("Subtitle", NamedTextColor.YELLOW),
    times
);

// Show to player
player.showTitle(title);
```

## Boss Bar

Boss bars are also fully supported via Adventure.

```java
import net.kyori.adventure.bossbar.BossBar;
import java.util.Set;

BossBar bossBar = BossBar.bossBar(
    Component.text("Boss Health"),
    1.0f,
    BossBar.Color.RED,
    BossBar.Overlay.PROGRESS
);

// Show the bar to player
player.showBossBar(bossBar);

// Update progress (0.0 to 1.0)
bossBar.progress(0.5f);

// Add flags (e.g., darken sky)
bossBar.addFlag(BossBar.Flag.DARKEN_SCREEN);

// To remove, hide it
player.hideBossBar(bossBar);
```

## Interactive Chat (Click & Hover)

You can add click and hover events to components.

```java
import net.kyori.adventure.text.event.ClickEvent;
import net.kyori.adventure.text.event.HoverEvent;

Component interactive = Component.text("Click to execute command!")
    .clickEvent(ClickEvent.runCommand("/say Hello"))
    .hoverEvent(HoverEvent.showText(Component.text("This will run /say Hello")));
```

## MiniMessage

Minestom includes MiniMessage, a string-based format for components. This is great for config files or user input.

```java
import net.kyori.adventure.text.minimessage.MiniMessage;

var mm = MiniMessage.miniMessage();
Component parsed = mm.deserialize("<red>Hello <bold>World</bold>!");
```

## Translation

Minestom supports client-side translation keys.

```java
Component translatable = Component.translatable("block.minecraft.stone");
```

For server-side translations, you can register a `GlobalTranslator`.

```java
import net.kyori.adventure.translation.GlobalTranslator;
import net.kyori.adventure.translation.TranslationRegistry;
import net.kyori.adventure.key.Key;
import java.util.Locale;

TranslationRegistry registry = TranslationRegistry.create(Key.key("minestom", "main"));
// Register bundles here...
// registry.registerAll(Locale.US, resourceBundle);

GlobalTranslator.translator().addSource(registry);
```

