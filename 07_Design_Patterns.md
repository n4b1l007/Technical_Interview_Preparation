# Design Patterns

### Q23: Explain SOLID.

**A:** SOLID হলো Object Oriented Design (OOD)-এর পাঁচটি নীতির সমষ্টি যা কোডকে reusable ও maintainable করে।

- **S — Single Responsibility Principle**
  প্রতিটি class বা module শুধুমাত্র একটি দায়িত্ব পালন করবে।
  > **উদাহরণ:** একটি class শুধু User registration করবে — login বা অন্য কাজ করবে না।

- **O — Open/Closed Principle**
  Class extension-এর জন্য open, কিন্তু modification-এর জন্য closed।
  > **উদাহরণ:** `User` class-কে সরাসরি না বদলে, inherit করে `LoginService`, `RegisterService` তৈরি করো।

- **L — Liskov Substitution Principle**
  Subclass দিয়ে parent class replace করা যাবে, কোনো behavior ভাঙবে না।
  > **উদাহরণ:** `Bird` class-এ `fly()` আছে। `Magpie` (subclass) দিয়ে `Bird`-এর জায়গায় কাজ করা যায়। কিন্তু `Penguin`-এ `fly()` নেই, তাই LSP ভঙ্গ হয়।

- **I — Interface Segregation Principle**
  বড় interface না বানিয়ে ছোট ছোট interface তৈরি করো, যাতে class শুধু প্রয়োজনীয় method implement করে।
  > **উদাহরণ:** রেস্তোরাঁর online ও offline order-এর জন্য আলাদা interface রাখো, কারণ online-এর অনেক feature offline-এ লাগে না।

- **D — Dependency Inversion Principle**
  High-level module, low-level module-এর উপর সরাসরি নির্ভর করবে না — abstraction-এর উপর নির্ভর করবে।
  > **উদাহরণ:** `SendMail` class সরাসরি `Gmail`-এর উপর নির্ভর না করে একটি `IEmailService` interface-এর উপর নির্ভর করবে। তাহলে `Gmail` বা `Yahoo` যেকোনোটি ব্যবহার করা যাবে। 

### Q24: Repository pattern pros & cons?

**A:**
- **সুবিধা:** Abstraction তৈরি হয়, testing সহজ হয়।
- **অসুবিধা:** EF-এর উপরে unnecessary extra layer হয়ে যেতে পারে।

### Q25: What is CQRS?

**A:** Command Query Responsibility Segregation — read ও write model আলাদা করে scalability ও code clarity উন্নত করে।

### Q26: What is the Singleton design pattern?

**A:** একটি class-এর শুধুমাত্র একটি instance নিশ্চিত করে। .NET DI-তে `AddSingleton` এটি automatically করে। Manual উদাহরণ:

```csharp
public class Config
{
    private static Config _instance;
    private Config() { }
    public static Config Instance => _instance ??= new Config();
}

var c1 = Config.Instance;
var c2 = Config.Instance;
Console.WriteLine(c1 == c2); // True — same instance
```

### Q27: What is the Factory pattern?

**A:** Object তৈরির দায়িত্ব একটি factory method-এ দেওয়া হয়, construction logic লুকিয়ে রাখা হয়।

```csharp
interface INotification { void Send(string msg); }
class EmailNotification : INotification { public void Send(string m) => Console.WriteLine($"Email: {m}"); }
class SmsNotification   : INotification { public void Send(string m) => Console.WriteLine($"SMS: {m}"); }

class NotificationFactory
{
    public static INotification Create(string type) => type switch
    {
        "email" => new EmailNotification(),
        "sms"   => new SmsNotification(),
        _ => throw new ArgumentException("Unknown type")
    };
}

var n = NotificationFactory.Create("email");
n.Send("Hello!"); // Email: Hello!
```

### Q28: What is the Decorator pattern?

**A:** কোনো class পরিবর্তন না করেই dynamically object-এর behavior যোগ করা যায়।

```csharp
interface ILogger { void Log(string msg); }
class ConsoleLogger : ILogger { public void Log(string m) => Console.WriteLine(m); }

class TimestampLogger : ILogger
{
    private readonly ILogger _inner;
    public TimestampLogger(ILogger inner) { _inner = inner; }
    public void Log(string m) => _inner.Log($"[{DateTime.Now}] {m}");
}

ILogger logger = new TimestampLogger(new ConsoleLogger());
logger.Log("Started"); // [2026-02-27 ...] Started
```

### Q29: What is the Mediator pattern (MediatR)?

**A:** একটি central mediator-এর মাধ্যমে request route করে object গুলোকে decouple করা হয়। .NET-এ CQRS-এর সাথে সাধারণত ব্যবহৃত হয়।

```csharp
// Command
public record CreateOrderCommand(string Item) : IRequest<int>;

// Handler
public class CreateOrderHandler : IRequestHandler<CreateOrderCommand, int>
{
    public Task<int> Handle(CreateOrderCommand cmd, CancellationToken ct)
        => Task.FromResult(42); // returns new order id
}

// Controller
var id = await _mediator.Send(new CreateOrderCommand("Laptop"));
```

### Q30: What is the Observer pattern?

**A:** একটি object (subject) তার state পরিবর্তন হলে একাধিক subscriber-কে জানায়। C#-এর event এই pattern-এর উপর তৈরি।

```csharp
class Stock
{
    public event Action<decimal> PriceChanged;
    private decimal _price;
    public decimal Price
    {
        get => _price;
        set { _price = value; PriceChanged?.Invoke(value); }
    }
}

var stock = new Stock();
stock.PriceChanged += p => Console.WriteLine($"New price: {p}");
stock.Price = 150.5m; // New price: 150.5
```


---

**[Next: 08_Tricky_Questions.md →](08_Tricky_Questions.md)**
