# Kubernetes Deployment Strategies — Blue-Green & Canary Deployments

## Topics Covered

1. Blue-Green Deployment
2. Canary Deployment

---

# Blue-Green Deployment

Blue-Green Deployment is a release strategy where two identical environments are maintained:

- **Blue** → Current live version
- **Green** → New version

Traffic is switched from Blue to Green after validation.

## Benefits

- Near zero downtime
- Easy rollback
- Safe production testing
- Reduced deployment risk

---

# Blue Deployment

## Create Blue Deployment YAML

```bash
kubectl create deploy myapp-blue \
  --image=nginx \
  --replicas=2 \
  --port=80 \
  --dry-run=client -o yaml \
  -- sh -c "echo 'Blue Version is running' > /usr/share/nginx/html/index.html; nginx -g 'daemon off;'" > blue.yaml
```

---

## Update Labels in `blue.yaml`

```yaml
selector:
  matchLabels:
    app: myapp
    version: blue

template:
  metadata:
    labels:
      app: myapp
      version: blue
```

---

## Blue Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-blue

spec:
  replicas: 2

  selector:
    matchLabels:
      app: my-app
      version: blue

  template:
    metadata:
      labels:
        app: my-app
        version: blue

    spec:
      containers:
      - name: my-app
        image: nginx:1.25

        command:
        - /bin/sh
        - -c

        args:
        - |
          echo "BLUE version is running" > /usr/share/nginx/html/index.html;
          nginx -g "daemon off;"

        ports:
        - containerPort: 80
```

---

# Green Deployment

## Create Green Deployment YAML

```bash
kubectl create deploy my-app-green \
  --image=nginx:1.26 \
  --replicas=2 \
  --dry-run=client -o yaml > green.yaml
```

---

## Update Labels in `green.yaml`

```yaml
selector:
  matchLabels:
    app: my-app
    version: green

template:
  metadata:
    labels:
      app: my-app
      version: green
```

---

## Green Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-green

spec:
  replicas: 2

  selector:
    matchLabels:
      app: my-app
      version: green

  template:
    metadata:
      labels:
        app: my-app
        version: green

    spec:
      containers:
      - name: my-app
        image: nginx:1.26

        command:
        - /bin/sh
        - -c

        args:
        - |
          echo "GREEN version is running" > /usr/share/nginx/html/index.html;
          nginx -g "daemon off;"

        ports:
        - containerPort: 80
```

---

# Service Configuration

## Create NodePort Service

```bash
kubectl expose deploy my-app-blue \
  --name=my-app-service \
  --type=NodePort \
  --port=80
```

---

## Service YAML

Update the selector version to switch traffic:

- `version: blue` → Routes traffic to Blue deployment
- `version: green` → Routes traffic to Green deployment

```yaml
apiVersion: v1
kind: Service

metadata:
  name: my-app-service

spec:
  type: NodePort

  selector:
    app: my-app
    version: blue

  ports:
  - port: 80
    targetPort: 80
    nodePort: 32198
```

---

# Switching Traffic

## Check Current Service

```bash
kubectl get svc my-app-service -o yaml
```

---

## Edit Service

```bash
kubectl edit svc my-app-service
```

Change:

```yaml
version: blue
```

to:

```yaml
version: green
```

Traffic instantly switches to Green deployment.

---

# Testing Blue-Green Deployment

## Get Node IP

```bash
kubectl get nodes -o wide
```

Example:

```text
NAME           INTERNAL-IP
controlplane   172.30.1.2
node01         172.30.2.2
```

---

## Test Using Curl

```bash
curl http://172.30.2.2:32198
```

### Output

```text
GREEN version is running
```

Or use:

```bash
curl localhost:32198
```

---

# Important Notes

- Blue and Green environments run simultaneously
- Service selector controls traffic routing
- Rollback is immediate by changing selector back
- Can use:
  - ClusterIP
  - NodePort
  - LoadBalancer
  - Ingress

---

# Canary Deployment

Canary Deployment gradually shifts traffic to a new version.

Instead of switching all traffic immediately:

- Small percentage → New version (v2)
- Remaining traffic → Stable version (v1)

This reduces deployment risk.

---

# Benefits of Canary Deployment

- Safer releases
- Incremental rollout
- Easier monitoring
- Reduced blast radius
- Better production testing

---

# Canary Architecture

```text
Users
   |
Service
   |
   +---- app-v1 Pods
   |
   +---- app-v2 Pods
```

Kubernetes Service distributes traffic across matching pods.

Traffic percentage depends on replica count.

Example:

| Version | Replicas | Approx Traffic |
|---|---|---|
| v1 | 4 | 80% |
| v2 | 1 | 20% |

---

# Deployment: app-v1

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: app-v1
  labels:
    app: app-v1

spec:
  replicas: 1

  selector:
    matchLabels:
      app: myapp
      version: v1

  template:
    metadata:
      labels:
        app: myapp
        version: v1

    spec:
      containers:
      - name: http-echo
        image: hashicorp/http-echo

        args:
        - "-text=Hello from V1"

        ports:
        - containerPort: 5678
```

---

# Deployment: app-v2

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: app-v2
  labels:
    app: app-v2

spec:
  replicas: 1

  selector:
    matchLabels:
      app: myapp
      version: v2

  template:
    metadata:
      labels:
        app: myapp
        version: v2

    spec:
      containers:
      - name: http-echo
        image: hashicorp/http-echo

        args:
        - "-text=Hello from V2"

        ports:
        - containerPort: 5678
```

---

# Canary Service

The Service selects all pods with:

```yaml
app: myapp
```

This includes both:

- v1 pods
- v2 pods

---

## Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: myapp-service

spec:
  type: ClusterIP

  selector:
    app: myapp

  ports:
  - port: 80
    targetPort: 5678
```

---

# Testing Canary Deployment

## Method 1 — Multiple Temporary Pods

```bash
for i in {1..20}; do
  kubectl run tmp \
    --rm -it \
    --restart=Never \
    --image=curlimages/curl \
    -- curl -s http://myapp-service
done
```

---

## Method 2 — Single Temporary Pod (Recommended)

```bash
kubectl run curl-test \
  --image=curlimages/curl \
  --rm -it \
  --restart=Never \
  -- sh -c '
    for i in $(seq 1 20); do
      curl -s http://myapp-service
      echo
    done
  '
```

---

# Expected Output

```text
Hello from V1
Hello from V2
Hello from V1
Hello from V1
Hello from V2
```

Traffic is distributed across both versions.

---

# Blue-Green vs Canary Deployment

| Feature | Blue-Green | Canary |
|---|---|---|
| Traffic Shift | Instant | Gradual |
| Rollback | Immediate | Gradual rollback |
| Risk Level | Medium | Low |
| Complexity | Simple | Moderate |
| User Exposure | Entire traffic | Partial traffic |
| Testing Style | Full environment switch | Incremental validation |

---

# Key Takeaways

## Blue-Green Deployment
- Two identical environments
- Instant traffic switch
- Fast rollback
- Simple implementation

## Canary Deployment
- Gradual traffic rollout
- Safer production testing
- Reduced deployment risk
- Better observability

---

# Final Notes

- Kubernetes Services route traffic using labels/selectors
- Deployments manage pod lifecycle and scaling
- Canary percentages are usually controlled using:
  - Replica ratios
  - Service Mesh (Istio, Linkerd)
  - Ingress controllers
- Production-grade canary deployments often use:
  - Metrics
  - Automated rollback
  - Progressive delivery tools

---

# Second Part

# Kubernetes Administration Notes

## Topics Covered

1. Kubeconfig
2. Admission Controllers
3. API Versions & Deprecations

---

# Kubeconfig

## Default Kubeconfig Location

The default kubeconfig file is located at:

```bash
/root/.kube/config
```

You can verify the home directory using:

```bash
echo $HOME
```

---

# Switch Context to Access Cluster

Requirement:

- User: `dev-user`
- Cluster: `test-cluster-1`

Use the correct context from the kubeconfig file.

## Set Current Context

```bash
kubectl config --kubeconfig=/root/my-kube-config use-context research
```

---

## Verify Current Context

```bash
kubectl config --kubeconfig=/root/my-kube-config current-context
```

Expected output:

```text
research
```

---

# Make Custom Kubeconfig Persistent

Avoid specifying `--kubeconfig` in every command.

## Edit `.bashrc`

```bash
vi ~/.bashrc
```

Add:

```bash
export KUBECONFIG=/root/my-kube-config
```

---

## Reload Configuration

```bash
source ~/.bashrc
```

---

## Verify

```bash
echo $KUBECONFIG
```

Expected:

```text
/root/my-kube-config
```

---

# Troubleshooting Cluster Access

With context set to `research`, cluster access fails.

## Error

```bash
kubectl get pods
```

Output:

```text
error: unable to read client-cert /etc/kubernetes/pki/users/dev-user/developer-user.crt
```

Problem:
- Incorrect certificate filename in kubeconfig

---

# Solution

## Check Existing User Configuration

```bash
kubectl config view --kubeconfig=/root/my-kube-config | grep -A5 "name: dev-user"
```

---

## Verify Actual Certificate Files

```bash
ls -l /etc/kubernetes/pki/users/dev-user/
```

Expected files:

```text
dev-user.crt
dev-user.key
```

---

## Update Credentials

```bash
kubectl config set-credentials dev-user \
  --client-certificate=/etc/kubernetes/pki/users/dev-user/dev-user.crt \
  --client-key=/etc/kubernetes/pki/users/dev-user/dev-user.key \
  --kubeconfig=/root/my-kube-config
```

---

## Test Cluster Access

```bash
kubectl get pods
```

Expected:

```text
No resources found in default namespace.
```

---

# Admission Controllers

## Enable ImagePolicyWebhook Admission Plugin

Requirement:

- Enable `ImagePolicyWebhook`
- Configure admission controller config file
- Mount required configuration directory

---

# Backup Existing API Server Manifest

```bash
cp /etc/kubernetes/manifests/kube-apiserver.yaml \
   /opt/kube-apiserver.yaml.bak
```

---

# Edit API Server Manifest

```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

---

## Update Admission Plugins

Add:

```yaml
- --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
- --admission-control-config-file=/etc/kubernetes/imgvalidation/admission-configuration.yaml
```

---

# Add Volume Definition

```yaml
- name: imgvalidation
  hostPath:
    path: /etc/kubernetes/imgvalidation
    type: Directory
```

---

# Add Volume Mount

```yaml
- name: imgvalidation
  mountPath: /etc/kubernetes/imgvalidation
  readOnly: true
```

---

# Verify API Server Restart

```bash
kubectl get pods -n kube-system
```

The kube-apiserver pod should restart automatically.

---

# API Versions & Deprecations

## Enable `v1alpha1` RBAC API Version

Requirement:

Enable:

```text
rbac.authorization.k8s.io/v1alpha1
```

---

# Backup API Server Manifest

```bash
cp -v /etc/kubernetes/manifests/kube-apiserver.yaml \
      /root/kube-apiserver.yaml.backup
```

---

# Edit API Server Manifest

```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

---

# Add Runtime Config Flag

Under the `command` section:

```yaml
- --runtime-config=rbac.authorization.k8s.io/v1alpha1
```

Example:

```yaml
- command:
  - kube-apiserver
  - --advertise-address=10.18.17.8
  - --allow-privileged=true
  - --authorization-mode=Node,RBAC
  - --client-ca-file=/etc/kubernetes/pki/ca.crt
  - --enable-admission-plugins=NodeRestriction
  - --enable-bootstrap-token-auth=true
  - --runtime-config=rbac.authorization.k8s.io/v1alpha1
```

---

# Verify API Server Status

```bash
kubectl get pods -n kube-system
```

Ensure the kube-apiserver pod is in `Running` state.

---

# Install `kubectl-convert` Plugin

Used to convert deprecated Kubernetes API versions.

---

# Download Plugin

```bash
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert
```

---

# Verify Download

```bash
ls
```

Expected:

```text
kubectl-convert
```

---

# Make Executable

```bash
chmod +x kubectl-convert
```

---

# Move to System PATH

```bash
mv kubectl-convert /usr/local/bin/kubectl-convert
```

---

# Verify Installation

```bash
kubectl-convert --help
```

If help output appears, installation is successful.

---

# Convert Deprecated Ingress API Version

Existing file:

```text
/root/ingress-old.yaml
```

Requirement:
- Convert deprecated API version
- Use `networking.k8s.io/v1`

---

# Convert Manifest

```bash
kubectl-convert \
  -f ingress-old.yaml \
  --output-version networking.k8s.io/v1 \
  > ingress-new.yaml
```

---

# Create Resource

```bash
kubectl create -f ingress-new.yaml
```

Expected:

```text
ingress.networking.k8s.io/ingress-space created
```

---

# Verify API Version

```bash
kubectl get ingress ingress-space -o yaml | grep apiVersion
```

Expected:

```text
apiVersion: networking.k8s.io/v1
```

---

# Key Takeaways

## Kubeconfig
- Stores cluster, user, and context information
- `KUBECONFIG` environment variable sets default config
- Contexts simplify multi-cluster access

## Admission Controllers
- Intercept API requests before persistence
- Enforce validation, security, and governance
- Configured in kube-apiserver manifest

## API Deprecations
- Kubernetes removes deprecated APIs over time
- `kubectl-convert` helps migrate manifests
- Runtime APIs can be enabled using `--runtime-config`

---

# Useful Commands Summary

| Purpose | Command |
|---|---|
| Show current context | `kubectl config current-context` |
| Switch context | `kubectl config use-context <name>` |
| View kubeconfig | `kubectl config view` |
| Reload bashrc | `source ~/.bashrc` |
| Check system pods | `kubectl get pods -n kube-system` |
| Convert API versions | `kubectl-convert -f file.yaml` |

---
````
