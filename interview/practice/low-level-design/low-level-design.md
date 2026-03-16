# 50 Low-Level Design Questions (Java Class Design)

## Category 1: Creational Patterns (Questions 1-12)

### Singleton Pattern Scenarios

**1. Configuration Manager**
You're building a system where configuration settings (database URL, API keys, feature flags) need to be loaded once at startup and accessed globally throughout the application. Multiple components need to read these settings, but they should never load different versions of the configuration. Design the classes.

**2. Logger Service**
Your application needs a logging system that writes to files. Multiple threads across the application will log messages simultaneously. You need to ensure all logs go through a single instance to prevent file access conflicts and maintain log order. Design the classes.

**3. Cache Manager**
You need to design an in-memory cache for frequently accessed data (like user sessions). The cache should be accessible from anywhere in the application, but there should only be one cache instance to ensure data consistency. Design the classes.

---

### Factory Pattern Scenarios

**4. Payment Processing System**
You're building an e-commerce platform that supports multiple payment methods (Credit Card, PayPal, Bitcoin, Bank Transfer). Each payment method has different validation rules, processing logic, and fee calculations. New payment methods will be added frequently. The client code should not depend on concrete payment classes. Design the classes.

**5. Document Export System**
Your application needs to export data in multiple formats (PDF, Excel, CSV, JSON). Each format has different creation logic, dependencies, and configuration. Users should be able to request exports without knowing the details of how each format is created. Design the classes.

**6. Notification System**
You need to send notifications through multiple channels (Email, SMS, Push Notification, Slack). Each channel requires different setup, authentication, and message formatting. The system should easily support adding new notification channels. Design the classes.

---

### Builder Pattern Scenarios

**7. Complex SQL Query Builder**
You need to create a class that builds complex SQL queries dynamically. A query can have SELECT, FROM, WHERE, JOIN, GROUP BY, ORDER BY, HAVING clauses - all optional. Some queries are simple, others have 10+ conditions. The constructor approach is becoming unwieldy. Design the classes.

**8. Report Generation System**
Your system generates reports with various configurations: date range, filters, columns, sorting, grouping, formatting, export format, email recipients, scheduling options. Not all options are required, and combinations vary widely. Design the classes.

**9. HTTP Request Builder**
You're creating an HTTP client library. A request can have: URL, method, headers, query parameters, body, authentication, timeout, retry policy, interceptors. Users should be able to construct requests fluently without dealing with telescoping constructors. Design the classes.

---

### Prototype Pattern Scenarios

**10. Game Character System**
You're building a game where players can clone their characters (with all equipment, stats, skills) to create alternate versions. Creating a character from scratch is expensive (loads assets, initializes skills, calculates stats). Design the classes.

**11. Document Template System**
Your application has document templates (contracts, invoices, reports) that users can duplicate and customize. Each template has complex structure with nested sections, styles, and default content. Copying should be deep (not just references). Design the classes.

**12. Database Connection Pool**
You need to manage database connections. Creating new connections is expensive. Instead, you pre-create connection objects and clone them when needed, then reset state. Design the classes.

---

## Category 2: Structural Patterns (Questions 13-25)

### Adapter Pattern Scenarios

**13. Payment Gateway Integration**
Your e-commerce system has a standard `PaymentProcessor` interface. You need to integrate with 3rd-party payment providers (Stripe, PayPal, Square) that each have their own incompatible APIs. Your system shouldn't change when adding new providers. Design the classes.

**14. Legacy System Integration**
Your new application needs to work with a legacy inventory system that has methods like `getStock()`, `updateStock()`, `checkAvailability()`. Your new system expects `InventoryService` with methods `fetchInventory()`, `modifyInventory()`, `isAvailable()`. Design the classes.

**15. Multi-Cloud Storage**
Your application should work with multiple cloud storage providers (AWS S3, Google Cloud Storage, Azure Blob). Each provider has different SDKs and method signatures. Your application should have a unified interface for upload, download, delete operations. Design the classes.

---

### Decorator Pattern Scenarios

**16. Coffee Shop Ordering System**
You're building a POS system for a coffee shop. Customers can order base drinks (Coffee, Tea, Latte) with various add-ons (Milk, Sugar, Whip Cream, Extra Shot, Flavored Syrup). Each add-on has a price and modifies the description. Combinations are unlimited. Design the classes.

**17. Data Stream Processing**
You need to process data streams with optional transformations: compression, encryption, buffering, logging, checksum validation. Each transformation wraps the stream. Users should be able to combine transformations dynamically. Design the classes.

**18. Employee Salary Calculator**
Your HR system calculates employee salaries. Base salary is modified by various factors: bonus, tax deduction, insurance, overtime, performance multiplier, allowance. Each factor can be applied independently or in combination. Design the classes.

---

### Composite Pattern Scenarios

**19. File System Explorer**
You're building a file explorer application. Users can work with both individual files and folders (which contain files and other folders). Operations like getSize(), delete(), move() should work uniformly on both files and folders. Design the classes.

**20. Organization Hierarchy**
Your HR system manages organizational structure. The organization has employees and departments. Departments can contain employees and sub-departments. You need to calculate total salary, headcount, and print organization tree for any node. Design the classes.

**21. UI Component System**
You're building a UI framework. Components can be simple (Button, TextField, Label) or containers (Panel, Window, Tab) that hold other components. Operations like render(), enable(), disable() should work on both simple and composite components. Design the classes.

---

### Facade Pattern Scenarios

**22. Home Theater System**
You're creating an API for a home theater system with multiple subsystems: Amplifier, Projector, StreamingPlayer, Lights, PopcornMaker. Watching a movie requires coordinating 10+ steps across subsystems. Users want a simple "watchMovie()" interface. Design the classes.

**23. E-Commerce Order Processing**
Your order processing involves: InventoryService, PaymentService, ShippingService, NotificationService, LoyaltyService, AnalyticsService. Placing an order requires coordinating all these services in the right sequence with error handling. Design the classes.

**24. Computer Startup System**
A computer has multiple components: CPU, Memory, Disk, Network, Display. Starting up requires initializing each in sequence with specific checks. Users want a simple "start()" operation. Design the classes.

---

### Proxy Pattern Scenarios

**25. Image Loading System**
Your application displays high-resolution images. Loading all images at startup is slow. Images should only be loaded when actually displayed. After loading, they should be cached. Design the classes.

**26. Database Connection Protection**
Your application has a RealDatabase that's expensive to connect to. You want to: (1) delay connection until first query, (2) cache query results, (3) log all queries for auditing. Design the classes.

**27. Access Control System**
Your system has sensitive documents that require access control. Different users have different permission levels. The system should check permissions before allowing document access without modifying the document class. Design the classes.

---

## Category 3: Behavioral Patterns (Questions 28-45)

### Strategy Pattern Scenarios

**28. Sorting System**
Your application needs to sort data using different algorithms (Bubble Sort, Quick Sort, Merge Sort, Insertion Sort). The best algorithm depends on data size, data distribution, and memory constraints. Users should be able to switch algorithms at runtime. Design the classes.

**29. Shipping Cost Calculator**
Your e-commerce platform calculates shipping costs using different carriers (FedEx, UPS, USPS, DHL). Each carrier has different pricing rules (weight-based, distance-based, flat rate). Carrier selection can change at checkout. Design the classes.

**30. Text Formatting System**
Your text editor supports multiple formatting styles (Plain Text, HTML, Markdown, JSON). Each format has different rules for indentation, line breaks, and syntax highlighting. Users should switch formats dynamically. Design the classes.

---

### Observer Pattern Scenarios

**31. Stock Price Alert System**
Users can subscribe to stock price updates. When a stock price changes, all subscribed users should be notified. Users can subscribe/unsubscribe at any time. Different users want different notification methods (email, SMS, push). Design the classes.

**32. Weather Station**
A weather station collects temperature, humidity, and pressure data. Multiple displays (Current Conditions, Statistics, Forecast) need to update whenever measurements change. New display types may be added later. Design the classes.

**33. Event Management System**
Your application has an event bus. Components can publish events and subscribe to events they're interested in. When an event is published, all interested subscribers should be notified. Design the classes.

---

### Command Pattern Scenarios

**34. Remote Control System**
You're building a universal remote control. Each button can be programmed with different commands (Turn On TV, Change Channel, Adjust Volume). The remote should support undo operations and macro commands (multiple commands in sequence). Design the classes.

**35. Text Editor Undo/Redo**
Your text editor supports operations: Insert Text, Delete Text, Format Text, Cut, Paste. Each operation should be undoable and redoable. Users should be able to perform multiple undos in sequence. Design the classes.

**36. Order Processing Queue**
Your restaurant ordering system receives orders that need to be processed in sequence. Orders can be queued, executed, cancelled, or logged for audit. The kitchen should process orders one at a time. Design the classes.

---

### State Pattern Scenarios

**37. Vending Machine**
A vending machine has states: Idle, HasMoney, Dispensing, OutOfStock. Behavior changes based on state: inserting coin works only in Idle, dispensing works only in HasMoney, etc. Transitions happen based on user actions. Design the classes.

**38. Document Approval Workflow**
Documents go through states: Draft, Submitted, UnderReview, Approved, Rejected, Published. Available actions depend on state (can submit only from Draft, can approve only from UnderReview). State transitions have rules. Design the classes.

**39. Game Character States**
A game character has states: Idle, Walking, Running, Jumping, Attacking, Dead. Available actions and animations depend on current state. State transitions happen based on user input and game events. Design the classes.

---

### Template Method Pattern Scenarios

**40. Data Import System**
Your system imports data from files. The process is: Open File → Read Header → Validate → Parse Data → Transform → Save to DB → Close File. The overall flow is fixed, but parsing and transformation vary by file type (CSV, Excel, XML). Design the classes.

**41. Report Generation Framework**
All reports follow the same process: Fetch Data → Sort Data → Format Data → Add Header/Footer → Export. The data fetching and formatting vary by report type, but the overall algorithm is the same. Design the classes.

**42. Build System**
Your build system follows a fixed process: Compile → Test → Package → Deploy. Different project types (Java, Python, JavaScript) have different compile and test commands, but the overall flow is the same. Design the classes.

---

### Iterator Pattern Scenarios

**43. Custom Collection System**
You're building a custom tree data structure. Users should be able to iterate over nodes in different orders (In-order, Pre-order, Post-order, Level-order) without exposing the internal tree structure. Design the classes.

**44. Playlist Manager**
Your music player has playlists that can be traversed in different ways: Sequential, Shuffle, Repeat One, Repeat All. Users should navigate next/previous without knowing the traversal strategy. Design the classes.

**45. Database Result Set**
Your database library returns large result sets. Users should iterate through results one at a time without loading all results into memory. Support forward, backward, and random access iteration. Design the classes.

---

## Category 4: Mixed/Advanced Patterns (Questions 46-50)

### Multiple Patterns Scenarios

**46. Coffee Machine System**
A coffee machine has:
- Different coffee types (Espresso, Latte, Cappuccino, Americano) - each with different recipes
- Add-ons (Milk, Sugar, Extra Shot) that can be combined
- States (Idle, Brewing, OutOfIngredients, Maintenance)
- Inventory tracking for ingredients
- Usage logging for maintenance

Design the classes using appropriate patterns.

---

**47. Ride-Sharing App**
A ride-sharing platform needs:
- Different vehicle types (Car, Bike, SUV, Luxury) with different pricing
- Pricing strategies (Standard, Surge, Discount, Promotional)
- Driver-rider matching with multiple algorithms
- Real-time location updates to riders
- Payment processing with multiple providers
- Ride states (Requested, Accepted, InProgress, Completed, Cancelled)

Design the classes using appropriate patterns.

---

**48. Smart Home Automation**
A smart home system has:
- Multiple device types (Lights, Thermostat, Locks, Cameras, Speakers)
- Device groups (Rooms, Zones, Floors)
- Automation rules (If motion detected after sunset → turn on lights)
- Remote access via different protocols (WiFi, Bluetooth, Zigbee)
- User access control (Admin, Family, Guest)
- Scene modes (Good Morning, Good Night, Away, Party)

Design the classes using appropriate patterns.

---

**49. Airline Booking System**
An airline reservation system needs:
- Multiple seat classes (Economy, Business, First) with different pricing
- Pricing strategies (Early Bird, Last Minute, Group, Loyalty)
- Flight search with multiple criteria (Direct, Connecting, Flexible dates)
- Booking states (Reserved, Confirmed, CheckedIn, Boarded, Cancelled)
- Multiple payment methods
- Seat selection with visual layout
- Passenger information management

Design the classes using appropriate patterns.

---

**50. Hospital Management System**
A hospital management system needs:
- Multiple staff types (Doctor, Nurse, Admin, Technician) with different permissions
- Appointment scheduling with different specialties
- Patient records with access control
- Multiple treatment types with different workflows
- Billing with insurance processing
- Inventory management for medicines and equipment
- Emergency vs Regular patient handling

Design the classes using appropriate patterns.

---

## Tips for Solving These Problems

### 1. Identify the Variation Points
Ask yourself:
- What changes frequently?
- What should be extensible?
- What should be hidden from clients?

### 2. Match Patterns to Problems

| Problem Characteristic | Consider Pattern |
|------------------------|------------------|
| Object creation logic varies | Factory |
| Complex object construction | Builder |
| Only one instance needed | Singleton |
| Incompatible interfaces | Adapter |
| Add responsibilities dynamically | Decorator |
| Tree structures | Composite |
| Simplify complex subsystem | Facade |
| Control access to object | Proxy |
| Algorithm varies independently | Strategy |
| One-to-many dependencies | Observer |
| Undo/redo, command queue | Command |
| State-dependent behavior | State |
| Fixed algorithm with variations | Template Method |
| Custom iteration | Iterator |

### 3. SOLID Principles Checklist
- **SRP**: Does each class have one responsibility?
- **OCP**: Can I add new functionality without modifying existing code?
- **LSP**: Can subclasses be substituted for base classes?
- **ISP**: Are interfaces focused and not bloated?
- **DIP**: Do classes depend on abstractions, not concretions?

### 4. Common Pattern Combinations
- Factory + Strategy (create different strategies)
- Decorator + Composite (add responsibilities to tree structures)
- Observer + Strategy (different notification strategies)
- Command + State (state-dependent commands)
- Builder + Factory (build complex products)

---

## Answer Template

For each question, structure your answer as:

```markdown
## Question X: [System Name]

### Classes:
```java
// Core interfaces/abstract classes
public interface X { ... }

// Concrete implementations
public class ConcreteX implements X { ... }

// Client usage example
public class Client {
    public static void main(String[] args) {
        // Usage example
    }
}
```

### Design Patterns Used:
1. **Pattern Name** - Why: [explanation]
2. **Pattern Name** - Why: [explanation]

### Key Design Decisions:
- [Decision 1 and rationale]
- [Decision 2 and rationale]

### UML Diagram (text representation):
[ClassA] --(implements)--> [Interface]
[ClassB] --(extends)--> [ClassA]
```

---

Good luck with your low-level design practice! 🍀
