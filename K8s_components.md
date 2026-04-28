1. Pod: "A Pod is the smallest deployable unit in Kubernetes that runs containers with shared networking and storage, while Deployments and ReplicaSets manage scaling, updates, and auto-healing."
-	Pod is the smallest deployable unit in Kubernetes that runs one or more containers sharing network and storage. 
-	We don’t create Pods directly in production → we use Deployment / ReplicaSet for auto-healing, scaling, and updates. 
-	Containers are not created directly by Kubernetes → they run inside Pods because Pods provide networking, storage, and lifecycle management. 
-	Pod provides shared network → all containers use same IP and communicate via localhost. 
-	Pod provides shared storage → containers can share data using volumes. 
-	Pod supports multi-container pattern → app container + logging/monitoring sidecars. 
-	Scaling is done via Deployment/ReplicaSet → multiple identical Pods are created. 
-	ReplicaSet maintains desired state → uses replicas field to ensure required number of Pods always run. 
-	Auto-healing → if a Pod fails, ReplicaSet creates a new Pod automatically. 
-	restartPolicy: Always → container inside Pod is restarted if it crashes. 
-	Pods simplify management → Kubernetes handles networking, storage, scaling, and lifecycle at Pod level. 
-	In my project, we use Kubernetes to deploy and manage our application.
-	We have a Kubernetes cluster which consists of a Control Plane and multiple Worker Nodes. The Worker Nodes are responsible for running the application workloads.
-	Our application runs inside Pods, which are the smallest deployable units in Kubernetes. Each Pod contains one or more containers that share the same network and storage.
-	We do not create Pods directly. Instead, we use a Deployment. The Deployment automatically creates a ReplicaSet, and the ReplicaSet ensures that the required number of Pods are always running.
-	For example, if we define replicas as 3, Kubernetes ensures that 3 Pods are always running. If any Pod fails, the ReplicaSet automatically creates a new Pod, which provides auto-healing.
-	We use Deployments because they support scaling, rolling updates, and rollback. When we update the application version, Kubernetes performs a rolling update without downtime.
-	To interact with the cluster, we use kubectl. It communicates with the Kubernetes API Server to create, update, or monitor resources. For example, we use commands like kubectl apply to deploy applications and kubectl get pods to check the status.
-	Overall, Kubernetes helps us manage applications efficiently by providing scalability, high availability, and automated recovery.

2. Node: Node is a physical or virtual machine in a Kubernetes cluster that runs Pods and containers. 
- Why we use Node: To provide compute resources (CPU, memory) for running applications. 
- How we use Node: Kubernetes schedules Pods onto Nodes automatically.  Worker Nodes run Pods, Control Plane manages them. 
- What kind of problem it solves: Provides infrastructure to run containerized applications.  Enables distributed execution across multiple machines. 

3. Cluster: Cluster is a group of Nodes managed by Kubernetes to run applications.  It consists of Control Plane (Master) + Worker Nodes. 
- Why we use Cluster: To manage and run applications across multiple machines. 
- How we use Cluster: Control Plane manages cluster state, Worker Nodes run Pods. 
- What kind of problem it solves: Provides scalability and high availability. Centralized management of distributed systems. 

4. Kubectl: kubectl is a CLI tool used to interact with Kubernetes API Server to manage cluster resources. 
- Why we use kubectl: To create, view, update, and delete Kubernetes resources. 
- How we use kubectl: User → kubectl → API Server → Cluster → Nodes → Pods  Example: kubectl get pods, kubectl apply -f file.yaml 
- What kind of problem it solves: Provides easy command-line access to control the cluster.  Simplifies management of Kubernetes resources. 

5. ReplicaSet: ReplicaSet ensures a specified number of Pods are always running. 
- Why we use ReplicaSet: To maintain desired number of Pods and provide auto-healing. 
- How we use ReplicaSet: Define replicas in YAML → ReplicaSet creates/deletes Pods accordingly. 
- What kind of problem it solves: Prevents downtime by recreating failed Pods.  Maintains application availability. 

6. Deployment: Deployment manages ReplicaSets and Pods to run applications. 
- Why we use Deployment: To enable scaling, rolling updates, and rollback, auto healing. Easy Application Management: Deployment provides a single object to manage the entire application lifecycle. 
- How we use Deployment: Create Deployment → Kubernetes creates ReplicaSet → Pods → Containers. Update image/version → rolling update happens automatically. 
- What kind of problem it solves: Enables zero-downtime updates and easy scaling. Provides version control and rollback for applications. 

> "A Kubernetes cluster consists of nodes that run Pods, kubectl is used to manage resources, ReplicaSet maintains desired Pods, and Deployment manages scaling, updates, and rollback of applications."

In my project, we use Kubernetes to deploy and manage our application.

- We have a Kubernetes cluster which consists of a Control Plane and multiple Worker Nodes. The Worker Nodes are responsible for running the application workloads.
- Our application runs inside Pods, which are the smallest deployable units in Kubernetes. Each Pod contains one or more containers that share the same network and storage.
- We do not create Pods directly. Instead, we use a Deployment. The Deployment automatically creates a ReplicaSet, and the ReplicaSet ensures that the required number of Pods are always running.
- For example, if we define replicas as 3, Kubernetes ensures that 3 Pods are always running. If any Pod fails, the ReplicaSet automatically creates a new Pod, which provides auto-healing.
- We use Deployments because they support scaling, rolling updates, and rollback. When we update the application version, Kubernetes performs a rolling update without downtime.- To interact with the cluster, we use kubectl. It communicates with the Kubernetes API Server to create, update, or monitor resources. For example, we use commands like kubectl apply to deploy applications and kubectl get pods to check the status.
- Overall, Kubernetes helps us manage applications efficiently by providing scalability, high availability, and automated recovery.
