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

