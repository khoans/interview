**34.** Design a Remote Control System using the Command pattern. Support programmable buttons, undo, and macro commands.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Remote Control System — Command Pattern

**Pattern:** Command (Behavioral)
**Why Command?** Encapsulate each device action as an object — enables programmable buttons, undo stack, and macro recording without the remote knowing device details.

**SOLID Principles Applied:**
- **SRP:** Each command encapsulates one action; Remote handles button mapping; Devices handle hardware
- **OCP:** Add new devices/commands without changing Remote
- **DIP:** Remote depends on Command interface, not device classes

### Text UML Diagram

```
┌──────────────┐     ┌───────────────────┐     ┌─────────────┐
│ RemoteControl │────→│  <<interface>>     │────→│   Device    │
│──────────────│     │    Command         │     │  (Receiver) │
│ -slots[]     │     │───────────────────│     └─────────────┘
│ -undoStack   │     │ +execute()         │
│ +setCommand()│     │ +undo()            │
│ +pressButton()│    │ +getName()         │
│ +pressUndo() │     └────────┬──────────┘
│ +pressMacro()│              │ implements
└──────────────┘    ┌─────────┼──────────┐
                    │         │          │
              LightOnCmd  FanSpeedCmd  MacroCommand
              LightOffCmd DoorLockCmd  (composite)
```

### Key Design Decisions

1. **Slot-based buttons** — Remote has numbered slots, each holds an on/off command pair
2. **Undo stack** — Each execute pushes to stack; undo pops and calls `undo()`
3. **MacroCommand** — Composite of multiple commands executed in sequence
4. **NoCommand null object** — Avoids null checks for empty slots

### Java Implementation

```java
import java.util.*;

// ============================================================
// COMMAND INTERFACE
// ============================================================

interface Command {
    void execute();
    void undo();
    String getName();
}

// ============================================================
// NULL OBJECT — avoids null checks
// ============================================================

class NoCommand implements Command {
    @Override public void execute() { /* do nothing */ }
    @Override public void undo() { /* do nothing */ }
    @Override public String getName() { return "NoCommand"; }
}

// ============================================================
// RECEIVERS — actual devices
// ============================================================

class Light {
    private final String location;
    private int brightness; // 0-100

    public Light(String location) {
        this.location = location;
        this.brightness = 0;
    }

    public void on() { brightness = 100; System.out.println(location + " Light ON"); }
    public void off() { brightness = 0; System.out.println(location + " Light OFF"); }
    public void dim(int level) { brightness = level; System.out.println(location + " Light dimmed to " + level + "%"); }
    public int getBrightness() { return brightness; }
    public String getLocation() { return location; }
}

class CeilingFan {
    public enum Speed { OFF, LOW, MEDIUM, HIGH }
    private final String location;
    private Speed speed;

    public CeilingFan(String location) {
        this.location = location;
        this.speed = Speed.OFF;
    }

    public void setSpeed(Speed speed) {
        this.speed = speed;
        System.out.println(location + " Fan set to " + speed);
    }

    public Speed getSpeed() { return speed; }
}

class SecuritySystem {
    private boolean armed;

    public void arm() { armed = true; System.out.println("Security system ARMED"); }
    public void disarm() { armed = false; System.out.println("Security system DISARMED"); }
    public boolean isArmed() { return armed; }
}

// ============================================================
// CONCRETE COMMANDS
// ============================================================

class LightOnCommand implements Command {
    private final Light light;
    private int previousBrightness;

    public LightOnCommand(Light light) { this.light = light; }

    @Override
    public void execute() {
        previousBrightness = light.getBrightness();
        light.on();
    }

    @Override
    public void undo() {
        light.dim(previousBrightness);
    }

    @Override
    public String getName() { return light.getLocation() + " Light On"; }
}

class LightOffCommand implements Command {
    private final Light light;
    private int previousBrightness;

    public LightOffCommand(Light light) { this.light = light; }

    @Override
    public void execute() {
        previousBrightness = light.getBrightness();
        light.off();
    }

    @Override
    public void undo() {
        light.dim(previousBrightness);
    }

    @Override
    public String getName() { return light.getLocation() + " Light Off"; }
}

class CeilingFanSpeedCommand implements Command {
    private final CeilingFan fan;
    private final CeilingFan.Speed targetSpeed;
    private CeilingFan.Speed previousSpeed;

    public CeilingFanSpeedCommand(CeilingFan fan, CeilingFan.Speed targetSpeed) {
        this.fan = fan;
        this.targetSpeed = targetSpeed;
    }

    @Override
    public void execute() {
        previousSpeed = fan.getSpeed();
        fan.setSpeed(targetSpeed);
    }

    @Override
    public void undo() {
        fan.setSpeed(previousSpeed);
    }

    @Override
    public String getName() { return "Fan " + targetSpeed; }
}

class SecurityArmCommand implements Command {
    private final SecuritySystem security;
    private boolean wasArmed;

    public SecurityArmCommand(SecuritySystem security) { this.security = security; }

    @Override
    public void execute() {
        wasArmed = security.isArmed();
        security.arm();
    }

    @Override
    public void undo() {
        if (!wasArmed) security.disarm();
    }

    @Override
    public String getName() { return "Arm Security"; }
}

// ============================================================
// MACRO COMMAND — composite of multiple commands
// ============================================================

class MacroCommand implements Command {
    private final String name;
    private final List<Command> commands;

    public MacroCommand(String name, List<Command> commands) {
        this.name = name;
        this.commands = new ArrayList<>(commands);
    }

    @Override
    public void execute() {
        System.out.println("--- Executing Macro: " + name + " ---");
        for (Command cmd : commands) {
            cmd.execute();
        }
    }

    @Override
    public void undo() {
        System.out.println("--- Undoing Macro: " + name + " ---");
        // Undo in reverse order
        for (int i = commands.size() - 1; i >= 0; i--) {
            commands.get(i).undo();
        }
    }

    @Override
    public String getName() { return "Macro: " + name; }
}

// ============================================================
// INVOKER — Remote Control
// ============================================================

class RemoteControl {
    private final Command[] onCommands;
    private final Command[] offCommands;
    private final Deque<Command> undoStack = new ArrayDeque<>();
    private final int slotCount;

    public RemoteControl(int slotCount) {
        this.slotCount = slotCount;
        this.onCommands = new Command[slotCount];
        this.offCommands = new Command[slotCount];

        // Initialize with NoCommand (null object pattern)
        Command noCommand = new NoCommand();
        Arrays.fill(onCommands, noCommand);
        Arrays.fill(offCommands, noCommand);
    }

    public void setCommand(int slot, Command onCommand, Command offCommand) {
        if (slot < 0 || slot >= slotCount) {
            throw new IllegalArgumentException("Invalid slot: " + slot);
        }
        onCommands[slot] = onCommand;
        offCommands[slot] = offCommand;
    }

    public void pressOnButton(int slot) {
        System.out.println("[Button " + slot + " ON]");
        onCommands[slot].execute();
        undoStack.push(onCommands[slot]);
    }

    public void pressOffButton(int slot) {
        System.out.println("[Button " + slot + " OFF]");
        offCommands[slot].execute();
        undoStack.push(offCommands[slot]);
    }

    public void pressUndo() {
        if (undoStack.isEmpty()) {
            System.out.println("[Undo] Nothing to undo");
            return;
        }
        Command lastCommand = undoStack.pop();
        System.out.println("[Undo] Undoing: " + lastCommand.getName());
        lastCommand.undo();
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("\n--- Remote Control ---\n");
        for (int i = 0; i < slotCount; i++) {
            sb.append(String.format("[slot %d] ON: %-25s OFF: %s%n",
                    i, onCommands[i].getName(), offCommands[i].getName()));
        }
        return sb.toString();
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class RemoteControlDemo {
    public static void main(String[] args) {
        RemoteControl remote = new RemoteControl(4);

        // Devices
        Light livingRoom = new Light("Living Room");
        Light bedroom = new Light("Bedroom");
        CeilingFan fan = new CeilingFan("Living Room");
        SecuritySystem security = new SecuritySystem();

        // Program buttons
        remote.setCommand(0, new LightOnCommand(livingRoom), new LightOffCommand(livingRoom));
        remote.setCommand(1, new LightOnCommand(bedroom), new LightOffCommand(bedroom));
        remote.setCommand(2,
                new CeilingFanSpeedCommand(fan, CeilingFan.Speed.HIGH),
                new CeilingFanSpeedCommand(fan, CeilingFan.Speed.OFF));

        // Macro: "Good Night" = all lights off + arm security
        MacroCommand goodNight = new MacroCommand("Good Night", List.of(
                new LightOffCommand(livingRoom),
                new LightOffCommand(bedroom),
                new CeilingFanSpeedCommand(fan, CeilingFan.Speed.OFF),
                new SecurityArmCommand(security)
        ));
        remote.setCommand(3, goodNight, new NoCommand());

        System.out.println(remote);

        // Use remote
        remote.pressOnButton(0);  // Living room light on
        remote.pressOnButton(2);  // Fan high
        remote.pressUndo();       // Fan back to off
        remote.pressOnButton(3);  // Good Night macro

        System.out.println();
        remote.pressUndo();       // Undo entire Good Night macro
    }
}
```

### Interview Talking Points

- **Why Command over direct method calls?** Commands decouple invoker from receiver, enable undo/redo, logging, queueing, and macro recording — none of which are possible with direct calls.
- **Undo with state:** Each command stores previous state before execute. For complex undo (e.g., text editors), use Memento pattern alongside Command.
- **Real-world:** Java's `Runnable` is a command. Spring Batch jobs, Celery tasks, and transaction logs all use this pattern. Thread pools accept Command objects (Runnables) for deferred execution.
