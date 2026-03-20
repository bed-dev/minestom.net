# Map Rendering

## Introduction

Minecraft maps display a 128x128 image. While originally intended for aerial views, Minestom allows you to render arbitrary pixel data onto maps, enabling custom GUIs, images, or dynamic displays.

## Map Colors

Map pixels are not standard RGB. They are indices pointing to a specific palette of colors defined by Minecraft.

The `MapColors` class provides utilities to find the closest map color index for a given RGB value.

```java
import net.minestom.server.map.MapColors;

// Get the closest map color index for a specific RGB
byte colorIndex = MapColors.closestColor(255, 0, 0); // Red
```

## Framebuffers

Writing raw byte arrays for map data is tedious. Minestom provides `Framebuffer` to handle drawing operations.

### Creating a Framebuffer

```java
import net.minestom.server.map.Framebuffer;

Framebuffer framebuffer = new Framebuffer(128, 128);
```

### Drawing

You can set individual pixels or bulk data.

```java
// Set a single pixel at (x, y)
framebuffer.set(10, 10, MapColors.DIAMOND_BLUE);

// Draw a rectangle (manual loop or utility)
for (int x = 0; x < 20; x++) {
    for (int y = 0; y < 20; y++) {
        framebuffer.set(x, y, MapColors.EMERALD_GREEN);
    }
}
```

### Sending to Player

Once you have drawn your content, you need to send it to the player. This is done via `MapDataPacket`.

First, you need a map item with a specific ID.

```java
ItemStack mapItem = ItemStack.of(Material.FILLED_MAP)
    .withMeta(MapMeta.class, meta -> meta.mapId(100));

player.getInventory().setItemInMainHand(mapItem);
```

Then, send the packet updating map ID 100.

```java
import net.minestom.server.network.packet.server.play.MapDataPacket;

MapDataPacket packet = framebuffer.preparePacket(100);

player.sendPacket(packet);
```

## Large Framebuffers

`LargeFramebuffer` allows you to create images larger than 128x128. They logicaly represent a grid of standard maps.

```java
import net.minestom.server.map.LargeFramebuffer;

// 2x2 maps (256x256 pixels)
LargeFramebuffer largeFramebuffer = new LargeFramebuffer(256, 256);

// Draw large image...
// ...

// Get a specific 128x128 view (e.g., top-left map)
Framebuffer view = largeFramebuffer.createSubView(0, 0);
```

## Images

You can load standard images (PNG, JPEG) and render them onto a framebuffer.

```java
import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;

BufferedImage image = ImageIO.read(new File("image.png"));

// Resize needs to be handled manually or via Java AWT/Graphics2D before writing to framebuffer if needed
for (int x = 0; x < 128; x++) {
    for (int y = 0; y < 128; y++) {
        int rgb = image.getRGB(x, y);
        byte color = MapColors.closestColor(rgb);
        framebuffer.set(x, y, color);
    }
}
```
