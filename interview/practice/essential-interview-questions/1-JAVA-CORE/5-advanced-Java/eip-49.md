49. What is the difference between Runnable and Callable?

**Runnable returns no result and can't throw checked exceptions. Callable returns a result and can throw exceptions.**

Runnable is the older interface (Java 1.0) — `run()` returns void. Callable (Java 5) was added for ExecutorService — `call()` returns a value and throws Exception. Both are functional interfaces for task submission.

**Trade-offs:**
- Runnable: Simpler, no result handling, but you lose error information and output
- Callable: Returns Future<V> — you can get result, check exceptions, but adds complexity
- Exception handling: Runnable exceptions kill the thread silently; Callable exceptions are captured in Future

**Real-world example:**
We had a data validation job originally using Runnable — it validated customer records and updated a shared counter. Problem: couldn't return which records failed, and exceptions were logged but lost. Refactored to Callable<List<ValidationError>>. Each task returned its failures. Used Future.get() to collect all results. Could report "95% valid, 50 records failed" with specific error details. Made debugging 10x faster.

**Key insight:** Use Runnable for fire-and-forget tasks — logging, notifications, cache updates. Use Callable when you need results or error handling — data processing, validations, computations. The Future from Callable is worth the extra complexity when the task can fail or produce valuable output.

**Full example: Callable implementation with ExecutorService**

```java
// 1. Define a Callable that processes an order
public class OrderProcessor implements Callable<OrderResult> {
    private final Order order;
    private final PaymentService paymentService;

    public OrderProcessor(Order order, PaymentService paymentService) {
        this.order = order;
        this.paymentService = paymentService;
    }

    @Override
    public OrderResult call() throws Exception {
        // Validate order
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Empty order");
        }

        // Process payment
        PaymentResponse payment = paymentService.charge(order);

        // Return result
        return new OrderResult(order.getId(), payment.getStatus(), order.getTotal());
    }
}

// 2. Use it with ExecutorService
public class OrderProcessingService {
    private final ExecutorService executor;
    private final PaymentService paymentService;

    public OrderProcessingService(int threadCount, PaymentService paymentService) {
        this.executor = Executors.newFixedThreadPool(threadCount);
        this.paymentService = paymentService;
    }

    public List<OrderResult> processOrders(List<Order> orders) throws InterruptedException {
        List<Future<OrderResult>> futures = new ArrayList<>();

        // Submit all orders for processing
        for (Order order : orders) {
            Callable<OrderResult> task = new OrderProcessor(order, paymentService);
            futures.add(executor.submit(task));
        }

        // Collect results
        List<OrderResult> results = new ArrayList<>();
        for (Future<OrderResult> future : futures) {
            try {
                results.add(future.get(30, TimeUnit.SECONDS));
            } catch (ExecutionException e) {
                // Log failed order, continue with others
                logger.error("Order failed: " + e.getCause().getMessage());
            } catch (TimeoutException e) {
                logger.error("Order timed out");
            }
        }

        return results;
    }

    public void shutdown() {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
            Thread.currentThread().interrupt();
        }
    }
}

// 3. Usage
public class Main {
    public static void main(String[] args) {
        PaymentService paymentService = new PaymentService();
        OrderProcessingService service = new OrderProcessingService(10, paymentService);

        List<Order> orders = loadOrdersFromDatabase();

        try {
            List<OrderResult> results = service.processOrders(orders);
            System.out.println("Processed " + results.size() + " orders");
        } finally {
            service.shutdown();
        }
    }
}
```

**Key points:**
- Callable has one method: `call()` returns a value and throws Exception
- ExecutorService.submit() returns Future<T>
- Future.get() blocks until result is ready
- Always handle ExecutionException (wraps your Callable's exception)
- Always shutdown ExecutorService or threads leak
