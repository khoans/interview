**6.** Notification System — Factory pattern. Send via Email, SMS, Push, Slack with different setup/auth/formatting. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Notification System — Factory Pattern

### Pattern: Factory Method + Template Method

**Why Factory?** Each notification channel has unique auth (SMTP credentials, Twilio API key, FCM token, Slack webhook), formatting (HTML email vs plain SMS vs JSON payload), and delivery logic. Factory isolates this complexity.

### Text UML

```
┌──────────────────────────┐      ┌─────────────────────────┐
│   <<interface>>          │      │  NotificationFactory    │
│   NotificationSender     │      ├─────────────────────────┤
├──────────────────────────┤      │ + create(channel):      │
│ + send(notification): void│     │     NotificationSender  │
│ + supports(channel): bool │     └─────────────────────────┘
└──────────┬───────────────┘
           │
    ┌──────┼──────────┬──────────────┐
    │      │          │              │
EmailSender SmsSender PushSender SlackSender

┌─────────────────────────┐
│  Notification           │
├─────────────────────────┤
│ - recipient: String     │
│ - subject: String       │
│ - body: String          │
│ - channel: Channel      │
│ - metadata: Map         │
└─────────────────────────┘
```

### Key Design Decisions

1. **Notification DTO** — Immutable data object decouples message content from delivery mechanism
2. **Channel enum** — Type-safe channel selection; prevents typos
3. **Metadata map** — Extensible per-channel data (priority, template ID) without polluting the core model
4. **Factory with registry** — Map-based factory allows runtime registration of new senders

### SOLID Principles Applied

- **SRP**: Each sender handles one channel's auth + formatting + delivery
- **OCP**: Add Telegram sender by implementing interface + registering — zero changes to existing code
- **DIP**: Service layer depends on `NotificationSender` interface

### Java Code

```java
import java.util.*;

// --- Channel Enum ---
public enum NotificationChannel {
    EMAIL, SMS, PUSH, SLACK
}

// --- Notification DTO (Immutable) ---
public class Notification {
    private final String recipient;
    private final String subject;
    private final String body;
    private final NotificationChannel channel;
    private final Map<String, String> metadata;

    public Notification(String recipient, String subject, String body,
                        NotificationChannel channel, Map<String, String> metadata) {
        this.recipient = recipient;
        this.subject = subject;
        this.body = body;
        this.channel = channel;
        this.metadata = metadata != null ? Map.copyOf(metadata) : Map.of();
    }

    public String getRecipient() { return recipient; }
    public String getSubject() { return subject; }
    public String getBody() { return body; }
    public NotificationChannel getChannel() { return channel; }
    public Map<String, String> getMetadata() { return metadata; }
}

// --- Sender Interface ---
public interface NotificationSender {
    void send(Notification notification);
    NotificationChannel getChannel();
}

// --- Email Sender ---
public class EmailSender implements NotificationSender {
    private final String smtpHost;
    private final String username;
    private final String password;

    public EmailSender(String smtpHost, String username, String password) {
        this.smtpHost = smtpHost;
        this.username = username;
        this.password = password;
    }

    @Override
    public void send(Notification notification) {
        // In production: use JavaMail API
        String htmlBody = "<html><body><h2>" + notification.getSubject()
            + "</h2><p>" + notification.getBody() + "</p></body></html>";
        System.out.printf("EMAIL to %s via %s: %s%n",
            notification.getRecipient(), smtpHost, htmlBody);
    }

    @Override
    public NotificationChannel getChannel() { return NotificationChannel.EMAIL; }
}

// --- SMS Sender ---
public class SmsSender implements NotificationSender {
    private final String twilioSid;
    private final String twilioToken;
    private final String fromNumber;

    public SmsSender(String twilioSid, String twilioToken, String fromNumber) {
        this.twilioSid = twilioSid;
        this.twilioToken = twilioToken;
        this.fromNumber = fromNumber;
    }

    @Override
    public void send(Notification notification) {
        // Truncate to 160 chars for SMS
        String smsBody = notification.getBody();
        if (smsBody.length() > 160) {
            smsBody = smsBody.substring(0, 157) + "...";
        }
        System.out.printf("SMS from %s to %s: %s%n",
            fromNumber, notification.getRecipient(), smsBody);
    }

    @Override
    public NotificationChannel getChannel() { return NotificationChannel.SMS; }
}

// --- Push Notification Sender ---
public class PushSender implements NotificationSender {
    private final String fcmServerKey;

    public PushSender(String fcmServerKey) {
        this.fcmServerKey = fcmServerKey;
    }

    @Override
    public void send(Notification notification) {
        String priority = notification.getMetadata().getOrDefault("priority", "normal");
        System.out.printf("PUSH [%s] to device %s: %s - %s%n",
            priority, notification.getRecipient(),
            notification.getSubject(), notification.getBody());
    }

    @Override
    public NotificationChannel getChannel() { return NotificationChannel.PUSH; }
}

// --- Slack Sender ---
public class SlackSender implements NotificationSender {
    private final String webhookUrl;

    public SlackSender(String webhookUrl) {
        this.webhookUrl = webhookUrl;
    }

    @Override
    public void send(Notification notification) {
        String payload = String.format(
            "{\"channel\":\"%s\",\"text\":\"*%s*\\n%s\"}",
            notification.getRecipient(),
            notification.getSubject(),
            notification.getBody()
        );
        System.out.printf("SLACK to %s via webhook: %s%n",
            notification.getRecipient(), payload);
    }

    @Override
    public NotificationChannel getChannel() { return NotificationChannel.SLACK; }
}

// --- Factory with Registry ---
public class NotificationFactory {
    private final Map<NotificationChannel, NotificationSender> senders = new EnumMap<>(NotificationChannel.class);

    public void register(NotificationSender sender) {
        senders.put(sender.getChannel(), sender);
    }

    public NotificationSender create(NotificationChannel channel) {
        NotificationSender sender = senders.get(channel);
        if (sender == null) {
            throw new IllegalArgumentException("No sender registered for: " + channel);
        }
        return sender;
    }
}

// --- Service Layer ---
public class NotificationService {
    private final NotificationFactory factory;

    public NotificationService(NotificationFactory factory) {
        this.factory = factory;
    }

    public void notify(Notification notification) {
        NotificationSender sender = factory.create(notification.getChannel());
        sender.send(notification);
    }

    // Send to multiple channels
    public void broadcast(String recipient, String subject, String body,
                          NotificationChannel... channels) {
        for (NotificationChannel ch : channels) {
            Notification n = new Notification(recipient, subject, body, ch, null);
            notify(n);
        }
    }
}
```

### Usage

```java
NotificationFactory factory = new NotificationFactory();
factory.register(new EmailSender("smtp.gmail.com", "user", "pass"));
factory.register(new SmsSender("AC123", "token", "+1234567890"));
factory.register(new PushSender("FCM_KEY"));
factory.register(new SlackSender("https://hooks.slack.com/xxx"));

NotificationService service = new NotificationService(factory);

// Send single notification
service.notify(new Notification("user@test.com", "Order Shipped",
    "Your order #123 has shipped", NotificationChannel.EMAIL, null));

// Broadcast to multiple channels
service.broadcast("user@test.com", "Alert", "System maintenance at 2AM",
    NotificationChannel.EMAIL, NotificationChannel.SLACK);
```

### Interview Talking Points

- **Registry-based factory** — More flexible than switch/case; supports runtime registration and Spring `@Autowired List<NotificationSender>`
- **Production**: In Spring, each sender is a `@Component` auto-discovered via `List<NotificationSender>` injection, registered by channel in `@PostConstruct`
- **Async delivery**: In real systems, wrap with `@Async` or push to a message queue (Kafka/RabbitMQ) for reliability and retry
- **Template pattern**: Could add `AbstractNotificationSender` with common validate-format-send steps if channels share pre/post logic
