# Kubernetes (K8s) Commands List 
```
#YAML File Commands
* `kubectl apply -f app.yaml` → Create/update resources from YAML
* `kubectl create -f app.yaml` → Create resource from YAML
* `kubectl delete -f app.yaml` → Delete resource from YAML
* `kubectl replace -f app.yaml` → Replace existing resource
* `kubectl diff -f app.yaml` → Compare changes before apply

# Daily use commanda
* `kubectl get pods -A`
* `kubectl logs -f <pod>`
* `kubectl describe pod <pod>`
* `kubectl exec -it <pod> -- bash`
* `kubectl rollout restart deployment <name>`
* `kubectl apply -f deployment.yaml`

# Shortcuts 
* `po` → pods
* `deploy` → deployments
* `svc` → services
* `ns` → namespaces
* `rs` → ReplicaSets
```
  
## Cluster Commands

* `kubectl cluster-info` → Display cluster information
* `kubectl version` → Check kubectl and cluster version
* `kubectl get nodes` → List all worker/master nodes
* `kubectl describe node <node-name>` → Detailed node information
* `kubectl top nodes` → Show node CPU and memory usage
---

## Namespace Commands
| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl get namespace` | List namespaces | `kubectl get namespace` |
| `kubectl create namespace` | Create namespace | `kubectl create namespace my-ns` |
| `kubectl delete namespace` | Delete namespace | `kubectl delete namespace my-ns` |
| `kubectl config set-context` | Set default namespace | `kubectl config set-context --current --namespace=my-ns` |
### Namespace Resource Commands
* `kubectl get all` → Show all resources in namespace
* `kubectl get all -A` → Show all resources in all namespaces
---

## Pod Commands
```bash
# List pods
kubectl get pods # List pods in current namespace
kubectl get pods -n my-namespace # List pods particular namespaces
kubectl get pods -A  # List pods all namespaces

# Get detailed info
kubectl describe pod my-pod # Pod details. my-pod is a pod name
kubectl get pod my-pod -o yaml # YAML output
kubectl get pods -o wide # Pod details with node/IP or Detailed pod info

# View logs
kubectl logs my-pod #View pod logs
kubectl logs -f my-pod  #  Live logs streaming / Follow logs
kubectl logs my-pod --tail=50  # Last 50 lines
kubectl logs my-pod -p  # Previous pod logs

# Execute/Run command in pod
kubectl exec -it my-pod -- bash or /bin/bash
kubectl exec my-pod -- ls -la

# Port forward to pod
kubectl port-forward my-pod 8080:8080

kubectl top pod → Show pod resource usage

# Create pod from YAML
kubectl apply -f pod.yaml # Create / Update Pod
kubectl Create -f pod.yaml # only form creating pod

# Edit pod
kubectl edit pod my-pod # Edit pod config

# Delete pod
kubectl delete pod my-pod
kubectl delete pod -l app=myapp  # Delete by label
```

---

## Deployment Commands
```bash
# Apply deployment
kubectl apply -f deployment.yaml # Create / update depolyment
kubectl create deployment my-app --image=myimage:1.0 #Create deployment CLI

# List deployments
kubectl get deployments
kubectl get deploy -o wide

# Update image. kubectl set image
kubectl set image deployment/my-deploy app=myimage:2.0

# Describe deployment
kubectl describe deployment my-app

# Scale replicas / deployment: kubectl scale
kubectl scale deployment my-app --replicas=5

# Check rollout status
kubectl rollout status deployment/my-app

# View rollout history
kubectl rollout history deployment/my-app

# Rollback to previous version
kubectl rollout undo deployment/my-app

# Rollback to specific revision
kubectl rollout undo deployment/my-app --to-revision=2

#Delete deployment
kubectl delete deployment my-deploy

```
### ReplicaSet Commands

* `kubectl get rs` → List ReplicaSets
* `kubectl describe rs <rs-name>` → ReplicaSet details
* `kubectl delete rs <rs-name>` → Delete ReplicaSet
---

## Service Commands
```bash
# List services
kubectl get svc or kubectl get service
kubectl get svc -A  # All namespaces

# Describe service
kubectl describe svc my-service

# Expose deployment: `kubectl expose`
kubectl expose deployment my-app --port=80 --target-port=8080 --type=ClusterIP

# Create service from YAML
kubectl apply -f service.yaml

# Port forward to service
kubectl port-forward svc/my-service 8080:80

# Delete service
kubectl delete svc my-service
```
---
## **5. Node Commands**
```bash
# List nodes
kubectl get nodes
kubectl get nodes -o wide # Detailed node info

# Node details
kubectl describe node node-1

# Resource usage
kubectl top nodes #  Node resource usage
kubectl top pods # Pod resource usage

# Cordon node (prevent new pods)
kubectl cordon node-1

# Uncordon node (allow scheduling again)
kubectl uncordon node-1

# Drain node (move pods to other nodes)
kubectl drain node-1 --ignore-daemonsets

# Label a node
kubectl label nodes node-1 disk=ssd
```
## ConfigMap & Secret Commands

* `kubectl get configmap` → List ConfigMaps
* `kubectl create configmap app-config --from-literal=env=prod` → Create ConfigMap
* `kubectl get secrets` → List secrets
* `kubectl create secret generic db-secret --from-literal=password=1234` → Create secret
## Persistent Volume Commands

* `kubectl get pv` → List Persistent Volumes
* `kubectl get pvc` → List Persistent Volume Claims
* `kubectl describe pvc <pvc-name>` → PVC details

---
## **KUBERNETES NETWORK COMMANDS**

### **1. Service Network Commands**

```bash
# List all services
kubectl get services
kubectl get svc -A  # All namespaces
kubectl get svc -o wide

# Service details
kubectl describe svc my-service

# Get service endpoints
kubectl get endpoints my-service

# Port forward to service
kubectl port-forward svc/my-service 8080:80

# Check DNS name resolution
kubectl exec -it my-pod -- nslookup my-service
kubectl exec -it my-pod -- nslookup my-service.default.svc.cluster.local
```

### **Service DNS Resolution**

```
Service DNS format:
<service-name>.<namespace>.svc.cluster.local

Examples:
my-service.default.svc.cluster.local
postgres.database.svc.cluster.local
nginx.kube-system.svc.cluster.local
```

---

### **2. NetworkPolicy Commands**
```bash
# List network policies
kubectl get networkpolicy
kubectl get netpol -A  # Short version of networkpolicy is netpol -A means all

# Describe policy / Policy details
kubectl describe netpol deny-all

# Apply / create policy
kubectl apply -f network-policy.yaml

# Delete policy
kubectl delete netpol deny-all
```

---

### **3. Ingress Commands**

```bash
# List ingress
kubectl get ingress
kubectl get ingress -A # All namespaces

# Describe ingress / Ingress details
kubectl describe ingress my-ingress

# Apply / Create ingress
kubectl apply -f ingress.yaml

# Delete ingress
kubectl delete ingress my-ingress

# View ingress endpoints
kubectl get endpoints
```

---

### **4. DNS and Service Discovery**

```bash
# Check DNS resolution inside pod
kubectl exec -it my-pod -- nslookup kubernetes.default
kubectl exec -it my-pod -- nslookup my-service
kubectl exec -it my-pod -- nslookup my-service.default.svc.cluster.local

# Using dig for DNS queries
kubectl exec -it my-pod -- apt-get update && apt-get install -y dnsutils
kubectl exec -it my-pod -- dig my-service
kubectl exec -it my-pod -- dig my-service.default.svc.cluster.local

# Check CoreDNS pod logs
kubectl logs -n kube-system -l k8s-app=kube-dns
kubectl logs -n kube-system -l k8s-app=coredns
```

### **DNS Service Names**

```
Pod DNS:
<pod-ip>.<namespace>.pod.cluster.local

Service DNS:
<service-name>.<namespace>.svc.cluster.local

Examples:
my-pod.default.pod.cluster.local
web-service.default.svc.cluster.local
db-service.production.svc.cluster.local
```

---

### **5. Network Debugging Commands**

```bash
# Check pod network connectivity
kubectl run -it --rm debug --image=busybox --restart=Never -- sh

# Test service connectivity
kubectl exec -it my-pod -- ping web-service
kubectl exec -it my-pod -- curl http://web-service:80

# Check service endpoints
kubectl get endpoints
kubectl describe endpoints my-service

# View network policies for namespace
kubectl get netpol -n my-namespace

# Check node network settings
kubectl describe node node-1

# View cluster network info
kubectl cluster-info
kubectl get svc -n kube-system
```
---
```
## Describe & Debug Commands
* `kubectl describe pod <pod-name>` → Detailed troubleshooting info
* `kubectl events` → View cluster events
* `kubectl get events --sort-by=.metadata.creationTimestamp` → Events in time order

## Resource Monitoring Commands
* `kubectl top nodes` → Node CPU/memory usage
* `kubectl top pods` → Pod CPU/memory usage

## Context & Config Commands
* `kubectl config view` → View kubeconfig
* `kubectl config get-contexts` → List contexts
* `kubectl config use-context <context-name>` → Switch cluster context

## DaemonSet Commands
* `kubectl get daemonset` → List DaemonSets
* `kubectl describe daemonset <name>` → DaemonSet details

## StatefulSet Commands
* `kubectl get statefulset` → List StatefulSets
* `kubectl describe statefulset <name>` → StatefulSet details

## Job & CronJob Commands
* `kubectl get jobs` → List jobs
* `kubectl get cronjobs` → List cronjobs
* `kubectl create job test --image=busybox` → Create job
```
# Easy Flow to Remember

👉 Cluster → Namespace → Pod → Deployment → Service → Logs → Debugging → Scaling → Rollback

## **KUBERNETES COMMANDS**

### **1. Basic kubectl Commands**

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl version` | Check kubectl/cluster/API version | `kubectl version` |
| `kubectl cluster-info` | Cluster information | `kubectl cluster-info` |
| `kubectl config view` | View kubeconfig | `kubectl config view` |
| `kubectl config use-context` | Switch context | `kubectl config use-context my-cluster` |
| `kubectl api-resources` | Available resources | `kubectl api-resources` |
| `kubectl explain` | Resource documentation | `kubectl explain pods` |

---
Deploment.yaml
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app-container
        image: myimage:1.0
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```
### **Service Types**

```yaml
# ClusterIP (default, internal only)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080

---
# NodePort (accessible on node IP)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080

---
# LoadBalancer (external load balancer)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

### **NetworkPolicy Examples**

```yaml
# Deny all ingress traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress

---
# Allow traffic from specific namespace
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-ns
spec:
  podSelector:
    matchLabels:
      role: db
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          project: myproject
    ports:
    - protocol: TCP
      port: 5432

---
# Allow traffic from specific pod
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-pod
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080

---
# Deny all egress (outbound) traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress

---
# Allow specific ports and protocols
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-specific
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: app-tier
    ports:
    - protocol: TCP
      port: 8080
  - from:
    - namespaceSelector:
        matchLabels:
          name: monitoring
    ports:
    - protocol: TCP
      port: 9090
```
### **Ingress Examples**

```yaml
# Simple HTTP Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80

---
# Multiple hosts and paths
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: complex-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /v1
        pathType: Prefix
        backend:
          service:
            name: api-v1
            port:
              number: 8080
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: api-v2
            port:
              number: 8080
  - host: web.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80

---
# HTTPS Ingress with TLS
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - example.com
    secretName: tls-secret
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```
