**32.** Design a Weather Station using the Observer pattern. Multiple displays (Current Conditions, Statistics, Forecast) update when measurements change.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Weather Station — Observer Pattern

**Pattern:** Observer (Behavioral)
**Why Observer?** Classic textbook example — one sensor source, many independent displays that react to data changes without the station knowing about display types.

**SOLID Principles Applied:**
- **SRP:** WeatherStation collects data; each display renders it differently
- **OCP:** Add new displays (HeatIndex, Wind Chill) without modifying station
- **ISP:** Displays implement only what they need via a simple callback interface

### Text UML Diagram

```
┌─────────────────────┐        ┌─────────────────────┐
│   WeatherStation    │───────→│   <<interface>>      │
│   (Subject)         │  1..*  │   WeatherDisplay     │
│─────────────────────│        │─────────────────────│
│ -temperature        │        │ +update(data)        │
│ -humidity           │        │ +display()           │
│ -pressure           │        └─────────┬───────────┘
│ +registerDisplay()  │                  │ implements
│ +removeDisplay()    │        ┌─────────┼──────────┐
│ +setMeasurements()  │        │         │          │
│ +notifyDisplays()   │     Current  Statistics  Forecast
└─────────────────────┘     Display   Display    Display
                            (latest)  (min/max/  (trend
                                       avg)     analysis)
```

### Key Design Decisions

1. **WeatherData as immutable record** — Passed to all displays, prevents displays from modifying shared state
2. **Display interface has update + display** — Separate data receipt from rendering for testability
3. **Statistics display tracks history** — Maintains running min/max/avg without storing all data points
4. **Forecast display uses trend** — Compares current vs previous pressure to predict weather

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

// ============================================================
// IMMUTABLE DATA — shared across all displays
// ============================================================

class WeatherData {
    private final double temperature; // Fahrenheit
    private final double humidity;    // percentage
    private final double pressure;    // inches of mercury
    private final long timestamp;

    public WeatherData(double temperature, double humidity, double pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        this.timestamp = System.currentTimeMillis();
    }

    public double getTemperature() { return temperature; }
    public double getHumidity() { return humidity; }
    public double getPressure() { return pressure; }
    public long getTimestamp() { return timestamp; }
}

// ============================================================
// OBSERVER INTERFACE
// ============================================================

interface WeatherDisplay {
    void update(WeatherData data);
    String display();
}

// ============================================================
// CONCRETE DISPLAYS
// ============================================================

class CurrentConditionsDisplay implements WeatherDisplay {
    private WeatherData currentData;

    @Override
    public void update(WeatherData data) {
        this.currentData = data;
        System.out.println(display());
    }

    @Override
    public String display() {
        if (currentData == null) return "[Current] No data yet";
        return String.format("[Current Conditions] Temp: %.1fF | Humidity: %.1f%% | Pressure: %.2f in",
                currentData.getTemperature(), currentData.getHumidity(), currentData.getPressure());
    }
}

class StatisticsDisplay implements WeatherDisplay {
    private double minTemp = Double.MAX_VALUE;
    private double maxTemp = Double.MIN_VALUE;
    private double tempSum = 0;
    private int readingCount = 0;

    @Override
    public void update(WeatherData data) {
        double temp = data.getTemperature();
        tempSum += temp;
        readingCount++;
        minTemp = Math.min(minTemp, temp);
        maxTemp = Math.max(maxTemp, temp);
        System.out.println(display());
    }

    @Override
    public String display() {
        if (readingCount == 0) return "[Statistics] No data yet";
        return String.format("[Statistics] Avg: %.1fF | Min: %.1fF | Max: %.1fF (%d readings)",
                tempSum / readingCount, minTemp, maxTemp, readingCount);
    }
}

class ForecastDisplay implements WeatherDisplay {
    private double lastPressure = 0;
    private double currentPressure = 0;
    private String forecast = "N/A";

    @Override
    public void update(WeatherData data) {
        lastPressure = currentPressure;
        currentPressure = data.getPressure();

        if (currentPressure > lastPressure) {
            forecast = "Improving weather on the way!";
        } else if (currentPressure < lastPressure) {
            forecast = "Watch out for cooler, rainy weather.";
        } else {
            forecast = "More of the same.";
        }
        System.out.println(display());
    }

    @Override
    public String display() {
        return String.format("[Forecast] %s (pressure: %.2f -> %.2f)",
                forecast, lastPressure, currentPressure);
    }
}

class HeatIndexDisplay implements WeatherDisplay {
    private double heatIndex = 0;

    @Override
    public void update(WeatherData data) {
        heatIndex = computeHeatIndex(data.getTemperature(), data.getHumidity());
        System.out.println(display());
    }

    @Override
    public String display() {
        return String.format("[Heat Index] %.2fF", heatIndex);
    }

    /**
     * Simplified Rothfusz regression equation for heat index.
     */
    private double computeHeatIndex(double t, double rh) {
        return 16.923 + 0.185212 * t + 5.37941 * rh
                - 0.100254 * t * rh + 0.00941695 * t * t
                + 0.00728898 * rh * rh + 0.000345372 * t * t * rh
                - 0.000814971 * t * rh * rh
                + 0.0000102102 * t * t * rh * rh
                - 0.000038646 * t * t * t
                + 0.0000291583 * rh * rh * rh
                + 0.00000142721 * t * t * t * rh
                + 0.000000197483 * t * rh * rh * rh
                - 0.0000000218429 * t * t * t * rh * rh
                + 0.000000000843296 * t * t * rh * rh * rh
                - 0.0000000000481975 * t * t * t * rh * rh * rh;
    }
}

// ============================================================
// SUBJECT — Weather Station
// ============================================================

class WeatherStation {
    private final List<WeatherDisplay> displays = new CopyOnWriteArrayList<>();
    private WeatherData latestData;

    public void registerDisplay(WeatherDisplay display) {
        displays.add(display);
    }

    public void removeDisplay(WeatherDisplay display) {
        displays.remove(display);
    }

    public void setMeasurements(double temperature, double humidity, double pressure) {
        this.latestData = new WeatherData(temperature, humidity, pressure);
        notifyDisplays();
    }

    private void notifyDisplays() {
        System.out.println("\n--- Weather Update ---");
        for (WeatherDisplay display : displays) {
            display.update(latestData);
        }
    }

    public WeatherData getLatestData() {
        return latestData;
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class WeatherStationDemo {
    public static void main(String[] args) {
        WeatherStation station = new WeatherStation();

        // Register displays
        CurrentConditionsDisplay current = new CurrentConditionsDisplay();
        StatisticsDisplay stats = new StatisticsDisplay();
        ForecastDisplay forecast = new ForecastDisplay();
        HeatIndexDisplay heatIndex = new HeatIndexDisplay();

        station.registerDisplay(current);
        station.registerDisplay(stats);
        station.registerDisplay(forecast);
        station.registerDisplay(heatIndex);

        // Simulate sensor readings
        station.setMeasurements(80, 65, 30.4);
        station.setMeasurements(82, 70, 29.2);
        station.setMeasurements(78, 90, 29.2);

        // Remove a display
        station.removeDisplay(heatIndex);
        station.setMeasurements(75, 60, 30.1);
    }
}
```

### Interview Talking Points

- **Head First Design Patterns classic:** This is the textbook Observer example. Knowing it cold shows pattern fluency.
- **Push vs Pull trade-off:** Here we push all data. Alternative: push a "changed" signal, let displays pull only what they need via `station.getTemperature()`. Pull reduces unnecessary data transfer.
- **Java built-in:** `java.util.Observable` was deprecated in Java 9. Use `PropertyChangeListener` or build your own like this. In Spring, use `ApplicationEventPublisher`.
- **Thread safety:** `CopyOnWriteArrayList` is fine for few displays. For high-frequency updates with many observers, consider async notification with an `ExecutorService`.
