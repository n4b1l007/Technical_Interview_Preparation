# Async Programming

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

**[Next: 04_Dependency_Injection.md →](04_Dependency_Injection.md)**
