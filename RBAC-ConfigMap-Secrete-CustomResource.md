# RBAC (Role-Based Access Control)
* It is a security mechanism used to control who can access what resources in a system based on their roles.
* RBAC is widely used in systems like Kubernetes, databases, operating systems, and enterprise applications.
* Imagine a company office system: Role, Permissions, Admin Create users, delete users, manage system

### Real-Time Example
* Admin → Create users, delete users, manage system
* Developer → Deploy applications
* Viewer → Only view reports

---

### RBAC in Kubernetes

* RBAC controls who can access cluster resources.
* Resources include: Pods, Services, Deployments, Nodes, ConfigMaps
* RBAC decides:Who can create, Who can view, Who can delete, Who can update

## RBAC Components

### 1. Role
* Defines permissions inside a single namespace
* Example permissions: Get pods, List pods and Create pods
* Example:
  * Role: pod-reader
  * Permission: Read pods
  * Namespace: default

### 2. ClusterRole
* Defines permissions for the entire cluster and it's including all namespaces.
* Not limited to one namespace
* Example permissions: View/read all nodes, Manage all namespaces, Access all pods

### 3. RoleBinding
* Connects a user to a Role / RoleBinding links the user and the role, but it does not combine them into one object.
* A RoleBinding is used to assign a Role to a user, group, or service account within a namespace. 
* Assigns Role to user/group/service account within a namespace
* Example:
  * User → Priyanka
  * Role → pod-reader
  * Result → Priyanka can read pods

### 4. ClusterRoleBinding
* Connects a user to a ClusterRole
* ClusterRole alone does not give access
* Access is granted only when attached using binding
* Example:
  * User → Admin
  * ClusterRole → cluster-admin
  * Result → Admin controls whole cluster

## Namespace in Kubernetes
* Namespace is a way to organize and isolate Kubernetes resources inside a cluster.
* A Namespace in Kubernetes is used to logically divide a cluster into multiple virtual environments so that different teams, projects, or applications can use the same cluster without interfering with each other.  
* It helps in: Separating applications, Managing access, Organizing resources
*  Real-World Example: Cluster = Building,  Namespace = Department ( Department means HR, Finance, Development)
*  Each department works independently. So resources of one department do not mix with another.
*  Example in Kubernetes
  - Suppose your cluster runs multiple applications:
  - Application: Payment App, Shopping App, Monitoring Tools
  - Namespace: Payment, Shopping, Monitoring
  - Each namespace contains its own: Pods, Services, Deployments, ConfigMaps
 
### Why Namespace is Used

1. Organize resources: Large clusters may have thousands of pods.
2. Resource isolation: Applications do not conflict with each other.
3. Access control using RBAC: Using RBAC, you can give access to only a specific namespace.

####  How User Connects to Role

* Done using RoleBinding YAML
* RoleBinding has:
  * Subjects → User / ServiceAccount / Group
  * roleRef → Role

> Important: Kubernetes does not create users. Users are managed externally (IAM, certificates, identity providers)

---

#### RoleBinding with ClusterRole
Reason: Reusability
* ClusterRole can be reused across namespaces
* Avoids creating same Role multiple times

> Important: RoleBinding restricts scope to namespace. Binding decides scope, not Role. why do rollbinding can use a custer role: RoleBinding can use a ClusterRole to reuse its permissions, but the access will still be limited to one namespace.


## ConfigMap

* Stores configuration data in key–value pairs
* ConfigMap is used to store configuration data in key–value pairs. Pods and containers use this configuration from the ConfigMap to avoid modifying the application code.
* ConfigMap stores non-sensitive configuration data such as environment variables, application settings, and configuration files in key–value format for use by containers in Kubernetes.
* ConfigMap stores configuration data and provides it to containers inside a Pod through environment variables, command arguments, or mounted volumes.
* ConfigMap: Stores configuration. Example: DB_HOST = mysql-service, LOG_LEVEL = debug
* Pod: Connects to the ConfigMap.
* Container: Reads the configuration.
* Application: Runs using those settings.
* Where does ConfigMap store its data?: ConfigMap data is stored in the Kubernetes database called etcd.
* Flow: User → Kubernetes API Server → etcd → ConfigMap → Pod → Container

#### Step-by-step explanation
**1. User creates ConfigMap:** kubectl create configmap app-config --from-literal=APP_ENV=production
``` YAML file:
apiVersion: v1


apiVersion: v1
kind: ServiceConfigMap

metadata:
  name: app-config
data:
  APP_ENV: production
```
**2. API Server receives the request:** When you run kubectl apply or kubectl create, the request goes to the Kubernetes API Server.

**3. API Server stores data in etcd:** The API Server saves the ConfigMap data in etcd.
-	etcd = Kubernetes key-value database
-	It stores all cluster state: Pods, Services, Deployments, ConfigMaps, Secrets

**4. Pod reads ConfigMap:** When a Pod needs configuration, the Kubelet fetches the ConfigMap from the API Server, which retrieves it from etcd.
Architecture: ConfigMap YAML --> kubectl --> API Server --> etcd (stores ConfigMap data) --> Pod --> Container reads configuration

#### Important Points
* Stored in etcd
* Managed by API Server
* Pods do NOT directly access etcd

---

### Usage Methods / How Pod Uses ConfigMap: in 3 ways:

**1. Environment:** are key–value pairs used to configure applications at runtime without changing the application code. In Kubernetes, they can be provided through ConfigMaps, Secrets, or directly in Pod definitions.
Environment variables are key–value variables inside a container. They configure how the application runs.

```
Variables env:
- name: DATABASE_URL
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: DATABASE_URL
```
**2. Command Line Arguments:** ConfigMap values can be passed as arguments to containers.

**3. Volume Mount:** ConfigMap can be mounted as a file inside container.

### Why ConfigMap

* We use ConfigMap to separate configuration from application code. This means:
* No need to rebuild Docker image / You don’t need to rebuild the Docker image when configuration changes.
* Easy configuration updates / You can change configuration easily.
---
> Important: Stores NON-sensitive data only. Do NOT store passwords, API keys. Data stored as key–value pairs. Used by Pods / Containers. Helps make applications flexible and configurable

## Secret
Stores sensitive data securely
* Example: Passwords, API keys, Tokens
* Stored as Base64 encoded
* Can be encrypted in etcd
#### Base64 Encoding
Converts data into text format
* It uses 64 characters: A–Z (26 uppercase letters), a–z (26 lowercase letters), 0–9 (10 numbers), + and / (2 symbols). Total = 64 characters
#### etcd (Kubernetes Database)
* Distributed key–value database and Stores entire cluster state
* etcd is a distributed key-value database used by Kubernetes to store all cluster state information such as pods, services, deployments, ConfigMaps, and Secrets. The Kubernetes API Server is the only component that directly interacts with etcd.
* Example things stored in etcd: Pods, Services, Deployments, ConfigMaps, Secrets, Nodes, Namespaces

**Step 1 — User request:** `kubectl apply -f pod.yaml`. The request goes to the Kubernetes API Server.

**Step 2 — API Server validates:** The API Server checks: YAML format, Authentication, Authorization. If everything is correct, it proceeds.

**Step 3 — Data stored in etcd:** The API Server stores the object in etcd.
- Example stored data: Key: /registry/pods/default/mypod and Value: Pod configuration

**Step 4 — Scheduler assigns node:** The Scheduler checks etcd and decides which node should run the Pod.

**Step 5 — Kubelet creates the Pod:** The Kubelet on that node reads the instruction and creates the Pod and containers.

> Important Rule: Only API Server talks to etcd and Pods never access etcd directly

#### What Happens if etcd Goes Down
- Kubernetes cannot read cluster data
- New pods cannot be created
- Updates cannot happen
- Cluster management stops
- Existing containers may still run, but the cluster cannot be controlled.

##### ConfigMap & Secret Updates
* Env variables → Need Pod restart
* Volume mount → Auto update

#### Desired vs Actual/ Current State
* Desired → What you want
* Current → What exists
* Controller makes them match

#### Resource vs Object
* Resource → Type (Pod)
* Object → Instance (nginx-pod)

### High Availability (HA)
* To avoid failure, Kubernetes uses multiple etcd nodes.
* Example: etcd-node-1, 2,3. These nodes form an etcd cluster. This is called High Availability (HA).
* How HA Works: etcd uses a system called Raft consensus algorithm.
* Meaning: Multiple nodes store the same data. One node becomes the leader. Others are followers. (Leader Follower, Follower)
* If the leader fails, another node becomes the new leader automatically
---
**1. How ConfigMap and Secret updates affect running Pods (Dynamic update vs Pod restart)**
- If ConfigMap or Secret is used as environment variables, the Pod must be restarted to apply changes. If it is mounted as a volume, Kubernetes automatically updates the file inside the container.

**2. ConfigMap vs Environment Variables vs Secrets vs Volumes**
- Volumes: Volumes provide persistent storage for containers. Containers normally lose data when they restart. Volume helps store data permanently.
- Examples: Database storage, Log files, Shared data between containers

**3. How etcd Stores Kubernetes Data**
- etcd is a key–value database, so Kubernetes stores all cluster objects like Pods, Services, ConfigMaps, Secrets, Nodes as key–value pairs.
```
Example of etcd Data Format
Example Pod created in Kubernetes:
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx
Inside etcd, it is stored like this:
Key:
/registry/pods/default/nginx-pod

Value:
{
 "kind": "Pod",
 "metadata": {
   "name": "nginx-pod",
   "namespace": "default"
 },
 "spec": {
   "containers": [
     {
       "name": "nginx",
       "image": "nginx"
     }
   ]
 }
}
```
```
Example Keys Stored in etcd
/registry/pods/default/nginx-pod
/registry/services/default/my-service
/registry/configmaps/default/app-config
/registry/secrets/default/db-secret
/registry/nodes/node1
Meaning of the Key
Example: /registry/pods/default/nginx-pod
Breakdown:
Part	Meaning
registry	Kubernetes storage path
pods	Resource type
default	Namespace
nginx-pod	Pod name
```
---
# Custom Resource (CR)
* User-defined Kubernetes object that allows you to store and manage your own application-specific data inside the Kubernetes API
* Custom Resource = Your own Kubernetes object
* Just like Kubernetes has: Pod, Service, Deployment
* You can create your own type like: Database, Backup, Application
* Example: MySQLCluster, Backup
* Custom Resource is a Kubernetes object that extends Kubernetes API to manage custom application resources. It is created using Custom Resource Definition (CRD).
#### Why Custom Resources Are Needed
Kubernetes built-in resources are not enough for every application.
- Example: Suppose you are running a MySQL database cluster.
- Kubernetes does not have a built-in resource called MySQLCluster.
- So you create a Custom Resource like: MySQLCluster. Then Kubernetes can manage it.

A Kubernetes Object is a configuration or resource that tells Kubernetes what you want to run in the cluster.
- Object: Purpose
- Pod: Runs containers
- Service: Exposes Pods to network
- Deployment: Manages Pod replicas
- ConfigMap: Stores configuration data
- Secret: Stores sensitive data
- Namespace: Organizes resources
- ReplicaSet:Maintains number of Pods

How Objects Work
- When you create an object using YAML or kubectl, you are telling Kubernetes:
- "This is the state I want." Kubernetes will constantly check and maintain that state.
- Desired State: What you want. Example: replicas: 3
- Current State: What Kubernetes currently has. Example: running pods: 2
- Controller will create 1 more pod to match the desired state.
- A resource is a type of object defined in the Kubernetes API, such as Pod or Service. An object is an instance of that resource created in the cluster and stored in etcd. An instance is something that belongs to or is created from a particular type or definition. An instance is a specific object created from a resource type, definition, or template.
- Example: Class → Car, Object → MyCar
- Same way: Resource → Pod, Object → nginx-pod

#### Custom Resource Definition (CRD)

* A Custom Resource Definition (CRD) is a Kubernetes object that allows you to create your own resource type in Kubernetes.
* CRD = A way to extend the Kubernetes API with your own resources.
* Kubernetes already has built-in resources like: Pod, Service, Deployment. But if you want a new resource type, you create a CRD.
* Example: Suppose you want Kubernetes to manage databases. Kubernetes does not have a built-in resource called Database. So you create a CRD for it.
* This tells Kubernetes: “A new resource called Database exists.”
* Once the CRD is created, you can create Custom Resources. Now Kubernetes understands Database as a resource.
* So CRD defines the structure/schema of the resource.
* Example: Create a new resource type called Database.
```
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.mycompany.com
# After applying this, Kubernetes now knows a new resource type:
Database
# You can check CRDs using:
kubectl get crd
```
#### Custom Controller
* A Custom Controller is a program that watches Custom Resources and takes actions to match the desired state with the actual state in the Kubernetes cluster.
* Program that manages Custom Resources automatically.

### Why needed:
* When you create a Custom Resource Definition (CRD), Kubernetes only knows the resource type, but it does not know how to manage it.
* Example: You create a CRD called: Database
* Kubernetes understands that Database is a resource, but it does not know: how to create database pods, how to scale it, how to backup it. So a Custom Controller is needed to handle these tasks.
* Kubernetes does not know how to manage custom resources

##### Example Flow: Suppose we create a Database custom resource.
**Step 1** – Create CRD like `Database`

**Step 2** – Create Custom Resource
```
apiVersion: mycompany.com/v1
kind: Database
metadata:
  name: mysql-db
spec:
  storage: 10Gi
  version: 8.0
```
**Step 3** – Custom Controller Watches It
- The Custom Controller detects the new resource and performs actions like:
-	create pods
-	create storage
-	create services
```
Flow:
Custom Resource Created
        ↓
Custom Controller watches API Server
        ↓
Controller creates Pods / Services
        ↓
Cluster reaches desired state
```

## Built-in vs Custom Controller

| Built-in                       | Custom                       |
| ------------------------------ | ---------------------------- |
| Provided by Kubernetes         | Created by developers        |
| Manages default resources      | Manages custom resources     |
| Example: Deployment Controller | Example: Database Controller |

