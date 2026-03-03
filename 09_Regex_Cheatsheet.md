# Regex Cheatsheet

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

**[Next: 10_Docker_Kubernetes.md →](10_Docker_Kubernetes.md)**
