# Kubernetes — Key Concepts, Architecture, and Building Blocks

## What is Kubernetes?

Kubernetes (often abbreviated as **K8s**) is an open-source platform for **container orchestration**. It automates deployment, scaling, networking, and management of containerized applications.

Originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), Kubernetes is widely used to run applications reliably across clusters of machines.

---

# 1. Core Kubernetes Concepts

## Cluster
A **Kubernetes Cluster** is a group of machines (physical or virtual) running Kubernetes.

It contains:

- **Control Plane (Master Nodes)** → manages the cluster
- **Worker Nodes** → run application workloads

---

## Node
A **Node** is a machine in the cluster.

### Types:
- **Control Plane Node** → scheduling, orchestration, API management
- **Worker Node** → executes containers

### Key node components:
- **kubelet** → agent communicating with the control plane
- **container runtime** → runs containers (e.g., containerd)
- **kube-proxy** → networking and service routing

---

## Pod (Smallest Deployable Unit)
A **Pod** is the smallest deployable object in Kubernetes.

A pod:
- Contains one or more tightly coupled containers
- Shares:
  - Network namespace
  - Storage volumes
  - IP address

### Example:
- Web server container + logging sidecar container

Pods are **ephemeral** (temporary). Kubernetes recreates failed pods automatically.

---

# 2. Workload Management

## Deployment
A **Deployment** manages stateless applications.

### Responsibilities:
- Creates and manages pods
- Handles rolling updates
- Supports rollback
- Maintains desired replica count

### Example:
```yaml
replicas: 3
```

Ensures 3 pod instances are always running.

---

## ReplicaSet
A **ReplicaSet** ensures a specified number of pod replicas remain running.

Usually managed indirectly through Deployments.

---

## StatefulSet
Used for **stateful applications** like:
- Databases
- Kafka
- Elasticsearch

### Provides:
- Stable network identity
- Persistent storage
- Ordered deployment/startup

---

## DaemonSet
Runs one pod on every node.

### Common uses:
- Monitoring agents
- Log collectors
- Security agents

### Example:
- Fluentd log collector on all nodes

---

## Job & CronJob

### Job
Runs a task until completion.

#### Examples:
- Database migration
- Batch processing

### CronJob
Runs jobs on a schedule.

#### Example:
```bash
0 0 * * *
```

Runs daily at midnight.

---

# 3. Networking Concepts

## Service
A **Service** provides stable network access to pods.

### Why needed:
- Pods are temporary and IPs change

### Types:
- **ClusterIP** → internal communication
- **NodePort** → exposes service on node port
- **LoadBalancer** → cloud external load balancer
- **ExternalName** → maps to external DNS

---

## Ingress
An **Ingress** manages HTTP/HTTPS external access.

### Features:
- URL routing
- SSL termination
- Virtual hosting

Usually implemented using an **Ingress Controller** such as:
- NGINX Ingress Controller
- Traefik

---

## DNS
Kubernetes includes internal DNS for service discovery.

### Example:
```bash
my-service.default.svc.cluster.local
```

---

# 4. Storage Concepts

## Volume
A **Volume** provides persistent/shared storage to containers in a pod.

---

## Persistent Volume (PV)
Cluster storage resource provisioned by admins/cloud providers.

---

## Persistent Volume Claim (PVC)
A request for storage by applications.

### Example:
```yaml
storage: 10Gi
```

---

## Storage Classes
Defines dynamic storage provisioning policies.

### Examples:
- SSD storage
- Standard HDD
- Cloud block storage

---

# 5. Configuration & Security

## ConfigMap
Stores non-sensitive configuration data.

### Examples:
- Environment variables
- Application configs

---

## Secret
Stores sensitive data:
- Passwords
- API keys
- TLS certificates

Secrets are base64-encoded and can integrate with external secret managers.

---

## Namespace
Provides logical isolation within a cluster.

### Used for:
- Teams
- Environments
- Projects

### Examples:
- dev
- staging
- production

---

## RBAC (Role-Based Access Control)
Controls permissions in Kubernetes.

### Key objects:
- Role
- ClusterRole
- RoleBinding

Defines:
- Who can access what resources
- Allowed operations

---

# 6. Kubernetes Control Plane Architecture

## API Server
Central entry point for all Kubernetes operations.

All communication goes through the API server.

---

## etcd
Distributed key-value database storing cluster state.

Critical for:
- Configuration
- Metadata
- Desired state

---

## Scheduler
Assigns pods to nodes based on:
- CPU/memory availability
- Affinity rules
- Constraints

---

## Controller Manager
Runs controllers that continuously reconcile desired vs actual state.

### Examples:
- Deployment controller
- Node controller

---

# 7. Kubernetes Desired State Model

Kubernetes follows a **declarative model**.

Users define:

```yaml
desired replicas = 3
```

Kubernetes continuously works to maintain that state automatically.

This is called the **reconciliation loop**.

---

# 8. Scaling & Self-Healing

## Horizontal Pod Autoscaler (HPA)
Automatically scales pods based on:
- CPU
- Memory
- Custom metrics

---

## Self-Healing Features
Kubernetes automatically:
- Restarts failed containers
- Replaces failed pods
- Reschedules workloads
- Performs health checks

### Health probes:
- Liveness probe
- Readiness probe
- Startup probe

---

# 9. Observability & Operations

## Logging
Applications should write logs to stdout/stderr.

### Common stack:
- Fluentd
- Elasticsearch
- Kibana

---

## Monitoring
Popular tools:
- Prometheus
- Grafana

### Metrics include:
- CPU
- Memory
- Request latency
- Pod health

---

# 10. Kubernetes Ecosystem

| Tool | Purpose |
|---|---|
| Helm | Package management |
| Istio | Service mesh |
| Argo CD | GitOps deployment |
| Terraform | Infrastructure as code |

---

# 11. Kubernetes Advantages

## Benefits
- High availability
- Automatic scaling
- Self-healing
- Efficient resource utilization
- Portability across clouds
- Declarative infrastructure
- Strong ecosystem/community

---

# 12. Common Kubernetes Challenges

## Challenges
- Steep learning curve
- Networking complexity
- Security hardening
- Cost management
- Observability at scale
- Stateful workload management

---

# Kubernetes in One Sentence

Kubernetes is a declarative container orchestration platform that automates deployment, scaling, networking, storage, and lifecycle management of applications across distributed infrastructure.
````
