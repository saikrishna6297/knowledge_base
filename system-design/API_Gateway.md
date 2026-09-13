

# 🌐 API Gateway – Overview

An **API Gateway** is a **single entry point** for all client requests to an application.
It sits between the **client** and a collection of **backend services**, acting as a mediator that manages and routes requests.

<p align="center">
  <img src="assets/images/APIGateway/APIGateway_Architecture.png" width="40%">
</p>

---

## 📌 Why Use an API Gateway?

Instead of clients calling multiple services directly, the API Gateway:

* Simplifies client interaction
* Centralizes common logic
* Improves security and observability

---

## ⚙️ Core Responsibilities

An API Gateway typically provides the following features:

### 🔐 Security & Access Control

* Authentication (who you are)
* Authorization (what you can do)
* Policy enforcement

### ⚖️ Traffic Management

* Load balancing
* Rate limiting
* Circuit breaking (prevents cascading failures)

### 🔄 Request Handling

* Protocol translation (e.g., HTTP ↔ gRPC)
* Service discovery (finding backend services)
* Request/response transformation

### 📊 Observability

* Logging
* Monitoring
* Analytics
* Billing (in some platforms)

### ⚡ Performance

* Caching responses to reduce backend load

---

## 🔁 Request Flow

Below is a typical lifecycle of a client request:

<p align="center">
  <img src="assets/images/APIGateway/RequestFlow.png" width="40%">
</p>

1. **Client Request**

    * Client sends an HTTP request to the API Gateway

2. **Parameter Validation**

    * Ensures request structure, headers, and parameters are valid

3. **Allow/Deny List Check**

    * Verifies client IP or headers
    * May include basic rate limiting

4. **Authentication & Authorization**

    * Delegates to an Identity Provider (IdP)
    * Returns an authenticated session with defined permissions (scope)

5. **Rate Limiting**

    * Enforces limits based on authenticated identity
    * Rejects request if limits are exceeded

6. **Routing & Service Discovery**

    * Determines the correct backend service
    * Uses path or rules to locate the service

7. **Protocol Conversion**

    * Converts request to backend protocol (e.g., gRPC)
    * Sends request to service
    * Converts response back to client-friendly format (e.g., HTTP/JSON)

8. **Response to Client**

    * Final response is returned to the client

---

## 🛠️ Additional Capabilities

API Gateways often include:

* Error handling
* Circuit breakers
* Centralized logging
* Monitoring and analytics for operational visibility

---

## 🌍 Deployment Considerations

<p align="center">
  <img src="assets/images/APIGateway/GlobalDeployment.png" width="40%">
</p>

* API Gateway is a **critical infrastructure component**
* Should be deployed across **multiple regions** for high availability
* Many cloud providers deploy gateways **close to users globally** (edge locations) to reduce latency

---

## 🧠 Key Takeaway

An API Gateway acts as the **brain and gatekeeper** of your system—handling security, routing, and operational concerns so backend services can stay focused on business logic.

---
🔗 Reference Video: What is API Gateway?
[https://www.youtube.com/watch?v=6ULyxuHKxg8](https://www.youtube.com/watch?v=6ULyxuHKxg8)
