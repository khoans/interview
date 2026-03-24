**48.** Design a Smart Home Automation system using mixed patterns. Composite (device groups) + Command (automation rules) + Adapter (protocols) + Strategy (access control).

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Smart Home Automation — Mixed Patterns

**Patterns:** Composite + Command + Adapter + Strategy
**Why mixed?** Composite groups devices hierarchically (room/floor), Command schedules automations, Adapter normalizes different protocols (Zigbee/WiFi/Z-Wave), Strategy switches access control modes.

### Text UML Diagram

```
Composite (Device Groups)          Command (Automation Rules)
┌────────────────┐                ┌────────────────────┐
│ <<interface>>  │                │ <<interface>>       │
│ SmartDevice    │                │ AutomationCommand   │
│────────────────│                │ +execute()          │
│ +turnOn()      │                │ +undo()             │
│ +turnOff()     │                │ +schedule()         │
│ +getStatus()   │                └──────┬─────────────┘
└───────┬────────┘                   TurnOn│Schedule│Macro
     ┌──┴───┐
   Leaf   DeviceGroup
 (Light)  (Room/Floor)

Adapter (Protocols)                Strategy (Access Control)
┌─────────────────┐               ┌────────────────────┐
│ <<interface>>   │               │ <<interface>>       │
│ SmartDevice     │               │ AccessStrategy      │
│ (unified API)   │               │ +canAccess()        │
└─────┬───────────┘               └──────┬─────────────┘
  ┌───┴────┐                        PIN │ Biometric │ Guest
ZigbeeAdapter WiFiAdapter
```

### Key Design Decisions

1. **Composite** — Treat single device and group of devices uniformly (turn off "Living Room" = turn off all devices in it)
2. **Command** — Automation rules as objects: schedulable, undoable, chainable
3. **Adapter** — Different device protocols exposed through a common SmartDevice interface
4. **Strategy** — Access control varies by mode (home/away/guest)

### Java Implementation

```java
import java.util.*;

// ============================================================
// SMART DEVICE — Composite interface
// ============================================================

interface SmartDevice {
    void turnOn();
    void turnOff();
    String getStatus();
    String getName();
}

// ============================================================
// ADAPTERS — normalize different protocols
// ============================================================

// Third-party Zigbee light (different API)
class ZigbeeLightBulb {
    private boolean powered;
    private int brightness;
    private final String zigbeeId;

    public ZigbeeLightBulb(String zigbeeId) { this.zigbeeId = zigbeeId; }
    public void setPower(boolean on) { powered = on; }
    public void setBrightness(int level) { brightness = level; }
    public boolean isPowered() { return powered; }
    public int getBrightness() { return brightness; }
}

class ZigbeeLightAdapter implements SmartDevice {
    private final ZigbeeLightBulb bulb;
    private final String name;

    public ZigbeeLightAdapter(String name, ZigbeeLightBulb bulb) {
        this.name = name;
        this.bulb = bulb;
    }

    @Override public void turnOn() { bulb.setPower(true); bulb.setBrightness(100); }
    @Override public void turnOff() { bulb.setPower(false); }
    @Override public String getStatus() { return bulb.isPowered() ? "ON (" + bulb.getBrightness() + "%)" : "OFF"; }
    @Override public String getName() { return name; }
}

// Third-party WiFi thermostat (different API)
class WiFiThermostat {
    private double setpoint;
    private boolean running;
    private final String ipAddress;

    public WiFiThermostat(String ip) { this.ipAddress = ip; }
    public void setTemperature(double temp) { setpoint = temp; running = true; }
    public void stop() { running = false; }
    public double getSetpoint() { return setpoint; }
    public boolean isRunning() { return running; }
}

class WiFiThermostatAdapter implements SmartDevice {
    private final WiFiThermostat thermostat;
    private final String name;
    private final double defaultTemp;

    public WiFiThermostatAdapter(String name, WiFiThermostat thermostat, double defaultTemp) {
        this.name = name;
        this.thermostat = thermostat;
        this.defaultTemp = defaultTemp;
    }

    @Override public void turnOn() { thermostat.setTemperature(defaultTemp); }
    @Override public void turnOff() { thermostat.stop(); }
    @Override public String getStatus() {
        return thermostat.isRunning() ? "ON (set: " + thermostat.getSetpoint() + "F)" : "OFF";
    }
    @Override public String getName() { return name; }
}

// Simple WiFi device (native interface)
class WiFiSmartPlug implements SmartDevice {
    private final String name;
    private boolean on;

    public WiFiSmartPlug(String name) { this.name = name; }
    @Override public void turnOn() { on = true; }
    @Override public void turnOff() { on = false; }
    @Override public String getStatus() { return on ? "ON" : "OFF"; }
    @Override public String getName() { return name; }
}

// ============================================================
// COMPOSITE — device groups (room, floor)
// ============================================================

class DeviceGroup implements SmartDevice {
    private final String name;
    private final List<SmartDevice> devices = new ArrayList<>();

    public DeviceGroup(String name) { this.name = name; }

    public void addDevice(SmartDevice device) { devices.add(device); }
    public void removeDevice(SmartDevice device) { devices.remove(device); }

    @Override
    public void turnOn() {
        System.out.println("Turning on group: " + name);
        devices.forEach(SmartDevice::turnOn);
    }

    @Override
    public void turnOff() {
        System.out.println("Turning off group: " + name);
        devices.forEach(SmartDevice::turnOff);
    }

    @Override
    public String getStatus() {
        StringBuilder sb = new StringBuilder(name + ":\n");
        for (SmartDevice d : devices) {
            sb.append("  ").append(d.getName()).append(": ").append(d.getStatus()).append("\n");
        }
        return sb.toString();
    }

    @Override public String getName() { return name; }
}

// ============================================================
// COMMAND — automation rules
// ============================================================

interface AutomationCommand {
    void execute();
    void undo();
    String getDescription();
}

class TurnOnCommand implements AutomationCommand {
    private final SmartDevice device;
    public TurnOnCommand(SmartDevice device) { this.device = device; }
    @Override public void execute() { device.turnOn(); }
    @Override public void undo() { device.turnOff(); }
    @Override public String getDescription() { return "Turn ON: " + device.getName(); }
}

class TurnOffCommand implements AutomationCommand {
    private final SmartDevice device;
    public TurnOffCommand(SmartDevice device) { this.device = device; }
    @Override public void execute() { device.turnOff(); }
    @Override public void undo() { device.turnOn(); }
    @Override public String getDescription() { return "Turn OFF: " + device.getName(); }
}

class MacroAutomation implements AutomationCommand {
    private final String name;
    private final List<AutomationCommand> commands;

    public MacroAutomation(String name, List<AutomationCommand> commands) {
        this.name = name;
        this.commands = new ArrayList<>(commands);
    }

    @Override
    public void execute() {
        System.out.println("Running automation: " + name);
        commands.forEach(AutomationCommand::execute);
    }

    @Override
    public void undo() {
        System.out.println("Undoing automation: " + name);
        for (int i = commands.size() - 1; i >= 0; i--) {
            commands.get(i).undo();
        }
    }

    @Override public String getDescription() { return "Macro: " + name; }
}

class ScheduledCommand {
    private final AutomationCommand command;
    private final String cronExpression;
    private boolean active;

    public ScheduledCommand(AutomationCommand command, String cronExpression) {
        this.command = command;
        this.cronExpression = cronExpression;
        this.active = true;
    }

    public void trigger() {
        if (active) {
            System.out.println("[Scheduled] " + cronExpression + " -> " + command.getDescription());
            command.execute();
        }
    }

    public void deactivate() { active = false; }
}

// ============================================================
// STRATEGY — access control
// ============================================================

interface AccessStrategy {
    boolean canAccess(String userId, SmartDevice device);
    String getModeName();
}

class HomeMode implements AccessStrategy {
    @Override
    public boolean canAccess(String userId, SmartDevice device) {
        return true; // everyone at home can control everything
    }
    @Override public String getModeName() { return "HOME"; }
}

class AwayMode implements AccessStrategy {
    private final Set<String> owners;
    public AwayMode(Set<String> owners) { this.owners = owners; }

    @Override
    public boolean canAccess(String userId, SmartDevice device) {
        return owners.contains(userId); // only owners can remote control
    }
    @Override public String getModeName() { return "AWAY"; }
}

class GuestMode implements AccessStrategy {
    private final Set<String> allowedDevices;
    public GuestMode(Set<String> allowedDeviceNames) { this.allowedDevices = allowedDeviceNames; }

    @Override
    public boolean canAccess(String userId, SmartDevice device) {
        return allowedDevices.contains(device.getName()); // guests only access specific devices
    }
    @Override public String getModeName() { return "GUEST"; }
}

// ============================================================
// SMART HOME CONTROLLER — ties everything together
// ============================================================

class SmartHomeController {
    private final Map<String, SmartDevice> devices = new LinkedHashMap<>();
    private final List<ScheduledCommand> schedules = new ArrayList<>();
    private AccessStrategy accessStrategy;

    public SmartHomeController() {
        this.accessStrategy = new HomeMode();
    }

    public void registerDevice(SmartDevice device) {
        devices.put(device.getName(), device);
    }

    public void setAccessMode(AccessStrategy strategy) {
        this.accessStrategy = strategy;
        System.out.println("Access mode: " + strategy.getModeName());
    }

    public void controlDevice(String userId, String deviceName, boolean turnOn) {
        SmartDevice device = devices.get(deviceName);
        if (device == null) {
            System.out.println("Device not found: " + deviceName);
            return;
        }
        if (!accessStrategy.canAccess(userId, device)) {
            System.out.println("Access DENIED for " + userId + " on " + deviceName);
            return;
        }
        if (turnOn) device.turnOn(); else device.turnOff();
        System.out.println(userId + " -> " + deviceName + ": " + device.getStatus());
    }

    public void addSchedule(ScheduledCommand schedule) {
        schedules.add(schedule);
    }

    public void printAllStatus() {
        System.out.println("\n--- Smart Home Status ---");
        devices.values().forEach(d -> System.out.println(d.getName() + ": " + d.getStatus()));
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class SmartHomeDemo {
    public static void main(String[] args) {
        SmartHomeController controller = new SmartHomeController();

        // Create devices via adapters
        SmartDevice light1 = new ZigbeeLightAdapter("Living Room Light", new ZigbeeLightBulb("ZB-001"));
        SmartDevice light2 = new ZigbeeLightAdapter("Bedroom Light", new ZigbeeLightBulb("ZB-002"));
        SmartDevice thermo = new WiFiThermostatAdapter("Thermostat", new WiFiThermostat("192.168.1.50"), 72);
        SmartDevice plug = new WiFiSmartPlug("Coffee Maker");

        // Create composite group
        DeviceGroup livingRoom = new DeviceGroup("Living Room");
        livingRoom.addDevice(light1);
        livingRoom.addDevice(plug);

        // Register all devices
        controller.registerDevice(light1);
        controller.registerDevice(light2);
        controller.registerDevice(thermo);
        controller.registerDevice(plug);
        controller.registerDevice(livingRoom);

        // Automation: "Good Night" macro
        AutomationCommand goodNight = new MacroAutomation("Good Night", List.of(
                new TurnOffCommand(livingRoom),  // composite: turns off group
                new TurnOffCommand(light2),
                new TurnOffCommand(thermo)
        ));

        // Home mode: everyone controls everything
        System.out.println("=== Home Mode ===");
        controller.controlDevice("alice", "Living Room Light", true);
        controller.controlDevice("guest-bob", "Coffee Maker", true);

        // Switch to Guest mode: restrict access
        System.out.println("\n=== Guest Mode ===");
        controller.setAccessMode(new GuestMode(Set.of("Living Room Light", "Coffee Maker")));
        controller.controlDevice("guest-bob", "Living Room Light", true);  // allowed
        controller.controlDevice("guest-bob", "Thermostat", true);         // denied

        // Run automation
        System.out.println("\n=== Good Night Automation ===");
        goodNight.execute();
        controller.printAllStatus();

        // Undo automation
        System.out.println("\n=== Undo Good Night ===");
        goodNight.undo();
        controller.printAllStatus();
    }
}
```

### Interview Talking Points

- **Composite shines here:** "Turn off Living Room" turns off all devices in the group. Clients don't distinguish between a single device and a group — uniform interface.
- **Adapter is essential:** Real IoT has Zigbee, Z-Wave, WiFi, BLE devices with incompatible APIs. Adapter wraps each in a common `SmartDevice` interface. This is what Samsung SmartThings and Home Assistant do.
- **Command for scheduling:** Each automation rule is a serializable command object. Schedule it with cron, chain multiple commands into macros, undo a whole routine with one call.
- **Strategy for security modes:** Access rules change entirely between Home/Away/Guest. Strategy swaps the entire permission model at runtime.
