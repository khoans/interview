**3. Cache Manager**
You need to design an in-memory cache for frequently accessed data (like user sessions). The cache should be accessible from anywhere in the application, but there should only be one cache instance to ensure data consistency. Design the classes.

```Java
class UserSession {
    private String sessionId;
    // constructor , getters, etc
}

public class CacheManager {
    private static volatile CacheManager instance;
    private UserSession session; 
    
    private CacheManager() {
        loadConfigs();
    }
    
    private void loadConfigs() {
        
    }
    
    public static CacheManager getInstance() {
        if (instance == null) {
            synchronized (CacheManager.class) {
                if (instance == null) {
                    instance = new CacheManager();
                    return instance;
                }
            }
        }
        return instance;
    }
}
```
