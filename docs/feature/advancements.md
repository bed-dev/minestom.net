# Advancements

Minestom allows you to create custom advancement tabs and notifications.

## Creating an Advancement

Advancements are immutable objects.

```java
import net.minestom.server.advancements.Advancement;
import net.minestom.server.advancements.FrameType;
import net.minestom.server.item.Material;
import net.kyori.adventure.text.Component;

Advancement advancement = new Advancement(
    Component.text("My Advancement"),
    Component.text("Description"),
    Material.DIAMOND,
    FrameType.TASK,
    false, // Show toast
    false, // Hidden
    false  // Announce to chat
);

// Set background only for root
advancement.setBackground("minecraft:textures/gui/advancements/backgrounds/stone.png");
```

## Creating a Tab

Advancements are organized into tabs. A player views one tab at a time.

```java
import net.minestom.server.advancements.AdvancementTab;
import net.minestom.server.advancements.AdvancementRoot;

// You need a root advancement for the tab
AdvancementRoot root = new AdvancementRoot(advancement);

AdvancementTab tab = new AdvancementTab("minestom:my_tab", root);

// Add children
Advancement child = new Advancement(
    Component.text("Child"),
    Component.text("Desc"),
    Material.STICK,
    FrameType.GOAL,
    true, false, true
);

tab.createAdvancement("minestom:child", child, root);
```

## Showing to Players

Each `AdvancementTab` stores who can see it.

```java
tab.addViewer(player);
```

## Notifications (Toast)

You can send a toast notification without adding an advancement to a tab permanently.

```java
import net.minestom.server.advancements.Notification;

Notification notification = new Notification(
    Component.text("Unlock!"),
    FrameType.CHALLENGE,
    Material.GOLD_INGOT
);

player.sendNotification(notification);
```

