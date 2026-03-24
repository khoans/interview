**22.** Home Theater System — Facade pattern. Simple watchMovie() coordinating 5+ subsystems. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Home Theater System — Facade Pattern

### Pattern: Facade

**Why Facade?** Watching a movie requires coordinating 6+ subsystems (projector, sound, lights, player, screen, popcorn). Without a facade, the client must call 15+ methods in the correct order. Facade provides one `watchMovie()` method that orchestrates everything.

### Text UML

```
┌─────────────────────────────────┐
│     HomeTheaterFacade           │
├─────────────────────────────────┤
│ - projector: Projector          │
│ - soundSystem: SoundSystem      │
│ - streamPlayer: StreamPlayer    │
│ - lights: TheaterLights         │
│ - screen: Screen                │
│ - popcornMaker: PopcornMaker    │
├─────────────────────────────────┤
│ + watchMovie(title): void       │
│ + endMovie(): void              │
│ + listenToMusic(playlist): void │
│ + adjustVolume(level): void     │
└─────────────────────────────────┘
        │
        │ coordinates
        ▼
┌──────────┐ ┌──────────┐ ┌──────────────┐
│Projector │ │SoundSystem│ │StreamPlayer  │
├──────────┤ ├──────────┤ ├──────────────┤
│ on/off   │ │ on/off   │ │ play/stop    │
│ setInput │ │ setVolume│ │ selectContent│
│ widescreen│ │ setSurround│ │ pause/resume│
└──────────┘ └──────────┘ └──────────────┘

┌──────────────┐ ┌──────────┐ ┌──────────────┐
│TheaterLights │ │ Screen   │ │PopcornMaker  │
├──────────────┤ ├──────────┤ ├──────────────┤
│ dim(level)   │ │ down()   │ │ on/off       │
│ on/off       │ │ up()     │ │ pop()        │
└──────────────┘ └──────────┘ └──────────────┘
```

### Key Design Decisions

1. **Simplified interface** — `watchMovie()` replaces 10+ sequential calls
2. **Subsystems still accessible** — Facade doesn't hide subsystems; power users can still call them directly
3. **Correct ordering** — Facade ensures subsystems are initialized in proper sequence (screen down before projector on)
4. **Shutdown in reverse** — `endMovie()` stops in reverse order for clean shutdown

### SOLID Principles Applied

- **SRP**: Facade only orchestrates; each subsystem manages its own behavior
- **DIP**: Client depends on Facade, not on 6 concrete subsystems
- **Law of Demeter**: Client talks to one object (Facade) instead of reaching into 6 subsystems

### Java Code

```java
// ===================== Subsystem Classes =====================

public class Projector {
    public void on() { System.out.println("  Projector: ON"); }
    public void off() { System.out.println("  Projector: OFF"); }
    public void setInput(String source) { System.out.println("  Projector: input set to " + source); }
    public void wideScreenMode() { System.out.println("  Projector: widescreen mode (16:9)"); }
}

public class SoundSystem {
    public void on() { System.out.println("  Sound System: ON"); }
    public void off() { System.out.println("  Sound System: OFF"); }
    public void setVolume(int level) { System.out.println("  Sound System: volume set to " + level); }
    public void setSurroundSound() { System.out.println("  Sound System: surround sound enabled"); }
    public void setStereoMode() { System.out.println("  Sound System: stereo mode"); }
}

public class StreamPlayer {
    public void on() { System.out.println("  Stream Player: ON"); }
    public void off() { System.out.println("  Stream Player: OFF"); }
    public void play(String content) { System.out.println("  Stream Player: playing '" + content + "'"); }
    public void stop() { System.out.println("  Stream Player: stopped"); }
    public void pause() { System.out.println("  Stream Player: paused"); }
    public void resume() { System.out.println("  Stream Player: resumed"); }
}

public class TheaterLights {
    public void on() { System.out.println("  Lights: ON (full brightness)"); }
    public void off() { System.out.println("  Lights: OFF"); }
    public void dim(int level) { System.out.println("  Lights: dimmed to " + level + "%"); }
}

public class Screen {
    public void down() { System.out.println("  Screen: lowering"); }
    public void up() { System.out.println("  Screen: raising"); }
}

public class PopcornMaker {
    public void on() { System.out.println("  Popcorn Maker: ON"); }
    public void off() { System.out.println("  Popcorn Maker: OFF"); }
    public void pop() { System.out.println("  Popcorn Maker: popping!"); }
}

// ===================== Facade =====================

public class HomeTheaterFacade {
    private final Projector projector;
    private final SoundSystem soundSystem;
    private final StreamPlayer streamPlayer;
    private final TheaterLights lights;
    private final Screen screen;
    private final PopcornMaker popcornMaker;

    public HomeTheaterFacade(Projector projector, SoundSystem soundSystem,
                              StreamPlayer streamPlayer, TheaterLights lights,
                              Screen screen, PopcornMaker popcornMaker) {
        this.projector = projector;
        this.soundSystem = soundSystem;
        this.streamPlayer = streamPlayer;
        this.lights = lights;
        this.screen = screen;
        this.popcornMaker = popcornMaker;
    }

    // One method replaces 10+ calls in correct order
    public void watchMovie(String movie) {
        System.out.println("=== Preparing to watch: " + movie + " ===");

        popcornMaker.on();
        popcornMaker.pop();

        lights.dim(10);

        screen.down();

        projector.on();
        projector.setInput("StreamPlayer");
        projector.wideScreenMode();

        soundSystem.on();
        soundSystem.setSurroundSound();
        soundSystem.setVolume(7);

        streamPlayer.on();
        streamPlayer.play(movie);

        System.out.println("=== Movie started! Enjoy! ===\n");
    }

    public void endMovie() {
        System.out.println("=== Shutting down movie... ===");

        streamPlayer.stop();
        streamPlayer.off();

        soundSystem.off();

        projector.off();

        screen.up();

        lights.on();

        popcornMaker.off();

        System.out.println("=== Movie night over ===\n");
    }

    // Another simplified operation
    public void listenToMusic(String playlist) {
        System.out.println("=== Music mode ===");
        lights.dim(40);
        soundSystem.on();
        soundSystem.setStereoMode();
        soundSystem.setVolume(5);
        streamPlayer.on();
        streamPlayer.play(playlist);
    }

    public void adjustVolume(int level) {
        soundSystem.setVolume(level);
    }
}
```

### Usage

```java
// Create subsystems
Projector projector = new Projector();
SoundSystem sound = new SoundSystem();
StreamPlayer player = new StreamPlayer();
TheaterLights lights = new TheaterLights();
Screen screen = new Screen();
PopcornMaker popcorn = new PopcornMaker();

// Create facade
HomeTheaterFacade theater = new HomeTheaterFacade(
    projector, sound, player, lights, screen, popcorn);

// Simple API — one call does everything
theater.watchMovie("The Matrix");
// All 10+ subsystem calls execute in correct order

theater.endMovie();
// Clean shutdown in reverse order
```

### Interview Talking Points

- **Head First Design Patterns classic** — this is THE canonical Facade example
- **Spring Boot is a Facade**: Auto-configuration hides the complexity of configuring DataSource, EntityManager, TransactionManager, etc. One `@SpringBootApplication` replaces 50+ lines of XML config
- **Facade vs Adapter**: Adapter changes an interface to match what client expects. Facade simplifies a complex set of interfaces into one easy-to-use interface
- **Not hiding subsystems**: Facade provides convenience but doesn't prevent direct subsystem access when needed — unlike encapsulation
