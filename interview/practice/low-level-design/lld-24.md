**24.** Computer Startup System — Facade pattern. Simple start() initializing CPU, Memory, Disk, Network, Display. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Computer Startup System — Facade Pattern

### Pattern: Facade

**Why Facade?** Booting a computer requires initializing 5+ subsystems in the correct order with dependency checks between them. The user just presses the power button — the facade handles all orchestration behind a single `start()` method.

### Text UML

```
┌─────────────────────────────────┐
│      ComputerFacade             │
├─────────────────────────────────┤
│ - cpu: CPU                      │
│ - memory: Memory                │
│ - disk: HardDisk                │
│ - network: NetworkInterface     │
│ - display: Display              │
│ - bios: BIOS                    │
├─────────────────────────────────┤
│ + start(): BootResult           │
│ + shutdown(): void              │
│ + restart(): void               │
│ + sleep(): void                 │
│ + getSystemStatus(): Status     │
└─────────────────────────────────┘
        │
        │ coordinates (sequential boot)
        ▼
┌─────┐ ┌──────┐ ┌────────┐ ┌─────────┐ ┌─────────┐ ┌──────┐
│ BIOS│ │ CPU  │ │ Memory │ │HardDisk │ │ Network │ │Display│
└─────┘ └──────┘ └────────┘ └─────────┘ └─────────┘ └──────┘
```

### Key Design Decisions

1. **Sequential boot with dependency checks** — Memory must pass POST before disk access; disk must mount before OS loads
2. **BootResult** — Returns success/failure with boot time and any warnings
3. **Health checks** — Each subsystem reports its status; facade aggregates into system health
4. **Graceful degradation** — Network failure doesn't prevent boot; disk failure does

### SOLID Principles Applied

- **SRP**: Each subsystem manages its own initialization; facade orchestrates the sequence
- **OCP**: Add new subsystems (GPU, USB controller) by injecting them into the facade
- **DIP**: Facade depends on subsystem abstractions

### Java Code

```java
import java.util.*;

// ===================== Subsystems =====================

public class BIOS {
    public boolean runPowerOnSelfTest() {
        System.out.println("  [BIOS] Running POST...");
        System.out.println("  [BIOS] POST passed.");
        return true;
    }

    public void loadBootLoader() {
        System.out.println("  [BIOS] Loading boot loader...");
    }
}

public class CPU {
    private boolean running = false;
    private double clockSpeed; // GHz

    public CPU(double clockSpeed) {
        this.clockSpeed = clockSpeed;
    }

    public void initialize() {
        System.out.printf("  [CPU] Initializing at %.1f GHz...%n", clockSpeed);
        running = true;
    }

    public void shutdown() {
        running = false;
        System.out.println("  [CPU] Halted.");
    }

    public boolean isRunning() { return running; }

    public String getStatus() {
        return running ? "Running at " + clockSpeed + " GHz" : "Halted";
    }
}

public class Memory {
    private final int sizeGB;
    private boolean initialized = false;

    public Memory(int sizeGB) {
        this.sizeGB = sizeGB;
    }

    public boolean initialize() {
        System.out.printf("  [Memory] Testing %d GB RAM...%n", sizeGB);
        // Simulate memory test
        initialized = true;
        System.out.printf("  [Memory] %d GB available.%n", sizeGB);
        return true;
    }

    public void clear() {
        System.out.println("  [Memory] Clearing RAM...");
        initialized = false;
    }

    public boolean isInitialized() { return initialized; }
    public String getStatus() { return initialized ? sizeGB + " GB active" : "Not initialized"; }
}

public class HardDisk {
    private final String type; // SSD, HDD
    private final int capacityGB;
    private boolean mounted = false;

    public HardDisk(String type, int capacityGB) {
        this.type = type;
        this.capacityGB = capacityGB;
    }

    public boolean mount() {
        System.out.printf("  [Disk] Mounting %s (%d GB)...%n", type, capacityGB);
        mounted = true;
        return true;
    }

    public void unmount() {
        System.out.println("  [Disk] Flushing buffers and unmounting...");
        mounted = false;
    }

    public boolean isMounted() { return mounted; }
    public String getStatus() { return mounted ? type + " " + capacityGB + "GB mounted" : "Not mounted"; }
}

public class NetworkInterface {
    private boolean connected = false;
    private String ipAddress;

    public boolean connect() {
        System.out.println("  [Network] Requesting DHCP lease...");
        this.ipAddress = "192.168.1." + (new Random().nextInt(200) + 10);
        this.connected = true;
        System.out.println("  [Network] Connected. IP: " + ipAddress);
        return true;
    }

    public void disconnect() {
        System.out.println("  [Network] Releasing IP and disconnecting...");
        connected = false;
        ipAddress = null;
    }

    public boolean isConnected() { return connected; }
    public String getStatus() { return connected ? "Connected (" + ipAddress + ")" : "Disconnected"; }
}

public class Display {
    private boolean active = false;
    private final String resolution;

    public Display(String resolution) {
        this.resolution = resolution;
    }

    public void turnOn() {
        System.out.printf("  [Display] Activating at %s...%n", resolution);
        active = true;
    }

    public void turnOff() {
        System.out.println("  [Display] Turning off...");
        active = false;
    }

    public boolean isActive() { return active; }
    public String getStatus() { return active ? resolution + " active" : "Off"; }
}

// ===================== Boot Result =====================

public class BootResult {
    private final boolean success;
    private final long bootTimeMs;
    private final List<String> warnings;

    public BootResult(boolean success, long bootTimeMs, List<String> warnings) {
        this.success = success;
        this.bootTimeMs = bootTimeMs;
        this.warnings = warnings;
    }

    public boolean isSuccess() { return success; }
    public long getBootTimeMs() { return bootTimeMs; }
    public List<String> getWarnings() { return warnings; }
}

// ===================== Facade =====================

public class ComputerFacade {
    private final BIOS bios;
    private final CPU cpu;
    private final Memory memory;
    private final HardDisk disk;
    private final NetworkInterface network;
    private final Display display;

    public ComputerFacade(BIOS bios, CPU cpu, Memory memory,
                           HardDisk disk, NetworkInterface network, Display display) {
        this.bios = bios;
        this.cpu = cpu;
        this.memory = memory;
        this.disk = disk;
        this.network = network;
        this.display = display;
    }

    public BootResult start() {
        long startTime = System.currentTimeMillis();
        List<String> warnings = new ArrayList<>();

        System.out.println("========== SYSTEM BOOT ==========");

        // Step 1: BIOS POST
        if (!bios.runPowerOnSelfTest()) {
            return new BootResult(false, 0, List.of("POST failed — hardware error"));
        }
        bios.loadBootLoader();

        // Step 2: CPU Init
        cpu.initialize();

        // Step 3: Memory Init (critical)
        if (!memory.initialize()) {
            cpu.shutdown();
            return new BootResult(false, 0, List.of("Memory initialization failed"));
        }

        // Step 4: Disk Mount (critical)
        if (!disk.mount()) {
            memory.clear();
            cpu.shutdown();
            return new BootResult(false, 0, List.of("Disk mount failed — no boot device"));
        }

        // Step 5: Display
        display.turnOn();

        // Step 6: Network (non-critical)
        try {
            if (!network.connect()) {
                warnings.add("Network: No connection available");
            }
        } catch (Exception e) {
            warnings.add("Network: Failed to connect — " + e.getMessage());
        }

        long bootTime = System.currentTimeMillis() - startTime;
        System.out.println("========== BOOT COMPLETE (" + bootTime + "ms) ==========\n");

        return new BootResult(true, bootTime, warnings);
    }

    public void shutdown() {
        System.out.println("========== SYSTEM SHUTDOWN ==========");
        network.disconnect();
        display.turnOff();
        disk.unmount();
        memory.clear();
        cpu.shutdown();
        System.out.println("========== SHUTDOWN COMPLETE ==========\n");
    }

    public void restart() {
        shutdown();
        start();
    }

    public String getSystemStatus() {
        StringBuilder sb = new StringBuilder("System Status:\n");
        sb.append("  CPU:     ").append(cpu.getStatus()).append("\n");
        sb.append("  Memory:  ").append(memory.getStatus()).append("\n");
        sb.append("  Disk:    ").append(disk.getStatus()).append("\n");
        sb.append("  Network: ").append(network.getStatus()).append("\n");
        sb.append("  Display: ").append(display.getStatus());
        return sb.toString();
    }
}
```

### Usage

```java
ComputerFacade computer = new ComputerFacade(
    new BIOS(),
    new CPU(3.5),
    new Memory(16),
    new HardDisk("SSD", 512),
    new NetworkInterface(),
    new Display("2560x1440")
);

// One button press
BootResult result = computer.start();
if (result.isSuccess()) {
    System.out.println("Boot time: " + result.getBootTimeMs() + "ms");
    if (!result.getWarnings().isEmpty()) {
        System.out.println("Warnings: " + result.getWarnings());
    }
}

System.out.println(computer.getSystemStatus());

computer.shutdown();
```

### Interview Talking Points

- **Boot order matters**: Memory before disk (can't read disk without RAM), BIOS before everything. Facade encodes this dependency chain
- **Critical vs non-critical**: Disk failure = boot fails. Network failure = boot succeeds with warning. Same pattern as order processing
- **Shutdown in reverse**: Resources released in reverse acquisition order — standard practice to prevent resource leaks
- **Spring Boot analogy**: `SpringApplication.run()` is a facade over context initialization, bean creation, server startup, health checks — one method hides enormous complexity
