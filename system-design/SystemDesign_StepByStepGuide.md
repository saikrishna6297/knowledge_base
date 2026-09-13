

# 📘 System Design Interview – Step-by-Step Guide

## 🚀 Overview

A structured approach to tackling system design interviews:

1. **Understand the problem & define scope**
2. **Propose a high-level design**
3. **Deep dive into the design**
4. **Wrap-up**

---

## 🧩 Step 1: Understand the Problem & Define Scope

> ⏱️ Target time: ~5 minutes

### Key Objectives:

* Ask as many clarifying questions as possible
* Identify the **target audience**
* Understand the **core features**
* Focus on **Non-Functional Requirements (NFRs)** such as:

   * Scalability
   * Availability
   * Performance

### Important Notes:

* Designing for **hundreds of users** is simple; designing for **millions** is challenging.
* As a **Senior Software Engineer**, you are expected to demonstrate your ability to handle scale and NFRs.
* Perform **rough estimations**:

   * Expected traffic
   * Storage needs
   * Bottlenecks

### Deliverables:

* ✅ List of core features
* ✅ NFRs (Scalability, Availability, Capacity)

---

## 🏗️ Step 2: Propose a High-Level Design

> ⏱️ Target time: ~20 minutes

### Approach:

* Use a **Top-Down Approach**
* Start with defining **APIs**

### API Design:

* Clearly define:

   * Inputs
   * Outputs (responses)
* Ensure APIs align with **functional requirements**
* Avoid unnecessary APIs (e.g., bookmarks, reviews unless required)

### Architecture Overview:

Typical flow:

```
Client → Load Balancer / API Gateway → Services → Data Storage
```

### Key Considerations:

* **WebSockets**:

   * Useful for real-time communication (In case, application requires two-way communication)
   * Hard to scale at large volumes

* Create a **High-Level Design Diagram**:

   * Acts as a blueprint
   * Ensures all requirements are covered

### Data Modeling:

* Understand:

   * Data access patterns
   * Read/Write ratio
* At scale, **data modeling significantly impacts performance**

### Notes:

* Avoid choosing specific databases too early
  → Finalize during **Step 3 (Deep Dive)**
* Identify discussion points:

   * Database scaling
   * High concurrency
   * Failure handling

---

## 🔍 Step 3: Design Deep Dive

> ⏱️ Target time: ~15 minutes

### Goals:

* Identify **bottlenecks and challenges**
* Propose solutions and discuss **trade-offs**

### How to Approach:

1. Clearly **articulate the problem**
2. Provide **at least 2 possible solutions**
3. Discuss **pros & cons**
4. Choose one solution and justify it
5. Iterate with interviewer feedback

---

### 🎯 Focus Areas:

* Security
* Consistency
* Data freshness
* Accuracy
* Performance
* Scalability

---

### 🧠 Example:

**Problem:**
Designing something like Google Maps with **1M QPS (queries per second)** to a location database.

**Possible Solutions:**

1. Reduce location update frequency
2. Use a scalable NoSQL database (e.g., DynamoDB)

**Trade-offs:**

* Lower frequency → Less load but stale data
* NoSQL → Scalable but may compromise consistency

---

## 🏁 Step 4: Wrap-Up

> ⏱️ Target time: ~5 minutes

### Final Steps:

* Summarize your design clearly
* Highlight:

   * Unique aspects
   * Key decisions
* Allow time for interviewer questions and discussion

---

## 📊 Additional Reference

**************** Add any necessary image here **********

---

## ✅ Pro Tips

* Always **communicate your thought process**
* Stay **structured and organized**
* Prioritize **clarity over complexity**
* Keep the discussion **interactive**

---

🔗 Reference Video: System Design Interview: A Step-By-Step Guide by ByteByteGo
[https://www.youtube.com/watch?v=i7twT3x5yv8](https://www.youtube.com/watch?v=i7twT3x5yv8)

---