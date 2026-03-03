# Microservices

### Q1: Microservices architecture কী?

**A:** একটি বড় application-কে ছোট ছোট independent service-এ ভাগ করার architectural style। প্রতিটি service আলাদাভাবে deploy, scale ও develop করা যায়।

```
Monolith:       [Order + User + Payment + Notification] → একটি deploy

Microservices:  [Order Service]      → আলাদা deploy
                [User Service]       → আলাদা deploy
                [Payment Service]    → আলাদা deploy
                [Notification Service] → আলাদা deploy
```

### Q2: Monolith vs Microservices?

**A:**
| বিষয় | Monolith | Microservices |
|---|---|---|
| Deploy | সব একসাথে | আলাদা আলাদা |
| Scale | পুরো app scale করতে হয় | শুধু প্রয়োজনীয় service |
| Failure | একটি bug সব ভাঙে | isolated failure |
| Complexity | সহজ শুরুতে | বেশি complexity |

### Q3: Service-to-service communication কীভাবে হয়?

**A:** দুটি প্রধান উপায়:
- **Synchronous:** HTTP/REST বা gRPC — একটি service সরাসরি আরেকটিকে call করে।
- **Asynchronous:** Message broker (RabbitMQ, Kafka) — event publish করে, অন্য service subscribe করে।

```
Sync:   OrderService → HTTP → PaymentService → response ফেরত দেয়

Async:  OrderService → [Kafka] → NotificationService (event consume করে)
```

### Q4: API Gateway কী এবং কেন দরকার?

**A:** API Gateway হলো সব client request-এর একটি single entry point। এটি routing, authentication, rate limiting, logging ইত্যাদি centrally handle করে।

```
Client → [API Gateway] → Order Service
                       → User Service
                       → Payment Service
```

> Client-কে প্রতিটি service-এর URL জানতে হয় না।

### Q5: Saga Pattern কী?

**A:** Distributed transaction handle করার pattern। যেহেতু microservices-এ একটি ACID transaction সম্ভব নয়, Saga প্রতিটি step-এ local transaction করে এবং ব্যর্থ হলে compensating transaction চালায়।

```
Order Created → Payment Charged → Inventory Reserved
                     ↓ (ব্যর্থ)
             Compensate: Order Cancel করো
```

### Q6: Circuit Breaker pattern কী?

**A:** কোনো downstream service বারবার fail করলে Circuit Breaker সেই service-এ call বন্ধ করে দেয় (open state), যাতে system overload না হয়। কিছুক্ষণ পর আবার try করে (half-open)।

```
Normal:     Request → Service B ✓
Failing:    Request → Service B ✗✗✗ → Circuit OPEN
Open:       Request → Fallback response (Service B-কে call করা হয় না)
Half-Open:  একটি test request → সফল হলে Circuit CLOSE
```

### Q7: Event-Driven Architecture কী?

**A:** Service গুলো সরাসরি একে অপরকে call না করে event publish/subscribe করে communicate করে। এটি loose coupling নিশ্চিত করে।

```csharp
// Order Service — event publish করে
await _publisher.PublishAsync(new OrderCreatedEvent { OrderId = 1, Amount = 500 });

// Notification Service — event consume করে
public async Task HandleAsync(OrderCreatedEvent e)
    => await _emailService.SendAsync($"Order {e.OrderId} confirmed!");
```

### Q8: Service Discovery কী?

**A:** Microservices dynamically চালু/বন্ধ হয়, তাই hardcoded IP কাজ করে না। Service Discovery (যেমন Consul, Kubernetes DNS) service-এর current address automatically খুঁজে দেয়।

```
Client → Service Registry ("order-service কোথায়?")
       ← "192.168.1.5:8080"
Client → 192.168.1.5:8080
```

### Q9: Distributed Tracing কী এবং কেন দরকার?

**A:** একটি request অনেক service-এর মধ্য দিয়ে যায়। Distributed tracing (যেমন OpenTelemetry, Jaeger) প্রতিটি service-এ কতক্ষণ লাগলো তা trace করে, debugging সহজ করে।

```
Request ID: abc-123
  → API Gateway       5ms
  → Order Service    20ms
  → Payment Service  150ms  ← এখানে slow!
  → Notification      8ms
Total: 183ms
```

### Q10: Microservices-এ Data Management কীভাবে করা হয়?

**A:** প্রতিটি service-এর **নিজস্ব database** থাকে (Database per Service pattern)। অন্য service-এর data সরাসরি access করা যায় না — শুধু API বা event-এর মাধ্যমে।

```
Order Service   → orders_db   (PostgreSQL)
User Service    → users_db    (PostgreSQL)
Product Service → products_db (MongoDB)
Session Service → sessions_db (Redis)
```

> এতে প্রতিটি service independently scale ও deploy করা যায় এবং একটির database পরিবর্তনে অন্যটি প্রভাবিত হয় না।
