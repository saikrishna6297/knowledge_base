# ⚖️ Load Balancing - Complete Guide

## 📌 Overview

Load balancing is a technique used to distribute incoming traffic across multiple servers to ensure high availability, scalability, and performance.

---

## 🚦 What is a Load Balancer?

A load balancer acts as a **traffic controller** between clients and backend servers.

It:

* Distributes requests efficiently
* Prevents server overload
* Improves system reliability

---

## 🧩 Architecture Diagram (Excalidraw Style)

```
          ┌──────────────┐
          │    Client    │
          └──────┬───────┘
                 │
                 ▼
        ┌──────────────────┐
        │  Load Balancer   │
        └──────┬──────┬────┘
               │      │
        ┌──────▼──┐ ┌─▼──────┐
        │ Server A│ │Server B│
        └─────────┘ └────────┘
               │
        ┌──────▼──────┐
        │  Server C   │
        └─────────────┘
```

👉 In Excalidraw:

* Use **rectangles** for components
* Use **arrows** for traffic flow
* Keep spacing loose and hand-drawn style

---

## 🧠 Load Balancing Algorithms (Visual)

### 🔁 Round Robin

* Requests distributed sequentially
* Best when all servers are equal

```
Request 1 → A
Request 2 → B
Request 3 → C
Request 4 → A
```

---

### 🔒 Sticky Sessions

* Same client → same server
* Useful for session-based applications

```
Alice → Server A
Alice → Server A
Bob   → Server B
```

---

### ⚖️ Weighted Round Robin

* Servers assigned weights based on capacity
* More powerful servers handle more traffic

```
Server A (80%) → ████████
Server B (10%) → █
Server C (10%) → █
```

---

### 🔑 IP Hash

* Requests routed using a hash function
* Ensures consistent routing

```
hash(IP1) → Server A
hash(IP2) → Server B
```

---

### 🔌 Least Connections

* Chooses server with fewest active connections
* Good for uneven workloads

```
A: 100 connections
B: 20 connections
C: 5 connections → selected
```

---

### ⏱️ Least Time

* Chooses server with the lowest latency
* Best for performance optimization

```
A: 120ms
B: 40ms
C: 10ms → selected
```

---

## 🏗️ Types of Load Balancers

### Hardware

* F5, Citrix ADC, Cisco
* High performance, expensive

### Software

* HAProxy, NGINX, Envoy
* Flexible, widely used

### Cloud

* AWS ELB, GCP Load Balancer, Azure
* Fully managed, auto-scaling

---

## 🌐 Layer 4 vs Layer 7 (Excalidraw Style)

### 🔸 Layer 4 (Transport Layer)

```
Client → Load Balancer → Server

Decision based on:
- IP Address
- Port
```

✔ Fast
✔ Low overhead
❌ No content awareness

---

### 🔸 Layer 7 (Application Layer)

```
Client → Load Balancer
           │
   ┌───────┴────────┐
   ▼                ▼
/api/users      /api/payments
   │                │
Server A        Server B
```

✔ Smart routing
✔ Content-aware
❌ Slightly slower

---

## ⚖️ When to Use What

### Use Layer 4 when:

* You need ultra-low latency
* Traffic is TCP/UDP (non-HTTP)
* Simple routing is enough

---

### Use Layer 7 when:

* You need API routing
* You use microservices
* You need authentication, caching, or headers

---

## 🌐 Layer 4 vs Layer 7 Load Balancers

This is one of the **most important concepts** in system design.

---

### 🔸 Layer 4 Load Balancer (Transport Layer)

**Works on:**

* IP address
* TCP/UDP ports

**Does NOT inspect:**

* HTTP content
* Headers or cookies

**Characteristics:**

* Very fast
* Low overhead
* Simple routing

👉 Example decision:

* “Send traffic to Server A based on IP + Port”

---

### ✅ When to Use Layer 4

* High-performance systems
* Simple routing requirements
* TCP/UDP services (e.g., databases, gaming servers)
* When latency must be minimal

---

### 🔸 Layer 7 Load Balancer (Application Layer)

**Works on:**

* HTTP/HTTPS data
* Headers, cookies, URLs

**Can inspect:**

* Request content
* API paths

**Characteristics:**

* Intelligent routing
* More flexible
* Slightly slower than Layer 4

👉 Example decision:

* `/api/users → Server A`
* `/api/payments → Server B`

---

### ✅ When to Use Layer 7

* Microservices architecture
* API gateways
* Content-based routing
* Authentication / session handling
* Caching / compression

---

## ⚖️ Layer 4 vs Layer 7 (Quick Comparison)

| Feature       | Layer 4        | Layer 7                 |
| ------------- | -------------- | ----------------------- |
| Speed         | Very Fast ⚡    | Slightly Slower         |
| Routing Logic | IP + Port      | Content-based           |
| Flexibility   | Low            | High                    |
| Use Case      | Infrastructure | Application-level logic |

---

## 🚀 Benefits of Load Balancing

* Eliminates single point of failure
* Improves reliability
* Handles traffic spikes
* Enables horizontal scaling

---

## ⚠️ Without Load Balancing

* Server overload
* Downtime
* Poor user experience
* Limited scalability

---

## ☁️ Real-World Mapping (AWS Example)

* **Layer 4 → Network Load Balancer (NLB)**
* **Layer 7 → Application Load Balancer (ALB)**

---

## 🚀 Advanced Architecture (Excalidraw Style)

```
                ┌──────────────┐
                │   Clients    │
                └──────┬───────┘
                       │
              ┌────────▼────────┐
              │ Global LB (DNS) │
              └────────┬────────┘
                       │
        ┌──────────────┴──────────────┐
        ▼                             ▼
 ┌──────────────┐             ┌──────────────┐
 │ Region US LB │             │ Region EU LB │
 └──────┬───────┘             └──────┬───────┘
        │                             │
   ┌────▼────┐                  ┌────▼────┐
   │Servers  │                  │Servers  │
   └─────────┘                  └─────────┘
```

---

## 🧠 Key Takeaways

* Load balancing is essential for modern distributed systems
* 6 core algorithms define traffic distribution
* Layer 4 = speed, Layer 7 = intelligence
* Cloud load balancers simplify scaling
* Always design for failure

---

## OSI Model (7 Layers)

The OSI (Open Systems Interconnection) model is a conceptual framework used to understand how data moves through a network. It consists of 7 layers, from physical transmission up to user-facing applications.


| Layer | Name               | Description                                                                  | Examples (Real World)                                  | Common Protocols / Tech            |
|------:|--------------------|------------------------------------------------------------------------------|--------------------------------------------------------|------------------------------------|
| 7     | Application Layer  | Provides network services to end-user applications                           | Browser requesting a webpage, API calls                | HTTP, HTTPS, FTP, DNS              |
| 6     | Presentation Layer | Formats data, handles encryption/decryption and encoding                     | SSL/TLS encryption, JSON/XML formatting                | TLS/SSL, JPEG, MP3                 |
| 5     | Session Layer      | Manages sessions/connections between systems                                 | Keeping a user logged in, session handling             | NetBIOS, RPC                       |
| 4     | Transport Layer    | Ensures reliable data transfer, flow control, error handling                 | TCP connection for web, UDP for streaming              | TCP, UDP                           |
| 3     | Network Layer      | Determines routing and logical addressing                                    | Routers forwarding packets using IP addresses          | IP, ICMP, IPsec                    |
| 2     | Data Link Layer    | Handles data framing and physical addressing (MAC)                           | Ethernet frames, switching in LAN                      | Ethernet, ARP, PPP                 |
| 1     | Physical Layer     | Transmits raw bits over physical medium                                      | Cables, Wi-Fi signals, fiber optics                    | Ethernet (physical), USB, DSL      |

---

🔗 Reference Video: https://www.youtube.com/watch?v=LQuuoHTyYz8 by ByteByteGo