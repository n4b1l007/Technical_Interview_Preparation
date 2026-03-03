# Common C# Tricky Questions

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

**[Next: 09_Regex_Cheatsheet.md →](09_Regex_Cheatsheet.md)**
