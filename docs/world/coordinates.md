# Coordinates

Minestom uses distinct types for different coordinate systems to ensure type safety and clarity. All coordinate objects are **immutable**.

## Point Interface

`Point` is the common interface for all 3D coordinates. It provides basic accessors `x()`, `y()`, `z()` and common math operations.

## Coordinate Types

### Vec

`Vec` represents a 3D vector or position in the world (doubles). It is used for velocity, offsets, and generic positioning.

```java
import net.minestom.server.coordinate.Vec;

Vec velocity = new Vec(0, 10.5, 0);
Vec position = new Vec(100, 64, -100);

// Math operations
Vec target = position.add(5, 0, 5).mul(2);
double distance = position.distance(target);
```

### Pos

`Pos` represents a position that includes rotation (Yaw and Pitch). It is primarily used for entities.

```java
import net.minestom.server.coordinate.Pos;

// x, y, z, yaw, pitch
Pos spawn = new Pos(0, 42, 0, 90f, 0f);

// Conversions
Vec asVector = spawn.asVec(); // Drops rotation
Pos lookedAt = spawn.withLookAt(targetPoint);
```

### BlockVec

`BlockVec` represents integer coordinates, mainly used for block positions.

```java
import net.minestom.server.coordinate.BlockVec;

BlockVec blockPos = new BlockVec(10, 64, 10);

// Automatic flooring
BlockVec fromDouble = new BlockVec(10.5, 64.9, 10.1); // becomes 10, 64, 10
```

## Operations

All coordinate types support chaining operations.

```java
Point result = new Vec(0, 0, 0)
    .add(10, 5, 10)
    .sub(0, 5, 0)
    .div(2)
    .normalize();
```

### Helper Methods

The `CoordConversion` class provides helpers for converting between chunk, block, and loose coordinates.

```java
import net.minestom.server.coordinate.CoordConversion;

int chunkX = CoordConversion.globalToChunk(blockX);
int index = CoordConversion.chunkBlockIndex(x, y, z);
```

## Immutability & Performance

Since all coordinate objects are immutable, operations like `add()` create new instances. While this might seem expensive, the JVM (HotSpot) is very good at scalar replacement for short-lived objects.

You should never try to manually pool these objects. Trust the JVM.
