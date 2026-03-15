**1. Configuration Manager**
You're building a system where configuration settings (database URL, API keys, feature flags) need to be loaded once at startup and accessed globally throughout the application. Multiple components need to read these settings, but they should never load different versions of the configuration. Design the classes.

```Java
public class SystemConfig {
    private static volatile SystemConfig instance;
    
    private SystemConfig() {
        loadConfigs();
    }
    
    public static SystemConfig getInstance() {
        if (instance == null) {
            synchronized (SystemConfig.class) {
                if (instance == null) {
                    instance = new SystemConfig();
                }
            }
        }
        return instance; 
    }
}
```

Alternative:

```Java
public class SystemConfig {
    
    private SystemConfig() {
        loadConfigs();
    }
    
    private void loadConfigs() {
        // Logic
    }
    
    private static class Holder {
        private static final SystemConfig INSTANCE = new SystemConfig();
    }
    
    public static SystemConfig getInstance() {
        return Holder.INSTANCE;
    }
}
```
