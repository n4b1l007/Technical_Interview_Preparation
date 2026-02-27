# Technical Interview Preparation

---

## C#, .NET Core

### Q1: Difference between IEnumerable, IQueryable, and IAsyncEnumerable?

**A:**
- `IEnumerable` মেমোরিতে query execute করে।
- `IQueryable` expression tree তৈরি করে এবং database-এ query execute করে।
- `IAsyncEnumerable` asynchronously result iterate করতে দেয় (বড় ডেটা streaming-এর জন্য উপযোগী)।

### Q2: What happens under the hood with async/await?

**A:** `async/await` compiler দ্বারা তৈরি একটি state machine ব্যবহার করে। এটি নতুন thread তৈরি করে না। কোনো incomplete `Task` await করার সময় method caller-কে control ফিরিয়ে দেয় এবং Task শেষ হলে আবার resume করে।

### Q3: Difference between Task and ValueTask?

**A:** `Task` একটি reference type এবং সাধারণভাবে ব্যবহৃত হয়। `ValueTask` result আগেই পাওয়া গেলে allocation এড়িয়ে চলে, তবে misuse এড়াতে সতর্কভাবে ব্যবহার করতে হবে।

### Q4: Explain Scoped, Transient, Singleton.

**A:**
- **Transient:** প্রতিবার নতুন instance তৈরি হয়।
- **Scoped:** প্রতিটি HTTP request-এ একটি instance।
- **Singleton:** পুরো app জীবনকালে একটিই instance থাকে।

[My image](/img/1720099553928.png) 

### Q5: Explain ASP.NET Core middleware pipeline.

**A:** Middleware হলো এমন component যেগুলো একটি নির্দিষ্ট order-এ execute হয়। প্রতিটি middleware request handle, modify বা পরবর্তী component-এ pass করতে পারে।

### Q6: Value types vs Reference types?

**A:**
- **Value type:** Stack-এ store হয়। assignment-এ পুরো মান copy হয়। (`int`, `bool`, `struct`)
- **Reference type:** Heap-এ store হয়। assignment-এ শুধু reference copy হয়। (`class`, `string`, `array`)

```csharp
int a = 5;
int b = a; // b is a copy
b = 10;
Console.WriteLine(a); // 5 — unchanged

var list1 = new List<int> { 1, 2 };
var list2 = list1; // same reference
list2.Add(3);
Console.WriteLine(list1.Count); // 3 — affected!
```

### Q7: What is boxing and unboxing?

**A:** Boxing একটি value type-কে `object`-এ রূপান্তর করে (heap-এ allocate হয়)। Unboxing সেটি আবার বের করে আনে। উভয়ই ব্যয়বহুল — performance-sensitive জায়গায় এড়িয়ে চলুন।

```csharp
int x = 42;
object boxed = x;       // boxing
int unboxed = (int)boxed; // unboxing
```

### Q8: Abstract class vs Interface?

**A:**
- **Abstract class:** Implementation, field, constructor রাখতে পারে। শুধুমাত্র single inheritance সম্ভব।
- **Interface:** শুধু contract define করে (C# 8+ থেকে default method সম্ভব)। Multiple inheritance support করে।

```csharp
// Use abstract class when sharing code
abstract class Animal { public void Breathe() => Console.WriteLine("breathing"); }

// Use interface for capability contracts
interface IFlyable { void Fly(); }
class Bird : Animal, IFlyable { public void Fly() => Console.WriteLine("flying"); }
```

### Q9: What is a delegate and event?

**A:** **Delegate** হলো type-safe function pointer। **Event** হলো restricted access সহ একটি delegate — শুধুমাত্র owner invoke করতে পারে।

```csharp
delegate void Notify(string msg);

class Button
{
    public event Notify OnClick;
    public void Click() => OnClick?.Invoke("clicked!");
}

var btn = new Button();
btn.OnClick += msg => Console.WriteLine(msg);
btn.Click(); // clicked!
```

### Q10: What is a `record` type in C#?

**A:** Record হলো immutable reference type যেখানে built-in value equality, `ToString()`, এবং `with` expression support আছে।

```csharp
record Person(string Name, int Age);

var p1 = new Person("Alice", 30);
var p2 = p1 with { Age = 31 }; // new copy with changed age
Console.WriteLine(p1 == p2); // False
```

### Q11: What is `ref` vs `out` vs `in`?

**A:**
- **ref:** pass করার আগে initialize করতে হবে। read ও write উভয়ই করা যায়।
- **out:** initialize করার দরকার নেই, তবে method-এর ভেতরে অবশ্যই assign করতে হবে।
- **in:** শুধুমাত্র read-only reference (কোনো copy নেই, write করা যাবে না)।

```csharp
void Add(ref int x) { x += 10; }
void Init(out int x) { x = 5; }
void Print(in int x) { Console.WriteLine(x); } // can't modify x

int a = 1; Add(ref a);   // a = 11
int b; Init(out b);      // b = 5
Print(in a);             // 11
```

### Q12: What is LINQ?

**A:** Language Integrated Query — C#-এর মধ্যে সরাসরি SQL-এর মতো syntax দিয়ে collection query করার সুবিধা দেয়।

```csharp
var nums = new[] { 1, 2, 3, 4, 5 };

var evens = nums.Where(n => n % 2 == 0)
                .Select(n => n * n)
                .ToList(); // [4, 16]
```

---

## REST API / Web API

### Q6: What makes an API RESTful?

**A:** Stateless, resource-based URL, HTTP verb সঠিকভাবে ব্যবহার, সঠিক status code এবং uniform interface থাকলে একটি API RESTful হয়।

- **Stateless:** প্রতিটি request স্বাধীন। Server আগের request এর কোনো state ধরে রাখে না। Client প্রত্যেকবার সব প্রয়োজনীয় তথ্য পাঠাবে — Server session ধরে রাখবে না।
- **Resource-based URL:**
  - খারাপ উদাহরণ: `/createOrder`
  - ভালো উদাহরণ: `/users`, `/orders`, `/users/10`

### Q7: PUT vs PATCH?

**A:** `PUT` পুরো resource replace করে। `PATCH` শুধুমাত্র resource-এর একটি অংশ update করে।

### Q8: Common HTTP status codes?

**A:**
- `200` OK
- `201` Created
- `400` Bad Request
- `404` Not Found
- `409` Conflict
- `500` Internal Server Error

### Q9: How to secure Web API?

**A:** JWT authentication, OAuth2, HTTPS, সঠিক validation এবং role-based authorization ব্যবহার করে API secure করা যায়।

### Q10: What is idempotency?

**A:** কোনো operation idempotent হলে তাকে একাধিকবার call করলেও একই result পাওয়া যায়।
- `GET`, `PUT`, `DELETE` — idempotent
- `POST` — idempotent নয় (প্রতিবার নতুন resource তৈরি করে)

```
POST /orders  → creates new order every call (not idempotent)
PUT /orders/5 → always ends up with same state (idempotent)
```

### Q11: What is the difference between Authentication and Authorization?

**A:**
- **Authentication:** তুমি কে? (পরিচয় যাচাই — JWT login)
- **Authorization:** তুমি কী করতে পারবে? (roles/permissions)

```csharp
[Authorize]               // must be authenticated
[Authorize(Roles = "Admin")] // must also be Admin
public IActionResult Delete() { ... }
```

### Q12: What is API Versioning and why is it important?

**A:** Versioning-এর মাধ্যমে existing client না ভেঙে API পরিবর্তন বা উন্নত করা যায়।

```
/api/v1/users  → old contract
/api/v2/users  → new contract with breaking changes
```

```csharp
// In .NET using Asp.Versioning
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/users")]
public class UsersV1Controller : ControllerBase { ... }
```

### Q13: What is CORS and when do you need it?

**A:** Cross-Origin Resource Sharing — browser-এর একটি security policy যা ভিন্ন domain থেকে আসা request block করে। Server-এ configure করে নির্দিষ্ট origin-কে allow করা যায়।

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFrontend",
        policy => policy.WithOrigins("https://myapp.com").AllowAnyMethod());
});
app.UseCors("AllowFrontend");
```

---

## Async Programming

### Q10: Why async improves scalability?

**A:** Async I/O operation-এর জন্য অপেক্ষা করার সময় thread মুক্ত রাখে, ফলে throughput বাড়ে এবং thread pool starvation কমে।

### Q11: What happens if you call `.Result` or `.Wait()`?

**A:** এটি deadlock তৈরি করতে পারে এবং thread block করে দেয়।

### Q12: Difference between concurrency and parallelism?

**A:**
- **Concurrency:** একসাথে একাধিক task manage করা (একটি করে চালানো যায়)।
- **Parallelism:** একাধিক task সত্যিকারের একই সময়ে execute করা।

### Q13: What is `ConfigureAwait(false)`?

**A:** awaiter-কে বলে যে original synchronization context-এ (যেমন UI thread) resume করতে হবে না। Library code-এ deadlock এড়াতে ব্যবহার করা হয়।

```csharp
// In a library (no UI context needed)
var data = await httpClient.GetStringAsync(url).ConfigureAwait(false);

// In ASP.NET Core it usually doesn't matter (no SyncContext),
// but it's still good practice in library code.
```

### Q14: What is `CancellationToken`?

**A:** Async operation cooperatively cancel করার সুবিধা দেয়। এটি pass করুন এবং cancellation request হলে check/throw করুন।

```csharp
public async Task DoWorkAsync(CancellationToken ct)
{
    await Task.Delay(5000, ct); // throws OperationCanceledException if cancelled
}

// Caller
var cts = new CancellationTokenSource(timeout: TimeSpan.FromSeconds(2));
await DoWorkAsync(cts.Token);
```

### Q15: `Task.WhenAll` vs `Task.WhenAny`?

**A:**
- `WhenAll` — সব task complete হওয়া পর্যন্ত অপেক্ষা করে।
- `WhenAny` — প্রথম task complete হলেই return করে।

```csharp
var t1 = FetchUserAsync();
var t2 = FetchOrdersAsync();
var (user, orders) = await (t1, t2); // or Task.WhenAll(t1, t2)

var fastest = await Task.WhenAny(t1, t2); // whichever finishes first
```

---

## Dependency Injection

### Q13: What problem does DI solve?

**A:** Tight coupling দূর করে এবং testability বাড়ায়।

### Q14: What happens if scoped service injected into singleton?

**A:** এটি runtime exception তৈরি করে কারণ scoped lifetime singleton-এর ভেতরে থাকতে পারে না।

### Q15: What is Composition Root?

**A:** যেখানে object graph তৈরি হয় — যেখানে app-এর DI container configure করা হয় (সাধারণত .NET Core-এ `Program.cs`-এ)।

### Q16: What is the Service Locator anti-pattern?

**A:** নিজের code-এর ভেতর থেকে container থেকে manually dependency resolve করা — এটি dependency লুকিয়ে রাখে ও testing কঠিন করে। এর বদলে constructor injection ব্যবহার করুন।

```csharp
// BAD — Service Locator
public class OrderService
{
    private readonly IRepo _repo;
    public OrderService(IServiceProvider sp)
    {
        _repo = sp.GetService<IRepo>(); // hidden dependency
    }
}

// GOOD — Constructor Injection
public class OrderService
{
    private readonly IRepo _repo;
    public OrderService(IRepo repo) { _repo = repo; } // explicit
}
```

### Q17: How do you register open generic types?

**A:** Type argument ছাড়া `typeof` ব্যবহার করতে হবে।

```csharp
services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
// Now IRepository<User>, IRepository<Order> etc. all resolved automatically
```

---

## Entity Framework & SQL

### Q16: Tracking vs NoTracking?

**A:**
- **Tracking:** Entity-কে change tracker-এ রাখে।
- **NoTracking:** Read-only query-তে performance উন্নত করে।

### Q17: What is N+1 problem?

**A:** Loop-এর ভেতরে lazy loading-এর কারণে একাধিক অপ্রয়োজনীয় query execute হয়।

```csharp
foreach(var user in users)
{
    // Extra query per user → N queries
    var orders = await context.Orders
                  .Where(o => o.UserId == user.Id)
                  .ToListAsync();
}
```

### Q18: Clustered vs Non-clustered index?

**A:** Clustered index data-এর physical order নির্ধারণ করে। Non-clustered index data-এর pointer store করে।

### Q19: What is ACID?

**A:** Atomicity (সবকিছু হয় অথবা কিছুই হয় না), Consistency (data সর্বদা valid থাকে), Isolation (transaction গুলো একে অপরের থেকে আলাদা থাকে), Durability (commit হলে data permanent হয়)।

### Q20: Lazy Loading vs Eager Loading vs Explicit Loading?

**A:**
- **Lazy Loading:** Related data access করলে automatically load হয় (extra query হয়, ঝুঁকিপূর্ণ)।
- **Eager Loading:** `Include()` দিয়ে original query-তেই related data include করা হয়।
- **Explicit Loading:** `Entry().Collection().LoadAsync()` দিয়ে পরে manually load করা হয়।

```csharp
// Eager Loading
var users = await context.Users
    .Include(u => u.Orders)
    .ToListAsync();

// Lazy Loading (requires virtual navigation + proxy)
var user = await context.Users.FindAsync(1);
var count = user.Orders.Count; // triggers DB query

// Explicit Loading
var user = await context.Users.FindAsync(1);
await context.Entry(user).Collection(u => u.Orders).LoadAsync();
```

### Q21: What is a Migration in EF Core?

**A:** Migration code-এ schema পরিবর্তন track করে এবং database-এ incrementally apply করে।

```bash
dotnet ef migrations add AddOrderTable
dotnet ef database update
```

### Q22: What are SQL JOIN types?

**A:**
- `INNER JOIN` — শুধুমাত্র উভয় table-এ match আছে এমন row।
- `LEFT JOIN` — বাম table-এর সব row + ডান table-এ match পাওয়া row (match না থাকলে null)।
- `RIGHT JOIN` — ডান table-এর সব row + বাম table-এ match পাওয়া row।
- `FULL JOIN` — উভয় table-এর সব row, match না থাকলে null।

```sql
-- Get all users even if they have no orders
SELECT u.Name, o.Id
FROM Users u
LEFT JOIN Orders o ON u.Id = o.UserId
```

### Q23: What is a database transaction?

**A:** Operation-এর একটি group যেখানে সবগুলো succeed করবে অথবা সবগুলো fail করবে (ACID guarantee)।

```csharp
using var transaction = await context.Database.BeginTransactionAsync();
try
{
    context.Orders.Add(order);
    context.Stock.Update(stock);
    await context.SaveChangesAsync();
    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
}
```

---

## Algorithms & Data Structures

### Q20: What is Big-O notation?

**A:** Input বাড়ার সাথে সাথে time/space complexity কেমন পরিবর্তন হয় তা describe করে।

### Q21: Time complexity of Dictionary lookup?

**A:** গড়ে O(1)। সবচেয়ে খারাপ ক্ষেত্রে O(n)।

### Q22: How does HashTable work?

**A:** Hash function দিয়ে index calculate করে এবং collision chaining বা probing দিয়ে handle করে।

### Q23: Stack vs Queue?

**A:**
- **Stack:** LIFO (Last In First Out) — থালার স্তূপের মতো, শেষেরটা আগে বের হয়।
- **Queue:** FIFO (First In First Out) — লাইনের মতো, আগে আসলে আগে যাবে।

```csharp
var stack = new Stack<int>();
stack.Push(1); stack.Push(2);
Console.WriteLine(stack.Pop()); // 2 (last in, first out)

var queue = new Queue<int>();
queue.Enqueue(1); queue.Enqueue(2);
Console.WriteLine(queue.Dequeue()); // 1 (first in, first out)
```

### Q24: Binary Search — how does it work?

**A:** Sorted array-কে বারবার অর্ধেক ভাগ করে খোঁজা হয়। সময় জটিলতা O(log n)।

```csharp
int[] arr = { 1, 3, 5, 7, 9, 11 };
int target = 7, lo = 0, hi = arr.Length - 1;

while (lo <= hi)
{
    int mid = (lo + hi) / 2;
    if (arr[mid] == target) { Console.WriteLine(mid); break; }
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
}
// Output: 3 (index of 7)
```

### Q25: Array vs LinkedList?

**A:**
- **Array:** O(1) random access, মাঝখানে insert/delete করতে O(n)।
- **LinkedList:** Access করতে O(n), কিন্তু পরিচিত node-এ insert/delete O(1)।

```
Array:       [1][2][3][4]  ← index-based, contiguous memory
LinkedList:  1 → 2 → 3 → 4  ← each node points to next
```

---

## Design Patterns

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

## Common C# Tricky Questions

### Q31: What is the difference between `==` and `.Equals()` for strings?

**A:** String-এর জন্য উভয়ই value equality check করে, কারণ `==` overloaded। Object-এর জন্য `==` reference equality check করে, আর `.Equals()` override করা যায়।

```csharp
string a = "hello";
string b = "hello";
Console.WriteLine(a == b);       // True (value)
Console.WriteLine(a.Equals(b));  // True (value)

object x = new object();
object y = x;
Console.WriteLine(x == y);       // True (same ref)
```

### Q32: What is the `null` coalescing operator `??` and `??=`?

**A:** `??` বাম দিক null হলে ডান দিকের value return করে। `??=` শুধুমাত্র variable null হলে assign করে।

```csharp
string name = null;
string result = name ?? "Guest";    // "Guest"
name ??= "Default";                 // assigns "Default" to name
Console.WriteLine(name);            // Default
```

### Q33: What is `IDisposable` and the `using` statement?

**A:** `IDisposable` unmanaged resource মুক্ত করতে ব্যবহার হয়। `using` নিশ্চিত করে যে exception হলেও `Dispose()` call হবে।

```csharp
using (var conn = new SqlConnection(connectionString))
{
    conn.Open();
    // use connection
} // Dispose() called automatically here

// Modern C# shorthand
using var conn2 = new SqlConnection(connectionString);
// Dispose() called at end of scope
```

### Q34: What is the difference between `throw` and `throw ex`?

**A:** `throw` মূল stack trace সংরক্ষণ করে। `throw ex` stack trace reset করে — exception কোথায় শুরু হয়েছিল সেই তথ্য হারিয়ে যায়।

```csharp
try { SomeMethod(); }
catch (Exception ex)
{
    throw;     // GOOD — preserves stack trace
    // throw ex; // BAD — loses original trace
}
```

### Q35: What is `string` vs `StringBuilder`?

**A:** `string` immutable — প্রতিবার concatenation-এ নতুন object তৈরি হয়। `StringBuilder` একই buffer-এ পরিবর্তন করে এবং বহু concatenation-এ efficient।

```csharp
// Slow — creates many string objects
string s = "";
for (int i = 0; i < 1000; i++) s += i;

// Fast — single buffer
var sb = new StringBuilder();
for (int i = 0; i < 1000; i++) sb.Append(i);
string result = sb.ToString();
```

---

## Regex Cheatsheet

| চিহ্ন | অর্থ | উদাহরণ |
| --- | --- | --- |
| `.` | যেকোনো এক অক্ষর | `"a1!"` — `a.` → `a1` |
| `\d` | সংখ্যা (0-9) | `"123"` — `\d+` → `123` |
| `\D` | সংখ্যা নয় | `"abc123"` — `\D+` → `abc` |
| `\w` | অক্ষর/সংখ্যা/underscore | `"user_1"` — `\w+` → `user_1` |
| `\W` | অক্ষর/সংখ্যা নয় | `"@#!"` — `\W+` → `@#!` |
| `\s` | whitespace (space, tab) | `"a b"` — `\s` → ` ` |
| `\S` | non-whitespace | `"a b"` — `\S+` → `a`, `b` |
| `+` | ১ বা তার বেশি | `"aaa"` — `a+` → `aaa` |
| `*` | ০ বা তার বেশি | `"aaa"` — `a*` → `aaa` |
| `?` | ০ বা ১ বার | `"color"` — `colou?r` → `color` |
| `^` | শুরুতে মিল | `"Hello"` — `^H` → `H` |
| `$` | শেষে মিল | `"Hello"` — `o$` → `o` |
| `[]` | character class | `"bat"` — `[ab]` → `b`, `a` |
| `[^]` | negation | `"bat"` — `[^a]` → `b`, `t` |
| `()` | group/capture | `"abc"` — `(a)(b)(c)` → `a,b,c` |
| `\|` | OR | `"cat\|dog"` — `"I have a dog"` → `dog` |
| `{n}` | ঠিক n বার | `"aaa"` — `a{2}` → `aa` |
| `{n,}` | n বা তার বেশি | `"aaaa"` — `a{2,}` → `aaaa` |
| `{n,m}` | n থেকে m বার | `"aaaa"` — `a{2,3}` → `aaa` |
| `\b` | word boundary | `"cat dog"` — `\bcat\b` → `cat` |
| `\B` | non-word boundary | `"scat"` — `\Bcat` → `cat` |
| `\A` | string শুরু | `"Hello"` — `\AHe` → `He` |
| `\Z` | string শেষে | `"Hello"` — `lo\Z` → `lo` |

---

## Docker & Kubernetes

### Q1: Docker কী এবং কেন ব্যবহার করা হয়?

**A:** Docker একটি containerization platform যা application ও তার dependency একটি isolated container-এ package করে। এতে "আমার machine-এ কাজ করে কিন্তু তোমার machine-এ করে না" সমস্যা দূর হয়।

```bash
# Image build করো
docker build -t myapp:1.0 .

# Container চালাও
docker run -d -p 8080:80 myapp:1.0
```

### Q2: Container vs Virtual Machine (VM)?

**A:**
- **VM:** পুরো OS চালায়, ভারী, boot নিতে সময় লাগে।
- **Container:** Host OS-এর kernel শেয়ার করে, হালকা, milliseconds-এ start হয়।

```
VM:        [App] [Libs] [Guest OS] → Hypervisor → Host OS
Container: [App] [Libs] → Docker Engine → Host OS (shared kernel)
```

### Q3: Dockerfile-এর মূল instructions কী কী?

**A:**

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0   # base image
WORKDIR /app                                # working directory
COPY . .                                    # ফাইল copy করো
RUN dotnet build                            # build time কমান্ড
EXPOSE 80                                   # port expose করো
ENTRYPOINT ["dotnet", "MyApp.dll"]          # container start হলে এটি চলবে
```

### Q4: Docker Compose কী?

**A:** একাধিক container একসাথে define ও run করার tool। একটি `docker-compose.yml` file-এ সব service, network ও volume configure করা যায়।

```yaml
services:
  api:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
```

```bash
docker-compose up -d   # সব service start করো
docker-compose down    # সব বন্ধ করো
```

### Q5: Docker image vs Docker container?

**A:**
- **Image:** Read-only template — blueprint-এর মতো।
- **Container:** Image-এর running instance — image থেকে তৈরি live process।

```bash
docker images          # সব image দেখো
docker ps              # চলমান container দেখো
docker ps -a           # সব container (বন্ধও) দেখো
```

### Q6: Kubernetes (K8s) কী?

**A:** Kubernetes একটি container orchestration platform যা বহু container-এর deployment, scaling ও management automate করে।

> **সহজ কথায়:** Docker একটি container চালায়, Kubernetes হাজার হাজার container পরিচালনা করে।

### Q7: Kubernetes-এর মূল components কী কী?

**A:**
- **Pod:** সবচেয়ে ছোট unit — এক বা তার বেশি container ধারণ করে।
- **Deployment:** Pod-এর desired state manage করে, rollout ও rollback করে।
- **Service:** Pod-গুলোর সামনে stable network endpoint দেয়।
- **Ingress:** বাইরে থেকে HTTP/HTTPS traffic route করে।
- **ConfigMap / Secret:** Configuration ও sensitive data আলাদা রাখে।

```yaml
# Deployment উদাহরণ
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3          # ৩টি Pod চালাও
  selector:
    matchLabels:
      app: myapp
  template:
    spec:
      containers:
        - name: myapp
          image: myapp:1.0
          ports:
            - containerPort: 80
```

### Q8: Kubernetes-এ Scaling কীভাবে কাজ করে?

**A:** Manual বা automatic (HPA) উভয়ভাবে scale করা যায়।

```bash
# Manual scaling
kubectl scale deployment myapp --replicas=5

# HPA — CPU usage ৭০% ছাড়ালে auto scale করবে
kubectl autoscale deployment myapp --cpu-percent=70 --min=2 --max=10
```

### Q9: Rolling Update vs Recreate strategy?

**A:**
- **Rolling Update:** পুরানো Pod একটি একটি করে নতুন দিয়ে replace করে — **zero downtime**।
- **Recreate:** আগে সব পুরানো Pod বন্ধ করে, তারপর নতুন চালু করে — downtime হয়।

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1   # কতটি Pod একসাথে বন্ধ থাকতে পারবে
    maxSurge: 1         # কতটি extra Pod চলতে পারবে
```

### Q10: Liveness Probe vs Readiness Probe?

**A:**
- **Liveness Probe:** Container জীবিত আছে কিনা check করে। ব্যর্থ হলে restart করে।
- **Readiness Probe:** Container traffic নেওয়ার জন্য ready কিনা check করে। ব্যর্থ হলে Service থেকে সরিয়ে দেয় (restart করে না)।

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5

readinessProbe:
  httpGet:
    path: /ready
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 3
```

---

## Microservices

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


