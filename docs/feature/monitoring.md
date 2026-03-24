# Monitoring and Benchmarking

Minestom comes with built-in tools for monitoring server performance, including tick duration tracking and JFR events.

## Benchmark Manager

The `BenchmarkManager` allows you to monitor the server's tick loop performance.

```java
import net.minestom.server.monitoring.BenchmarkManager;
import net.minestom.server.MinecraftServer;
import net.minestom.server.monitoring.TickMonitor;

BenchmarkManager benchmark = MinecraftServer.getBenchmarkManager();
benchmark.enable(Duration.ofSeconds(10)); // Collect data over 10s windows

benchmark.addThreadMonitor("tick-monitor", (threadResult) -> {
    double cpu = threadResult.getCpuPercentage();
    double memory = threadResult.getUserPercentage();
    // Log or broadcast stats
});
```

## Tick Monitoring

You can hook into the tick process to measure exact tick times.

```java
MinecraftServer.getBenchmarkManager().setTickMonitor((tickMonitor) -> {
    double tickTime = tickMonitor.getTickTime();
    double acquisitionTime = tickMonitor.getAcquisitionTime();
    
    if (tickTime > 50) {
        System.out.println("Lag spike! Tick took " + tickTime + "ms");
    }
});
```

## Java Flight Recorder (JFR) Events

Minestom emits custom JFR events (`EventsJFR`), allowing you to profile specific parts of the server using standard Java tools (like JDK Mission Control).

### Events Covered:
- `TickEvent`: Duration of a full server tick.
- `PacketReceiveEvent`: When a packet is received effectively.
- `PacketSendEvent`: When a packet is meant to be sent.
- `WorldGenerationEvent`: When a chunk is generated.

These events are automatically emitted if JFR is running. You can start JFR using JVM arguments:
`-XX:StartFlightRecording=filename=recording.jfr,duration=60s`

