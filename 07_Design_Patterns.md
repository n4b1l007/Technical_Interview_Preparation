# Design Patterns

### Q1: Explain SOLID.

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

### Q2: Repository pattern pros & cons?

**A:**
- **সুবিধা:** Abstraction তৈরি হয়, testing সহজ হয়।
- **অসুবিধা:** EF-এর উপরে unnecessary extra layer হয়ে যেতে পারে।

### Q3: What is CQRS?

**A:** Command Query Responsibility Segregation — read ও write model আলাদা করে scalability ও code clarity উন্নত করে।

### Q4: How many types of design pattern?

**A:** তিনটি প্রধান ধরন আছে — Creational (object creation), Structural (object composition), এবং Behavioral (object interaction)।

- **Creational (উৎপাদনগত):** অবজেক্ট কীভাবে তৈরি হবে তা নিয়ন্ত্রণ করে। উদাহরণ: `Singleton`, `Factory`, `Builder` — কখন বা কিভাবে নতুন instance তৈরি হবে বা অনুকরণ করে নেওয়া হবে তা নির্ধারণ করে।
- **Structural (গঠনগত):** ক্লাস বা অবজেক্টগুলোকে কিভাবে একসাথে সাজানো যায় তা নিয়ে কাজ করে। উদাহরণ: `Adapter`, `Facade`, `Decorator` — বিভিন্ন অংশকে মিলিয়ে সহজ ও পরিচ্ছন্ন API দেয়।
- **Behavioral (আচরণগত):** অবজেক্টগুলোর মধ্যে যোগাযোগ ও আচরণ কীভাবে পরিচালিত হবে তা নির্ধারণ করে। উদাহরণ: `Strategy`, `Observer`, `Mediator` — কিভাবে তারা ইন্টারেক্ট করে, কাকে কল করে এবং কবে আচরণ বদলাবে তা নিয়ন্ত্রণ করে।

সংক্ষেপে: Creational → নতুন instances কিভাবে/কখন; Structural → অংশগুলোকে কিভাবে organize করব; Behavioral → objects একে অপরের সাথে কিভাবে আচরণ করবে।


### Q5: What is the Singleton design pattern?

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

### Q6: What is the Factory pattern?

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

### Q7: What is the Decorator pattern?

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

### Q8: What is the Mediator pattern (MediatR)?

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

### Q9: What is the Observer pattern?

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


### Q10: What is the Adapter pattern?

**A:** Adapter হলো এক interface-কে অন্য interface-এ map করে এমনভাবে যাতে incompatible classes একসাথে কাজ করতে পারে।

```csharp
// Old API
class OldLogger { public void Write(string m) => Console.WriteLine(m); }

// Target interface
interface ILogger { void Log(string m); }

// Adapter
class LoggerAdapter : ILogger
{
  private readonly OldLogger _old;
  public LoggerAdapter(OldLogger old) { _old = old; }
  public void Log(string m) => _old.Write(m);
}

ILogger logger = new LoggerAdapter(new OldLogger());
logger.Log("Hello Adapter");
```

### Q11: What is the Strategy pattern?

**A:** Strategy হলো behavior-কে runtime-এ interchangeable strategy objects হিসেবে আলাদা করা—algorithm selection সোজা করে।

```csharp
interface ICompression { byte[] Compress(byte[] data); }
class ZipCompression : ICompression { public byte[] Compress(byte[] d) => d; }
class RarCompression : ICompression { public byte[] Compress(byte[] d) => d; }

class FileArchiver { private ICompression _strategy; public FileArchiver(ICompression s) { _strategy = s; } }
```

### Q12: What is the Prototype pattern?

**A:** Prototype pattern একটি object-এর copy করে নতুন instances তৈরি করে — costly construction বাঁচায়। `ICloneable` বা custom `Clone()` method ব্যবহার করা হয়।

```csharp
class Person { public string Name; public Person Clone() => (Person)MemberwiseClone(); }
```

### Q13: What is the Builder pattern and when to use it?

**A:** Builder pattern অনেকগুলো optional parameter থাকা complex object step-by-step তৈরির জন্য। immutability ও readable construction code দেয়।

```csharp
class Pizza { public string Dough; public string Sauce; }
class PizzaBuilder { /* fluent API to set parts and Build() */ }
```

### Q14: What is the Facade pattern?

**A:** Facade একটি simplified higher-level interface দেয় যা subsystem-এর complexity hide করে—client code সহজ হয়।

```csharp
class PaymentFacade { public void Pay() { /* uses Gateway, Ledger, Notifier internally */ } }
```

### Q15: What is the Flyweight pattern?

**A:** Flyweight memory-efficient approach—shared intrinsic state ব্যবহার করে many fine-grained objects তৈরি করা যায়। UI বা text rendering-এ common।

### Q16: What is the Null Object pattern?

**A:** Null Object একটি do-nothing implementation যেকে null-check না করে use করা যায়—control flow simple হয়।

```csharp
interface ILogger { void Log(string m); }
class NullLogger : ILogger { public void Log(string m) { } }
// use NullLogger instead of null
```

### Q17: What is the Template Method pattern?

**A:** Template Method base class-এ algorithm outline দেয় এবং subtasks জন্য virtual/abstract steps দেয়—subclass গুলো specific step implement করে।

```csharp
abstract class DataParser
{
  public void Parse() { Read(); ParseData(); Save(); }
  protected abstract void Read();
  protected abstract void ParseData();
  protected virtual void Save() { /* default */ }
}
```


---

### Q18: Provide a summary table of common design patterns

**A:** নিচে জনপ্রিয় design patterns-এর একটি compact summary টেবিল আছে — category, সমস্যার ধরন এবং সংক্ষিপ্ত উদাহরণ দেয়া আছে।

| Pattern | Category | Problem solved | C# (short) |
|---|---|---|---|
| Singleton | Creational | একক ইনস্ট্যান্স নিশ্চিত করা — global/shared resource নিয়ন্ত্রণ করা হয় | `Config.Instance` |
| Factory | Creational | অবজেক্ট তৈরির লজিক কেন্দ্রীয়করণ করে creation code abstract করা হয় | `NotificationFactory.Create()` |
| Builder | Creational | বহু optional অংশসহ জটিল অবজেক্ট ধাপে ধাপে তৈরি করা যায় | `new PizzaBuilder().Build()` |
| Prototype | Creational | বিদ্যমান অবজেক্ট ক্লোন করে দ্রুত নতুন ইনস্ট্যান্স তৈরি করা যায় | `obj.Clone()` |
| Adapter | Structural | অসমঞ্জস ইন্টারফেসগুলোকে মিলিয়ে compatibility layer সরবরাহ করা হয় | `new LoggerAdapter(old)` |
| Facade | Structural | একাধিক সাবসিস্টেমের জটিলতা একটি সহজ higher-level API দিয়ে লুকানো হয় | `new PaymentFacade().Pay()` |
| Flyweight | Structural | কমন স্টেট শেয়ার করে মেমরি কার্যকরভাবে ব্যবহার করা হয় (many fine-grained objects) | shared glyph/character objects |
| Decorator | Structural | রানটাইমে অবজেক্টে অতিরিক্ত দায়িত্ব বা ফিচার যোগ করা যায় | `new TimestampLogger(logger)` |
| Strategy | Behavioral | বিভিন্ন অ্যালগরিদম runtime-এ interchangeable করে নির্বাচন করা যায় | `new FileArchiver(new ZipCompression())` |
| Observer | Behavioral | স্টেট পরিবর্তনে বহু সাবস্ক্রাইবারকে নোটিফাই করা হয় | `PriceChanged` event |
| Mediator | Behavioral | অবজেক্টগুলোর মধ্যে যোগাযোগ কেন্দ্রীয়ভাবে নিয়ন্ত্রণ করা হয় | `IMediator.Send()` (MediatR) |
| Template Method | Behavioral | অ্যালগরিদমের skeleton নির্ধারণ করে নির্দিষ্ট ধাপ সাবক্লাসে বাস্তবায়ন করা হয় | `abstract DataParser.Parse()` |
| Null Object | Behavioral | null চেক বাদ দিয়ে do-nothing implementation ব্যবহার করে control flow সরল করা হয় | `new NullLogger()` |


**[Next: 08_Tricky_Questions.md →](08_Tricky_Questions.md)**
