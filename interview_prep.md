# Cefalo Bangladesh Interview Preparation

এই ডকুমেন্টে Cefalo Bangladesh এর ইন্টারভিউতে সাধারণত জিজ্ঞেস করা হয় এমন কিছু
প্রশ্ন ও সংক্ষিপ্ত উত্তর দেওয়া হয়েছে।

------------------------------------------------------------------------

# 1. OOP কী?

OOP (Object Oriented Programming) হলো একটি প্রোগ্রামিং প্যারাডাইম যেখানে
প্রোগ্রামকে Object দিয়ে তৈরি করা হয়।

OOP এর 4টি মূল নীতি: - Encapsulation - Inheritance - Polymorphism -
Abstraction

------------------------------------------------------------------------

# 2. Interface এবং Abstract Class এর পার্থক্য কী?

**Interface:** - শুধুমাত্র method signature থাকে - Multiple inheritance
support করে

**Abstract Class:** - Method implementation থাকতে পারে - Constructor
থাকতে পারে

------------------------------------------------------------------------

# 3. Async/Await কী?

Async/Await .NET এ asynchronous programming করার একটি সহজ উপায়।

এটি thread block না করে background এ কাজ করতে দেয়।

``` csharp
public async Task<string> GetData()
{
    return await httpClient.GetStringAsync(url);
}
```

------------------------------------------------------------------------

# 4. Thread কী?

Thread হলো program execution এর একটি ছোট unit।\
একটি process এর ভিতরে একাধিক thread থাকতে পারে।

------------------------------------------------------------------------

# 5. Task এবং Thread এর পার্থক্য কী?

**Thread** - OS level execution unit

**Task** - .NET abstraction - Thread pool ব্যবহার করে

------------------------------------------------------------------------

# 6. RESTful API কী?

RESTful API হলো একটি web service যা HTTP protocol ব্যবহার করে এবং REST
principles অনুসরণ করে।

সাধারণ HTTP methods: - GET - POST - PUT - DELETE - PATCH

------------------------------------------------------------------------

# 7. Dependency Injection কী?

Dependency Injection হলো একটি design pattern যেখানে object এর dependency
বাইরে থেকে inject করা হয়।

উপকারিতা: - Loose coupling - Easy testing - Maintainability

------------------------------------------------------------------------

# 8. DI Lifetime কী কী?

.NET এ তিন ধরনের lifetime আছে:

**Singleton**\
Application এ একবার তৈরি হয়

**Scoped**\
প্রতি request এ একবার তৈরি হয়

**Transient**\
প্রতি injection এ নতুন instance তৈরি হয়

------------------------------------------------------------------------

# 9. ASP.NET Core Middleware কী?

Middleware হলো request processing pipeline এর component।

প্রতি middleware request handle করে এবং পরের middleware এ pass করে।

``` csharp
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

------------------------------------------------------------------------

# 10. N+1 Problem কী?

Database query problem যেখানে:

1টি query parent data আনে\
তারপর প্রতিটি row এর জন্য আলাদা query চলে

ফলে performance খারাপ হয়।

------------------------------------------------------------------------

# 11. Composition Root কী?

Composition Root হলো application এর সেই জায়গা যেখানে সব dependency
configuration করা হয়।

সাধারণত: - Program.cs - Startup.cs

------------------------------------------------------------------------

# 12. SOLID Principles কী?

SOLID হলো 5টি design principle:

S - Single Responsibility\
O - Open Closed\
L - Liskov Substitution\
I - Interface Segregation\
D - Dependency Inversion

------------------------------------------------------------------------

# 13. Binary Search এর Time Complexity কী?

Time Complexity: **O(log n)**

------------------------------------------------------------------------

# 14. Polymorphism কী?

একই method বিভিন্নভাবে behave করতে পারে।

দুই ধরনের: - Compile time (method overloading) - Runtime (method
overriding)

------------------------------------------------------------------------

# 15. SQL এ duplicate row খুঁজে বের করার query

``` sql
SELECT name, COUNT(*)
FROM Users
GROUP BY name
HAVING COUNT(*) > 1;
```

------------------------------------------------------------------------

# 16. API RESTful হওয়ার জন্য কী প্রয়োজন?

-   Stateless
-   Resource based URL
-   HTTP methods ব্যবহার
-   Proper status codes

------------------------------------------------------------------------

# 17. Monolith এবং Microservice পার্থক্য

**Monolith** - সব feature এক application এ

**Microservice** - ছোট ছোট service এ ভাগ করা

------------------------------------------------------------------------

# 18. Production bug কিভাবে handle করবেন?

Steps:

1.  Log check
2.  Issue reproduce করা
3.  Root cause identify করা
4.  Fix implement করা
5.  Test করে deploy করা

------------------------------------------------------------------------

# 19. আপনার সবচেয়ে challenging problem কী ছিল?

এখানে নিজের real project experience explain করা উচিত:

-   Problem
-   Approach
-   Solution
-   Result

------------------------------------------------------------------------

# 20. কেন Cefalo join করতে চান?

সম্ভাব্য উত্তর:

-   International client experience
-   Strong engineering culture
-   Long term career growth
