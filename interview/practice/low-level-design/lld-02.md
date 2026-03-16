**2. Logger Service**
Your application needs a logging system that writes to files. Multiple threads across the application will log messages simultaneously. You need to ensure all logs go through a single instance to prevent file access conflicts and maintain log order. Design the classes.


```Java
public class LoggingService {
    private static volatile LoggingService instance;
    
    private LoggingService() {
        // Loading dependencies
    }
    
    private static LoggingService() {
        if (instance == null) {
            synchronized (LoggingService.class) {
                if (instance == null) {
                    instance = new LoggingService();
                    return instance;
                }
            }
        }
        return instance;
    }
    
}
```
