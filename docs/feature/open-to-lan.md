# Open to LAN

Minestom allows you to broadcast your server to the local network, making it appear in the "Lan Worlds" section of the Minecraft client server list.

## Usage

This feature uses `OpenToLAN` utility. Note that this **doesn't** actually open your server port or configure your firewall; it simply broadcasts the "Open to LAN" packet to the local network.

```java
// Open with default configuration
OpenToLAN.open();

// Open with custom configuration
OpenToLAN.open(new OpenToLANConfig()
    .eventCallDelay(Duration.of(30, TimeUnit.SECOND)) // Delay between ping events
    .port(25565) // The port to advertise (default is server port)
);
```

## Closing

You can stop the broadcasting at any time.

```java
OpenToLAN.close();
```


