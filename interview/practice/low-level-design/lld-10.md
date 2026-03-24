**10.** Game Character System — Prototype pattern. Clone characters with equipment, stats, skills. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Game Character System — Prototype Pattern

### Pattern: Prototype (Deep Clone)

**Why Prototype?** Creating a game character from scratch is expensive (loading stats, equipment, skills from DB/config). Cloning a pre-built template and customizing is much faster — especially for spawning NPCs or creating character presets.

### Text UML

```
┌──────────────────────────┐
│   <<interface>>          │
│   Cloneable<T>           │
├──────────────────────────┤
│ + deepClone(): T         │
└──────────┬───────────────┘
           │
┌──────────┴───────────────┐
│     GameCharacter        │
├──────────────────────────┤
│ - name: String           │
│ - stats: CharacterStats  │
│ - equipment: List<Item>  │
│ - skills: List<Skill>    │
│ - level: int             │
├──────────────────────────┤
│ + deepClone(): Character │
│ + addSkill(skill)        │
│ + equip(item)            │
└──────────────────────────┘

┌─────────────────┐  ┌──────────────────┐  ┌──────────────┐
│ CharacterStats  │  │     Item         │  │   Skill      │
├─────────────────┤  ├──────────────────┤  ├──────────────┤
│ - hp: int       │  │ - name: String   │  │ - name: String│
│ - mp: int       │  │ - type: ItemType │  │ - damage: int │
│ - attack: int   │  │ - power: int     │  │ - manaCost: int│
│ - defense: int  │  │ - durability: int│  │ - cooldown: int│
│ - speed: int    │  │ + deepClone()    │  │ + deepClone() │
└─────────────────┘  └──────────────────┘  └──────────────┘
```

### Key Design Decisions

1. **Custom `deepClone()` over `Object.clone()`** — `Cloneable` interface in Java is broken (Effective Java Item 13). Custom method is explicit and type-safe
2. **Deep copy everything** — Stats, equipment, skills are all mutable; shallow copy would share references
3. **CharacterRegistry** — Pre-built templates stored in a map; clone from registry instead of constructing from scratch
4. **Immutable skills, mutable equipment** — Skills are the same across clones; equipment varies (but still deep-copied to allow per-character durability)

### SOLID Principles Applied

- **SRP**: Each class manages its own cloning logic
- **OCP**: Add new character types/equipment without modifying existing clone logic
- **LSP**: All cloneable objects return proper deep copies

### Java Code

```java
import java.util.*;
import java.util.stream.Collectors;

// --- Custom Cloneable Interface (not java.lang.Cloneable) ---
public interface DeepCloneable<T> {
    T deepClone();
}

// --- Character Stats ---
public class CharacterStats implements DeepCloneable<CharacterStats> {
    private int hp;
    private int mp;
    private int attack;
    private int defense;
    private int speed;

    public CharacterStats(int hp, int mp, int attack, int defense, int speed) {
        this.hp = hp;
        this.mp = mp;
        this.attack = attack;
        this.defense = defense;
        this.speed = speed;
    }

    @Override
    public CharacterStats deepClone() {
        return new CharacterStats(hp, mp, attack, defense, speed);
    }

    // Getters and setters
    public int getHp() { return hp; }
    public void setHp(int hp) { this.hp = hp; }
    public int getAttack() { return attack; }
    public void setAttack(int attack) { this.attack = attack; }

    @Override
    public String toString() {
        return String.format("HP=%d MP=%d ATK=%d DEF=%d SPD=%d", hp, mp, attack, defense, speed);
    }
}

// --- Item ---
public enum ItemType { WEAPON, ARMOR, ACCESSORY }

public class Item implements DeepCloneable<Item> {
    private final String name;
    private final ItemType type;
    private final int power;
    private int durability;

    public Item(String name, ItemType type, int power, int durability) {
        this.name = name;
        this.type = type;
        this.power = power;
        this.durability = durability;
    }

    @Override
    public Item deepClone() {
        return new Item(name, type, power, durability);
    }

    public String getName() { return name; }
    public int getPower() { return power; }
    public int getDurability() { return durability; }
    public void reduceDurability(int amount) { this.durability -= amount; }

    @Override
    public String toString() { return name + "(+" + power + ")"; }
}

// --- Skill ---
public class Skill implements DeepCloneable<Skill> {
    private final String name;
    private final int damage;
    private final int manaCost;
    private final int cooldownSeconds;

    public Skill(String name, int damage, int manaCost, int cooldownSeconds) {
        this.name = name;
        this.damage = damage;
        this.manaCost = manaCost;
        this.cooldownSeconds = cooldownSeconds;
    }

    @Override
    public Skill deepClone() {
        return new Skill(name, damage, manaCost, cooldownSeconds);
    }

    public String getName() { return name; }

    @Override
    public String toString() { return name + "(dmg=" + damage + ")"; }
}

// --- Game Character (Prototype) ---
public class GameCharacter implements DeepCloneable<GameCharacter> {
    private String name;
    private int level;
    private CharacterStats stats;
    private List<Item> equipment;
    private List<Skill> skills;

    public GameCharacter(String name, int level, CharacterStats stats,
                         List<Item> equipment, List<Skill> skills) {
        this.name = name;
        this.level = level;
        this.stats = stats;
        this.equipment = new ArrayList<>(equipment);
        this.skills = new ArrayList<>(skills);
    }

    @Override
    public GameCharacter deepClone() {
        // Deep clone all mutable fields
        CharacterStats clonedStats = stats.deepClone();
        List<Item> clonedEquipment = equipment.stream()
            .map(Item::deepClone).collect(Collectors.toList());
        List<Skill> clonedSkills = skills.stream()
            .map(Skill::deepClone).collect(Collectors.toList());
        return new GameCharacter(name, level, clonedStats, clonedEquipment, clonedSkills);
    }

    public void setName(String name) { this.name = name; }
    public void addSkill(Skill skill) { this.skills.add(skill); }
    public void equip(Item item) { this.equipment.add(item); }
    public CharacterStats getStats() { return stats; }
    public String getName() { return name; }

    @Override
    public String toString() {
        return String.format("%s Lv%d | %s | Equipment: %s | Skills: %s",
            name, level, stats, equipment, skills);
    }
}

// --- Character Registry (Template Store) ---
public class CharacterRegistry {
    private final Map<String, GameCharacter> templates = new HashMap<>();

    public void registerTemplate(String key, GameCharacter character) {
        templates.put(key, character);
    }

    public GameCharacter createFromTemplate(String key, String newName) {
        GameCharacter template = templates.get(key);
        if (template == null) {
            throw new IllegalArgumentException("Unknown template: " + key);
        }
        GameCharacter clone = template.deepClone();
        clone.setName(newName);
        return clone;
    }
}
```

### Usage

```java
// Build a Warrior template
GameCharacter warriorTemplate = new GameCharacter("Warrior", 1,
    new CharacterStats(100, 20, 15, 12, 8),
    List.of(new Item("Iron Sword", ItemType.WEAPON, 10, 100),
            new Item("Steel Shield", ItemType.ARMOR, 8, 120)),
    List.of(new Skill("Slash", 20, 5, 3),
            new Skill("Shield Bash", 10, 3, 5)));

// Register template
CharacterRegistry registry = new CharacterRegistry();
registry.registerTemplate("warrior", warriorTemplate);

// Clone and customize
GameCharacter player1 = registry.createFromTemplate("warrior", "Aragorn");
player1.getStats().setAttack(20); // buff this player
player1.addSkill(new Skill("Whirlwind", 35, 10, 8));

GameCharacter player2 = registry.createFromTemplate("warrior", "Boromir");
// player2 still has original attack=15 — deep clone confirmed

System.out.println(player1); // Aragorn with buffed stats + extra skill
System.out.println(player2); // Boromir with original stats
```

### Interview Talking Points

- **Why not `Object.clone()`?** It's a broken API — requires `Cloneable` marker, returns `Object`, shallow by default, doesn't call constructors. Custom `deepClone()` is explicit and type-safe
- **When to use Prototype**: Object creation is expensive (DB lookups, file parsing) or object has many nested mutable objects
- **Production example**: In Spring, prototype scope beans (`@Scope("prototype")`) create a new instance per injection — similar concept
- **Deep vs Shallow**: If equipment was immutable (sealed records), shallow copy would suffice. Since durability changes per character, deep copy is mandatory
