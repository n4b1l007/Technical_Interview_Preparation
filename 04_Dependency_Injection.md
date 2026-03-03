# Dependency Injection

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

**[Next: 05_Entity_Framework_SQL.md →](05_Entity_Framework_SQL.md)**
