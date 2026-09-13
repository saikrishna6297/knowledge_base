Here’s a clean, **README.md-friendly version** of your content—kept simple, structured, and easy to follow (with placeholders for your images as requested).

---

# Consistent Hashing

What do Amazon DynamoDB, Apache Cassandra, Discord, and Akamai have in common?
👉 They all use **Consistent Hashing**

---

## 📌 What is Consistent Hashing?

In large-scale distributed systems, data is not stored on a single machine. Instead, it is distributed across multiple servers — this is called **horizontal scaling**.

To maintain predictable performance, data must be distributed **evenly** across servers.

---

## ⚙️ Simple Hashing (Baseline Approach)

A common way to distribute data:

```
serverIndex = hash(key) % N
```

* `key` → object identifier
* `N` → number of servers

---

## 🧪 Example

### Setup

* Number of servers: **N = 4**
* Servers: `S0, S1, S2, S3`
* Hash function (for illustration):
  `hash(key) = sum of ASCII values`

---

### Keys

* `"apple"`
* `"banana"`
* `"grape"`
* `"orange"`

---

### Step-by-step

**apple**

* Hash = 530 → `530 % 4 = 2` → S2

**banana**

* Hash = 609 → `609 % 4 = 1` → S1

**grape**

* Hash = 527 → `527 % 4 = 3` → S3

**orange**

* Hash = 636 → `636 % 4 = 0` → S0

---

### Final Distribution

| Key    | Hash | Server |
|--------|------|--------|
| apple  | 530  | S2     |
| banana | 609  | S1     |
| grape  | 527  | S3     |
| orange | 636  | S0     |

---

## 🚨 Problem: Rehashing

When the number of servers changes, **almost all keys get reassigned**.

### ➕ Adding a server (N = 5)

👉 Most keys move to different servers

### ➖ Removing a server

👉 Again, most keys get remapped

---

### ❗ Why this is bad

* Massive data movement
* Cache invalidation
* Increased network traffic
* Performance issues

---

## ✅ Solution: Consistent Hashing

Consistent hashing minimizes data movement when servers change.

👉 **Only a small subset of keys are reassigned**

---

## 🔄 How Consistent Hashing Works

* Hash both:

    * **Servers**
    * **Keys**
* Use the same hash function
* Map them onto a **hash space**

---

### 🔵 Hash Space & Ring

* Hash range: `0 → N`
* Ends are connected → forms a **Hash Ring**

---

### 🖼️ Visuals

# Image showing s0…s4 placed on Hash Ring

  <p align="center">
  <img src="assets/images/ConsistentHashing/CH_Servers.png" width="40%">
  </p>

# Image showing k0…k4 placed on Hash Ring

  <p align="center">
  <img src="assets/images/ConsistentHashing/CH_ObjectKeys.png" width="40%">
  </p>

# Image showing how keys are re-assigned when a new server is added

  <p align="center">
  <img src="assets/images/ConsistentHashing/CH_NewServerAdded.png" width="40%">
  </p>

# Image showing how keys are re-assigned when a new server is removed

  <p align="center">
  <img src="assets/images/ConsistentHashing/CH_ServerRemoved.png" width="40%">
  </p>

---

## 📍 Key Mapping Rule

👉 A key is assigned to the **first server clockwise** on the ring

* Key0 → Server0
* Key1 → Server1
* ...

---

## 🎯 Key Insight

Unlike modulo hashing:

* No dependency on total number of servers (`N`)
* Minimal reshuffling when servers change

---

## ⚠️ Problem: Uneven Distribution

Sometimes servers are not evenly spaced on the ring:

👉 Some servers get more data than others

---

### 🖼️ Visual

# Image showing uneven distribution of objects on the ring

<p align="center">
  <img src="assets/images/ConsistentHashing/CH_UnevenDistributedKeys.png" width="40%">
</p>

---

## 🧩 Solution: Virtual Nodes (VNodes)

Instead of placing each server once:

👉 Place each server **multiple times** on the ring

---

### ✅ Benefits

* Better load balancing
* More even distribution
* Reduced hotspots

---

### 🖼️ Visual

# Image showing how virtual nodes resolve the problem

<p align="center">
  <img src="assets/images/ConsistentHashing/CH_virtualNodes.png" width="40%">
</p>

---

## ⚖️ Trade-off

* More virtual nodes → better balance
* But → more metadata to manage

👉 Choose based on system needs

---

## 🌍 Real-world Use Cases

### 🗄️ Databases

* Amazon DynamoDB
* Apache Cassandra
  👉 Used for **data partitioning** and minimizing rebalancing cost

---

### 🌐 Content Delivery Networks

* Akamai
  👉 Distributes content across edge servers

---

### ⚖️ Load Balancing

* (e.g., Google Load Balancers)
  👉 Distributes persistent connections efficiently

---

## 🧠 Summary

| Approach           | Behavior              |
|--------------------|-----------------------|
| Simple Hashing     | Reassigns most keys ❌ |
| Consistent Hashing | Moves minimal keys ✅  |

---

👉 Consistent hashing ensures:

* Scalability
* Stability
* Efficient data distribution

---

🔗 Reference Video: Consistent Hashing | Algorithms You Should Know #1
[https://www.youtube.com/watch?v=UF9Iqmg94tk](https://www.youtube.com/watch?v=UF9Iqmg94tk)