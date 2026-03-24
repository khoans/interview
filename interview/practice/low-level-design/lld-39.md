**39.** Design Game Character States using the State pattern. States: Idle, Walking, Running, Jumping, Attacking, Dead.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Game Character States — State Pattern

**Pattern:** State (Behavioral)
**Why State?** Character actions depend on current state (can't attack while dead, can't jump while already jumping). State pattern makes transitions and allowed actions explicit.

**SOLID Principles Applied:**
- **SRP:** Each state handles its own behavior (animation, movement, transitions)
- **OCP:** Add new states (Swimming, Climbing, Stunned) without changing existing
- **LSP:** All states implement same interface — character treats them uniformly

### Text UML Diagram

```
State Transitions:
  Idle ──move──→ Walking ──sprint──→ Running
  Any* ──jump──→ Jumping ──land──→ previous
  Any* ──attack──→ Attacking ──done──→ previous
  Any* ──takeDamage(lethal)──→ Dead
  Running ──stop──→ Idle
  *except Dead

┌──────────────┐      ┌─────────────────────┐
│  Character   │─────→│  <<interface>>       │
│──────────────│      │  CharacterState      │
│ -state       │      │─────────────────────│
│ -health      │      │ +move()              │
│ -stamina     │      │ +stop()              │
│ -position    │      │ +jump()              │
│ +move()      │      │ +attack()            │
│ +jump()      │      │ +takeDamage(dmg)     │
│ +attack()    │      │ +update(deltaTime)   │
└──────────────┘      └────────┬────────────┘
                               │ implements
                    ┌──────────┼──────────┐
                  Idle  Walking  Running
                  Jumping  Attacking  Dead
```

### Key Design Decisions

1. **update(deltaTime)** — Game loop calls update every frame; state handles per-frame logic (movement, animation)
2. **Stamina system** — Running costs stamina; when depleted, fall back to Walking
3. **Previous state tracking** — After jump/attack ends, return to previous state (was walking? resume walking)
4. **Dead is terminal** — No transitions out of Dead (unless you add respawn)

### Java Implementation

```java
import java.util.*;

// ============================================================
// CHARACTER (CONTEXT)
// ============================================================

class GameCharacter {
    private final String name;
    private CharacterState currentState;
    private CharacterState previousState;
    private double health;
    private double maxHealth;
    private double stamina;
    private double maxStamina;
    private double x, y; // position
    private double speed;

    public GameCharacter(String name, double maxHealth, double maxStamina) {
        this.name = name;
        this.maxHealth = maxHealth;
        this.health = maxHealth;
        this.maxStamina = maxStamina;
        this.stamina = maxStamina;
        this.speed = 0;
        this.currentState = new IdleState();
        this.previousState = currentState;
    }

    // Delegate to state
    public void move(double dx, double dy) { currentState.move(this, dx, dy); }
    public void stop() { currentState.stop(this); }
    public void jump() { currentState.jump(this); }
    public void attack() { currentState.attack(this); }
    public void takeDamage(double damage) { currentState.takeDamage(this, damage); }
    public void update(double deltaTime) { currentState.update(this, deltaTime); }

    // State management
    public void setState(CharacterState newState) {
        System.out.printf("[%s] %s -> %s%n", name, currentState.getStateName(), newState.getStateName());
        this.previousState = this.currentState;
        this.currentState = newState;
        newState.enter(this);
    }

    public CharacterState getPreviousState() { return previousState; }
    public String getCurrentStateName() { return currentState.getStateName(); }

    // Health & Stamina
    public double getHealth() { return health; }
    public void reduceHealth(double amount) { health = Math.max(0, health - amount); }
    public boolean isDead() { return health <= 0; }

    public double getStamina() { return stamina; }
    public void useStamina(double amount) { stamina = Math.max(0, stamina - amount); }
    public void recoverStamina(double amount) { stamina = Math.min(maxStamina, stamina + amount); }

    // Position
    public void setSpeed(double speed) { this.speed = speed; }
    public double getSpeed() { return speed; }
    public void movePosition(double dx, double dy, double deltaTime) {
        x += dx * speed * deltaTime;
        y += dy * speed * deltaTime;
    }

    public String getStatus() {
        return String.format("%s [%s] HP:%.0f/%.0f STA:%.0f/%.0f pos(%.1f,%.1f)",
                name, currentState.getStateName(), health, maxHealth, stamina, maxStamina, x, y);
    }
}

// ============================================================
// STATE INTERFACE
// ============================================================

interface CharacterState {
    void enter(GameCharacter character);
    void move(GameCharacter character, double dx, double dy);
    void stop(GameCharacter character);
    void jump(GameCharacter character);
    void attack(GameCharacter character);
    void takeDamage(GameCharacter character, double damage);
    void update(GameCharacter character, double deltaTime);
    String getStateName();
}

// ============================================================
// CONCRETE STATES
// ============================================================

class IdleState implements CharacterState {
    @Override
    public void enter(GameCharacter c) {
        c.setSpeed(0);
    }

    @Override
    public void move(GameCharacter c, double dx, double dy) {
        c.setState(new WalkingState(dx, dy));
    }

    @Override public void stop(GameCharacter c) { /* already idle */ }

    @Override
    public void jump(GameCharacter c) {
        c.setState(new JumpingState());
    }

    @Override
    public void attack(GameCharacter c) {
        c.setState(new AttackingState());
    }

    @Override
    public void takeDamage(GameCharacter c, double damage) {
        c.reduceHealth(damage);
        System.out.printf("  Took %.0f damage! HP: %.0f%n", damage, c.getHealth());
        if (c.isDead()) c.setState(new DeadState());
    }

    @Override
    public void update(GameCharacter c, double deltaTime) {
        c.recoverStamina(5 * deltaTime); // recover stamina while idle
    }

    @Override public String getStateName() { return "IDLE"; }
}

class WalkingState implements CharacterState {
    private static final double WALK_SPEED = 3.0;
    private double dx, dy;

    public WalkingState(double dx, double dy) {
        this.dx = dx;
        this.dy = dy;
    }

    @Override
    public void enter(GameCharacter c) {
        c.setSpeed(WALK_SPEED);
    }

    @Override
    public void move(GameCharacter c, double dx, double dy) {
        this.dx = dx;
        this.dy = dy;
        // If sprinting (holding shift conceptually)
        if (c.getStamina() > 10) {
            c.setState(new RunningState(dx, dy));
        }
    }

    @Override
    public void stop(GameCharacter c) {
        c.setState(new IdleState());
    }

    @Override
    public void jump(GameCharacter c) {
        c.setState(new JumpingState());
    }

    @Override
    public void attack(GameCharacter c) {
        c.setState(new AttackingState());
    }

    @Override
    public void takeDamage(GameCharacter c, double damage) {
        c.reduceHealth(damage);
        if (c.isDead()) c.setState(new DeadState());
    }

    @Override
    public void update(GameCharacter c, double deltaTime) {
        c.movePosition(dx, dy, deltaTime);
        c.recoverStamina(2 * deltaTime);
    }

    @Override public String getStateName() { return "WALKING"; }
}

class RunningState implements CharacterState {
    private static final double RUN_SPEED = 7.0;
    private static final double STAMINA_COST_PER_SEC = 10.0;
    private double dx, dy;

    public RunningState(double dx, double dy) {
        this.dx = dx;
        this.dy = dy;
    }

    @Override
    public void enter(GameCharacter c) {
        c.setSpeed(RUN_SPEED);
    }

    @Override
    public void move(GameCharacter c, double dx, double dy) {
        this.dx = dx;
        this.dy = dy;
    }

    @Override
    public void stop(GameCharacter c) {
        c.setState(new IdleState());
    }

    @Override
    public void jump(GameCharacter c) {
        c.setState(new JumpingState());
    }

    @Override
    public void attack(GameCharacter c) {
        c.setState(new AttackingState());
    }

    @Override
    public void takeDamage(GameCharacter c, double damage) {
        c.reduceHealth(damage);
        if (c.isDead()) c.setState(new DeadState());
    }

    @Override
    public void update(GameCharacter c, double deltaTime) {
        c.movePosition(dx, dy, deltaTime);
        c.useStamina(STAMINA_COST_PER_SEC * deltaTime);
        // Out of stamina -> fall back to walking
        if (c.getStamina() <= 0) {
            System.out.println("  Out of stamina!");
            c.setState(new WalkingState(dx, dy));
        }
    }

    @Override public String getStateName() { return "RUNNING"; }
}

class JumpingState implements CharacterState {
    private double jumpTimer;
    private static final double JUMP_DURATION = 0.8;

    @Override
    public void enter(GameCharacter c) {
        jumpTimer = JUMP_DURATION;
        c.useStamina(5);
        System.out.println("  Jump!");
    }

    @Override public void move(GameCharacter c, double dx, double dy) { /* can't change direction mid-air */ }
    @Override public void stop(GameCharacter c) { /* can't stop mid-air */ }
    @Override public void jump(GameCharacter c) { /* can't double jump */ }

    @Override
    public void attack(GameCharacter c) {
        c.setState(new AttackingState()); // air attack
    }

    @Override
    public void takeDamage(GameCharacter c, double damage) {
        c.reduceHealth(damage);
        if (c.isDead()) c.setState(new DeadState());
    }

    @Override
    public void update(GameCharacter c, double deltaTime) {
        jumpTimer -= deltaTime;
        if (jumpTimer <= 0) {
            System.out.println("  Landed");
            // Return to previous state type
            c.setState(new IdleState());
        }
    }

    @Override public String getStateName() { return "JUMPING"; }
}

class AttackingState implements CharacterState {
    private double attackTimer;
    private static final double ATTACK_DURATION = 0.5;

    @Override
    public void enter(GameCharacter c) {
        attackTimer = ATTACK_DURATION;
        c.useStamina(8);
        System.out.println("  Attack!");
    }

    @Override public void move(GameCharacter c, double dx, double dy) { /* locked during attack */ }
    @Override public void stop(GameCharacter c) { /* locked */ }
    @Override public void jump(GameCharacter c) { /* locked */ }
    @Override public void attack(GameCharacter c) { /* can't chain during animation */ }

    @Override
    public void takeDamage(GameCharacter c, double damage) {
        c.reduceHealth(damage);
        if (c.isDead()) c.setState(new DeadState());
    }

    @Override
    public void update(GameCharacter c, double deltaTime) {
        attackTimer -= deltaTime;
        if (attackTimer <= 0) {
            c.setState(new IdleState());
        }
    }

    @Override public String getStateName() { return "ATTACKING"; }
}

class DeadState implements CharacterState {
    @Override
    public void enter(GameCharacter c) {
        c.setSpeed(0);
        System.out.println("  " + c.getStatus() + " has died!");
    }

    @Override public void move(GameCharacter c, double dx, double dy) { /* dead */ }
    @Override public void stop(GameCharacter c) { /* dead */ }
    @Override public void jump(GameCharacter c) { /* dead */ }
    @Override public void attack(GameCharacter c) { /* dead */ }
    @Override public void takeDamage(GameCharacter c, double damage) { /* already dead */ }
    @Override public void update(GameCharacter c, double deltaTime) { /* dead */ }
    @Override public String getStateName() { return "DEAD"; }
}

// ============================================================
// CLIENT — game loop simulation
// ============================================================

class GameDemo {
    public static void main(String[] args) {
        GameCharacter hero = new GameCharacter("Knight", 100, 50);
        System.out.println(hero.getStatus());

        // Simulate game actions
        hero.move(1, 0);                          // Idle -> Walking
        hero.update(1.0);                          // walk for 1 sec
        hero.move(1, 0);                           // Walking -> Running (sprint)
        hero.update(2.0);                          // run for 2 sec
        System.out.println(hero.getStatus());

        hero.jump();                               // Running -> Jumping
        hero.update(0.9);                          // land after 0.8s
        System.out.println(hero.getStatus());

        hero.attack();                             // Idle -> Attacking
        hero.update(0.6);                          // attack ends after 0.5s
        System.out.println(hero.getStatus());

        hero.takeDamage(90);                       // big hit
        hero.takeDamage(15);                       // lethal -> Dead
        hero.move(1, 0);                           // no effect when dead
    }
}
```

### Interview Talking Points

- **Game loop integration:** `update(deltaTime)` is how game engines work — each frame, current state processes elapsed time. Unity and Unreal use state machines for character controllers.
- **Animation locking:** AttackingState blocks movement — this prevents animation glitches from conflicting state changes. Real game engines use animation state machines (Mecanim in Unity).
- **Stamina as state driver:** RunningState auto-transitions to WalkingState when stamina depletes. This shows states can transition based on resource conditions, not just user input.
