Here’s a **clean, structured, README.md-friendly version** of your cache notes with added definitions and clarity.

---

# 📦 Caching — Notes & Concepts

## 🧠 What is Caching?

Caching is a technique used to **store frequently accessed data in a fast storage layer (usually memory)** to reduce latency, avoid repeated computation, and decrease load on backend systems like databases.

---

## 🎯 Why Use Cache?

1. **Save Network Calls**

   * Instead of repeatedly fetching data (e.g., user profile) from a database, store it as a key-value pair in cache.

2. **Avoid Repeated Computation**

   * Example: Calculating average age or aggregations repeatedly can be expensive.

3. **Reduce Database Load**

   * Multiple services hitting the DB can overload it; cache reduces direct DB access.

---

## ⏱️ When to Load & Evict Data?

This is governed by **Cache Eviction Policies**.

---

## 📜 Cache Eviction Policies

### 1. LRU (Least Recently Used)

* Removes the **least recently accessed** items first.
* Recently used items stay in cache.
* ✅ Most commonly used in real systems.

---

### 2. LFU (Least Frequently Used)

* Removes items that are accessed **least often**.
* ❗ Less common due to complexity in tracking frequency.

---

### 3. Sliding Window Policy

* Keeps data within a **time-based window**.
* Older data outside the window is evicted.

---

## ⚠️ Common Problems in Caching

### 1. Cache Miss Overhead

* Cache frequently returns **misses**, causing fallback to DB.
* Usually due to:

  * Poor eviction policy
  * Cache too small

---

### 2. Cache Thrashing

* Cache constantly evicts and replaces data.
* Example:

  * Cache stores profile X → request for Y → X evicted → Y stored → request for X again
* Happens when cache size is too small.

---

### 3. Data Consistency Issues

* Cache may hold **stale data** if DB is updated.
* Example:

  * Profile updated in DB but cache still has old value.

---

## 🗂️ Types of Caches

### 1. Local Cache (In-Memory)

* Stored within application/server memory.

#### ✅ Advantages

* Very fast (low latency)

#### ❌ Disadvantages

* Lost if server crashes
* Not shared across servers (inconsistent data)
* Limited by memory size
* Not suitable for sensitive data (e.g., financial info, passwords)

---

### 2. Global Cache (Distributed Cache)

* Shared across multiple servers.
* Example: Redis

#### ✅ Advantages

* Better consistency across services
* Scalable
* Centralized caching layer

---

## ✍️ Cache Write Policies

### 1. Write-Through Cache

**Definition:**
Data is written to cache and database **at the same time**.

#### ✅ Pros

* Cache always stays consistent with DB

#### ❌ Cons

* Higher write latency
* Multi-server inconsistency if cache isn’t shared

---

### 2. Write-Back Cache (Write-Behind)

**Definition:**
Data is written to cache first and **later persisted to DB asynchronously**.

#### ✅ Pros

* Faster writes (low latency)
* Reduced DB load

#### ❌ Cons

* Risk of data loss if cache fails before DB write
* Temporary inconsistency

---

## ⚖️ Optimization Strategy (Hybrid Approach)

To overcome write-back limitations:

* Write data to cache first
* Return response immediately
* Persist to DB **later in batches or intervals**

#### ⚠️ Trade-offs

* Temporary inconsistency is acceptable
* ❌ Not suitable for:

  * Financial data
  * Passwords
  * Critical transactions

---

## 🧩 Key Takeaways

* Use cache to **improve performance and scalability**
* Choose eviction policy carefully (LRU is most practical)
* Be aware of **consistency vs performance trade-offs**
* Use **distributed cache** like Redis for scalable systems
* Avoid caching **sensitive or critical data**

---
