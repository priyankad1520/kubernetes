## Kubernetes Cluster Architecture

kubectl apply -f deployment.yaml

Behind the scenes:
1.	kubectl → API Server 
2.	API Server → stores in etcd 
3.	Controller → reads from etcd 
4.	Scheduler → assigns node 
5.	Kubelet → creates Pod

Kubernetes has two main parts:
**1. Control Plane (Master Node)** → Brain of the cluster. The Control Plane manages the entire Kubernetes cluster. It makes decisions like scheduling, scaling, and healing.

**2. Data Plane / Worker Nodes** → Where applications actually run

Think of it like a restaurant 🍽️
-	Control Plane → Restaurant manager and system controlling everything
-	Worker Nodes → Kitchen where food is cooked

#### Control Plane Components
These components manage the cluster.

**1. API Server:** API Server is the entry point of Kubernetes. Every request goes through it.
- Examples: kubectl create, kubectl delete, kubectl get pods
- Real-World Example: Restaurant example: Customer → Waiter → Kitchen
- Here: Customer = User  Waiter = API Server  Kitchen = Worker Nodes
- The waiter takes your order and sends it to the kitchen.
- The API Server is the central component of the Kubernetes control plane. It exposes the Kubernetes API and acts as the communication hub between users, the control plane, and worker nodes.

**2. Scheduler:** Scheduler decides which worker node should run the pod.
- It checks: CPU, Memory, Resource availability
- Real-World Example: Manager decides which chef should cook the dish based on availability.
- The Kubernetes Scheduler is responsible for selecting the best worker node to run a pod based on available resources and scheduling policies.

**3. etcd:** etcd is the database of Kubernetes. etcd stores the entire Kubernetes cluster state as key-value pairs, where the values are full JSON objects
It stores:
-	Pods: Pod name, Namespace, Labels, Container image, Ports, Environment variables, Status (Running / Pending / Failed), Node where it is scheduled, IP address 
-	Nodes: Node name, IP address, CPU & memory capacity, Labels (e.g., zone=us-east-1), Status (Ready / NotReady
-	Deployment info: etcd stores the desired state. Number of replicas, Pod template, Strategy
-	Configurations: application settings
-	ConfigMap: is used to store non-sensitive in plan text formate and it is public setting configuration like URLs, ports, and environment variables.  Application mode → dev, test, prod,  Database host → mysql-service,  Port numbers → 8080, Feature flags → ENABLE_CACHE=true, Config files → .properties, .yaml
-	Secrets: Secret is used to store sensitive inbase64 encoded (not fully secure unless encryption enabled) formate and it is private settings data like Database password, API keys, OAuth tokens, TLS certificates, Private keys
-	In Amazon EKS: When you create a cluster, AWS automatically manages etcd (control plane), You don’t directly store data in etcd  Instead: You create Kubernetes objects (ConfigMap, Secret) You never interact with etcd directly. You use kubectl or YAML files. These are automatically stored in etcd by the API server. 
-	We should not store Kubernetes Secret YAML files with actual values in GitHub because they are only base64 encoded and can be easily decoded. Instead, we use secure solutions like AWS Secrets Manager or Parameter Store and integrate them with Kubernetes using External Secrets Operator. Another option is Sealed Secrets, which encrypts secrets before storing them in Git.

Both are stored in etcd but Secrets are handled more securely. ConfigMap and Secret are Kubernetes resources stored inside the cluster, specifically in etcd. They are not part of Pods directly, but Pods can consume them as environment variables or volumes.
App needs both: ConfigMap → tells how to connect and Secret → gives credentials to connect

-	Cluster state: Internal Kubernetes settings like: Service accounts, RBAC roles & permissions, Namespaces, API objects, Network policies
- Real-World Example: There is a register system that stores all orders and records.
- If this system crashes → restaurant cannot track orders.
- etcd is a distributed key-value store used by Kubernetes to store all cluster data and configuration information.

**4. Controller Manager:** Controller Manager ensures desired state = actual state.Desired State → What YOU want
- Actual State → What is CURRENTLY running
- Example: You want 3 pods running.
- If one pod crashes → controller creates a new pod.
- The Controller Manager runs various controllers that ensure the cluster maintains the desired state, such as maintaining the required number of pod replicas.

Real-World Example: Restaurant supervisor ensures:
-	3 chefs must be working.
-	If one chef leaves → hire another.

**5. Cloud Controller Manager (Optional):** Used when Kubernetes runs on cloud platforms.
- Examples: AWS, Azure and Google Cloud
- It connects Kubernetes with cloud services like: Load balancers, Storage and Networking
- The Cloud Controller Manager integrates Kubernetes with cloud provider APIs and manages resources such as load balancers, storage, and node management.

#### Worker Node Components (Data Plane): Worker nodes run the applications.

**1. Kubelet:** Kubelet is the agent running on each worker node.
- It Communicates with API Server Starts containers Monitors pods
- If pod fails → reports to control plane.
- Real-World Ex: In the restaurant kitchen: Kubelet is like the chef who actually cooks the food.
- Kubelet is an agent that runs on every worker node and ensures that containers described in Pod specifications are running correctly.

**2. Container Runtime:** Container runtime actually runs the containers.
- Examples: containerd and CRI-O
- Older Kubernetes used Docker.
- Real-World Example: In the kitchen: Container runtime is the stove where food is cooked.
- The Container Runtime is responsible for running containers inside pods and provides the execution environment for applications.

**3. Kube-Proxy:** Kube-proxy handles network communication between pods.
- It: Routes traffic, Provides load balancing and Uses iptables or IPVS
- Real-World Example: Restaurant waiter who delivers food to the correct table.
- Kube-proxy manages networking on worker nodes and handles service discovery and load balancing for pods.
---
#### Complete Flow 
When you run: kubectl apply -f deployment.yaml

Step-by-step:
1. Request goes to API Server
2. API Server stores data in etcd
3. Scheduler chooses best worker node
4. Kubelet creates the pod
5. Container runtime runs the container
6. Kube-proxy manages networking
7. Controller Manager ensures pods keep running
---
```
Kubernetes architecture consists of two main parts: the Control Plane and Worker Nodes.
The Control Plane includes components like API Server, Scheduler, etcd, and Controller Manager, which manage the cluster.
Worker nodes run the applications and contain components like kubelet, container runtime, and kube-proxy.
The Control Plane schedules and manages pods, while worker nodes execute and run the containers.
 ```
Real-World Example (Food Delivery Company): Imagine Swiggy or Zomato system.

Example: Control Plane
- API Server:	App where user places order
- Scheduler:	Decides which restaurant prepares order
- etcd:	Stores order information
- Controller Manager:	Ensures orders are completed

When you run: kubectl apply -f deployment.yaml

Flow:
1. Request goes to API Server
2. Data stored in etcd
3. Scheduler selects worker node
4. Kubelet creates pod
5. Container Runtime runs container
6. Kube Proxy manages network traffic
7. Controller Manager ensures pods keep running

Pod deleted automatically — how Kubernetes knows to recreate it?

> “Because the desired state is stored in etcd via Deployment. The controller continuously watches etcd, and if a Pod is missing, it recreates it to match the desired state.”


