# Algorithms & Data Structures

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

**[Next: 07_Design_Patterns.md →](07_Design_Patterns.md)**
