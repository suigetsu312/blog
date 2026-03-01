---
date: 2026-03-01T23:08:29+08:00
draft: false
title: "C++ STL Cheatsheet"
categories: ["dev"]
tags: ["C++", "STL"]
---

# C++ STL Cheatsheet

STL (Standard Template Library) provides generic containers and algorithms for C++ development.
This note summarizes commonly used containers, typical usage patterns, and pitfalls.

---

## Headers (common)

```cpp
#include <vector>
#include <array>
#include <string>
#include <deque>
#include <list>
#include <forward_list>

#include <queue>
#include <stack>

#include <set>
#include <unordered_set>
#include <map>
#include <unordered_map>

#include <algorithm>
#include <utility>
#include <tuple>
#include <bitset>
```

---

## Vector

Dynamic array with contiguous memory.

```cpp
vector<int> v = {1,2,3};
v.push_back(4);
v.emplace_back(5);

int x = v[0];
v.pop_back();
```

**Notes**

* Random access: O(1)
* Insert/erase in middle: O(n)
* `reserve(n)` reduces reallocations
* **Iterator invalidation**: reallocation invalidates all iterators/pointers/references

---

## Array

Fixed-size array (stack-friendly, no dynamic allocation).

```cpp
array<int, 3> a = {1,2,3};
a[0] = 10;
```

**Notes**

* Size is compile-time constant
* Useful when size is known and small

---

## String

`std::string` behaves like a dynamic array of `char`.

```cpp
string s = "abc";
s.push_back('d');
s += "ef";
```

**Notes**

* `s.size()` is O(1)
* Be careful with repeated concatenation inside loops (consider `reserve`)

---

## Deque

Double-ended queue; random access supported, efficient push/pop at both ends.

```cpp
deque<int> dq;
dq.push_back(1);
dq.push_front(0);
dq.pop_back();
dq.pop_front();
```

**Notes**

* Random access: O(1) (not contiguous)
* Often used as the underlying container for `queue` / `stack`

---

## List

Doubly linked list.

```cpp
list<int> lst;
lst.push_back(1);
lst.push_front(0);
```

**Notes**

* Insert/erase with iterator: O(1)
* No random access
* High overhead; rarely needed unless frequent splicing/erase in middle

---

## Forward List

Singly linked list.

```cpp
forward_list<int> fl;
fl.push_front(1);
fl.insert_after(fl.before_begin(), 0);
```

**Notes**

* Lower overhead than `list`, but only forward iteration
* Also less commonly used

---

## Queue

FIFO adapter (default underlying container: `deque`).

```cpp
queue<int> q;
q.push(1);
int front = q.front();
q.pop();
```

---

## Stack

LIFO adapter (default underlying container: `deque`).

```cpp
stack<int> st;
st.push(1);
int t = st.top();
st.pop();
```

---

## Priority Queue (Heap)

Default is max heap.

### Max heap

```cpp
priority_queue<int> pq;
pq.push(3);
pq.push(1);
pq.push(2);
int mx = pq.top(); // 3
pq.pop();
```

### Min heap

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

### Heap of pairs (common)

```cpp
priority_queue<
    pair<int,int>,
    vector<pair<int,int>>,
    greater<pair<int,int>>
> pq; // (freq, value) -> smallest freq on top
```

**Notes**

* `push/pop`: O(log n), `top`: O(1)
* Not iterable (no begin/end)
* Comparator semantics: `priority_queue` keeps “largest” by comparator; `greater<>` makes it a min heap

---

## Set / Multiset

Ordered tree-based containers.

```cpp
set<int> s;
s.insert(3);
s.insert(3); // ignored
```

```cpp
multiset<int> ms;
ms.insert(3);
ms.insert(3); // allowed
```

**Notes**

* Operations: O(log n)
* Sorted order maintained
* Supports `lower_bound` / `upper_bound`

---

## Unordered Set

Hash-based unique container.

```cpp
unordered_set<int> us;
us.insert(3);
if (us.count(3)) {}
```

**Notes**

* Average O(1), worst-case O(n)
* No ordering guarantee
* Use `reserve` if you know size to reduce rehashing:

```cpp
us.reserve(100000);
```

---

## Map / Multimap

Ordered key-value containers (tree).

```cpp
map<int,int> mp;
mp[1] = 10;
mp[2] = 20;
```

```cpp
multimap<int,int> mmp;
mmp.insert({1,10});
mmp.insert({1,20}); // duplicate keys allowed
```

**Notes**

* Operations: O(log n)
* `mp[key]` default-constructs value if key not present (can be a pitfall)

---

## Unordered Map

Hash-based key-value container.

```cpp
unordered_map<int,int> mp;
mp[1]++;                 // frequency counting
if (mp.find(1) != mp.end()) {}
```

**Notes**

* Average O(1), worst-case O(n)
* Prefer `find` when you don’t want implicit insertion

---

## Pair / Tuple

### pair

```cpp
pair<int,int> p = {1, 2};
auto [a, b] = p; // structured binding
```

### tuple

```cpp
tuple<int, string, double> t = {1, "x", 3.14};
auto [i, s, d] = t;
```

---

## Bitset

Fixed-size bit operations (fast, memory efficient).

```cpp
bitset<64> b;
b.set(3);
b.test(3);
b.flip();
```

---

## Algorithms (high frequency)

### sort + lambda

```cpp
sort(v.begin(), v.end(), [](const auto& a, const auto& b){
    return a.second > b.second;
});
```

### lower_bound / upper_bound (on sorted range)

```cpp
auto it = lower_bound(v.begin(), v.end(), x);
```

### accumulate

```cpp
#include <numeric>
int sum = accumulate(v.begin(), v.end(), 0);
```

---

## Pitfalls & Notes (worth remembering)

1. **Iterator invalidation**

   * `vector`: reallocation invalidates all iterators/pointers/references
   * `deque`: push/pop at ends may invalidate iterators (rules are trickier)
   * `list`: iterators stay valid except erased element

2. **unordered_* rehash**

   * rehash can invalidate iterators; `reserve` helps

3. **map operator[]**

   * inserts default value when key missing; use `find` if you only want lookup

4. **erase while iterating**

   * associative containers:

```cpp
for (auto it = s.begin(); it != s.end(); ) {
    if (*it % 2 == 0) it = s.erase(it);
    else ++it;
}
```

---

## Quick Complexity Summary (common)

* `vector` random access: O(1), push_back amortized O(1), insert middle O(n)
* `deque` push/pop ends: O(1), random access O(1)
* `set/map` operations: O(log n)
* `unordered_set/unordered_map` average O(1)
* `priority_queue` push/pop O(log n), top O(1)

---

# When to Choose Which Container

Choosing the correct container is more important than memorizing APIs.
Below are practical guidelines for real-world usage.

---

### Use `vector` when:

* You need fast random access (O(1))
* You iterate frequently
* You care about cache locality
* Insertions are mostly at the end

> Default choice in most situations.

---

### Use `deque` when:

* You need efficient push/pop at both ends
* You still need random access

> Good for sliding window or double-ended buffering.

---

### Use `list` when:

* You need frequent insert/erase in the middle
* Iterator stability is important

> Rarely necessary in performance-critical code.

---

### Use `unordered_map` / `unordered_set` when:

* You need fast lookup (average O(1))
* Order does not matter
* You are doing frequency counting or membership checks

> Default choice for hash-based lookup.

---

### Use `map` / `set` when:

* You need ordered traversal
* You need `lower_bound` / `upper_bound`
* You require predictable O(log n)

> Prefer when ordering matters.

---

### Use `priority_queue` when:

* You repeatedly need the largest or smallest element
* You need to maintain top-k elements
* Full sorting is unnecessary

> Useful when partial ordering is sufficient.

---

### Use `array` when:

* Size is fixed and known at compile time
* You want stack allocation

---

### Use `bitset` when:

* You need compact bit manipulation
* The size is fixed and small

---

# Practical Advice

* Prefer `vector` unless you have a strong reason not to.
* Prefer `unordered_*` over ordered containers unless ordering is required.
* Avoid premature optimization; choose based on access pattern.
* Always consider iterator invalidation rules.
