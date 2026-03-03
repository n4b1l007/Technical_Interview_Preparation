# C# & .NET Core

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

**[Next: 02_REST_API_Web_API.md →](02_REST_API_Web_API.md)**
