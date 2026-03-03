# Entity Framework & SQL

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

**[Next: 06_Algorithms_Data_Structures.md →](06_Algorithms_Data_Structures.md)**
