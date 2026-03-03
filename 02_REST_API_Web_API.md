# REST API / Web API

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

**[Next: 03_Async_Programming.md →](03_Async_Programming.md)**
