Here are your notes **organized cleanly without changing content** 👇

---

# 📘 Kubernetes Notes

---

## 🔹 RBAC (Role-Based Access Control)

* RBAC stands for Role-Based Access Control.
* It is a security mechanism used to control who can access what resources in a system based on their roles.
* RBAC is widely used in systems like Kubernetes, databases, operating systems, and enterprise applications. 

### 🔸 Real-Time Example

* Admin → Create users, delete users, manage system
* Developer → Deploy applications
* Viewer → Only view reports

---

## 🔹 RBAC in Kubernetes

* RBAC controls who can access cluster resources. 

### Resources include:

* Pods
* Services
* Deployments
* Nodes
* ConfigMaps

### RBAC decides:

* Who can create
* Who can view
* Who can delete
* Who can update

---

## 🔹 RBAC Components

### 1. Role

* Defines permissions inside a single namespace
* Example permissions:

  * Get pods
  * List pods
  * Create pods
* Example:

  * Role: pod-reader
  * Permission: Read pods
  * Namespace: default

---

### 2. ClusterRole

* Defines permissions for the entire cluster
* Not limited to one namespace
* Example permissions:

  * View nodes
  * Manage namespaces
  * Access all pods

---

### 3. RoleBinding

* Connects a user to a Role
* Assigns Role to user/group/service account within a namespace
* Example:

  * User → Priyanka
  * Role → pod-reader
  * Result → Priyanka can read pods

---

### 4. ClusterRoleBinding

* Connects a user to a ClusterRole
* ClusterRole alone does not give access
* Access is granted only when attached using binding
* Example:

  * User → Admin
  * ClusterRole → cluster-admin
  * Result → Admin controls whole cluster

---

## 🔹 Namespace in Kubernetes

* Used to logically divide a cluster into multiple environments
* Helps different teams/projects use same cluster without conflict 

### Helps in:

* Separating applications
* Managing access
* Organizing resources

---

### 🔸 Real-World Example

* Cluster = Building
* Namespace = Department

Departments:

* HR
* Finance
* Development

---

### 🔸 Kubernetes Example

Applications:

* Payment App
* Shopping App
* Monitoring Tools

Namespaces:

* Payment
* Shopping
* Monitoring

Each namespace contains:

* Pods
* Services
* Deployments
* ConfigMaps

---

### 🔸 Why Namespace is Used

1. Organize resources
2. Resource isolation
3. Access control using RBAC

---

## 🔹 How User Connects to Role

* Done using RoleBinding YAML
* RoleBinding has:

  * Subjects → User / ServiceAccount / Group
  * roleRef → Role

### Important:

* Kubernetes does not create users
* Users are managed externally (IAM, certificates, identity providers)

---

## 🔹 RoleBinding with ClusterRole

### Reason: Reusability

* ClusterRole can be reused across namespaces
* Avoids creating same Role multiple times

### Important:

* RoleBinding restricts scope to namespace
* Binding decides scope, not Role

---

## 🔹 ConfigMap

* Stores configuration data in key–value pairs
* Used by Pods/containers without changing application code 

### Example:

* DB_HOST = mysql-service
* LOG_LEVEL = debug

---

### 🔸 Flow

User → API Server → etcd → ConfigMap → Pod → Container

---

### 🔸 Steps

1. User creates ConfigMap
2. API Server receives request
3. Data stored in etcd
4. Pod reads ConfigMap

---

### 🔸 Important Points

* Stored in etcd
* Managed by API Server
* Pods do NOT directly access etcd

---

### 🔸 Usage Methods

1. Environment Variables
2. Command Line Arguments
3. Volume Mount

---

### 🔸 Why ConfigMap

* Separate config from code
* No need to rebuild Docker image
* Easy configuration updates

---

### ⚠️ Important

* Stores NON-sensitive data only
* Do NOT store passwords, API keys

---

## 🔹 Environment Variables

* Key–value pairs inside container
* Used to configure applications at runtime

---

## 🔹 Secret

* Stores sensitive data securely

* Example:

  * Passwords
  * API keys
  * Tokens

* Stored as Base64 encoded

* Can be encrypted in etcd

---

### 🔸 Base64 Encoding

* Converts data into text format
* Uses:

  * A–Z
  * a–z
  * 0–9
  * * /

---

## 🔹 etcd (Kubernetes Database)

* Distributed key–value database
* Stores entire cluster state 

### Stores:

* Pods
* Services
* Deployments
* ConfigMaps
* Secrets
* Nodes
* Namespaces

---

### 🔸 Flow

1. User → kubectl apply
2. API Server validates
3. Data stored in etcd
4. Scheduler assigns node
5. Kubelet creates Pod

---

### ⚠️ Important Rule

* Only API Server talks to etcd
* Pods never access etcd directly

---

### 🔸 If etcd fails

* Cannot create new pods
* Cannot update cluster
* Cluster control stops

---

### 🔸 High Availability (HA)

* Multiple etcd nodes
* Uses Raft algorithm
* Leader + Followers

---

## 🔹 ConfigMap & Secret Updates

* Env variables → Need Pod restart
* Volume mount → Auto update

---

## 🔹 Volumes

* Provide persistent storage
* Used for:

  * Database
  * Logs
  * Shared data

---

## 🔹 Kubernetes Object

* Represents desired state of cluster

### Examples:

| Object     | Purpose               |
| ---------- | --------------------- |
| Pod        | Runs containers       |
| Service    | Exposes pods          |
| Deployment | Manages replicas      |
| ConfigMap  | Stores config         |
| Secret     | Stores sensitive data |
| Namespace  | Organizes resources   |
| ReplicaSet | Maintains pods        |

---

### 🔸 Desired vs Current State

* Desired → What you want
* Current → What exists
* Controller makes them match

---

## 🔹 Resource vs Object

* Resource → Type (Pod)
* Object → Instance (nginx-pod)

---

## 🔹 Custom Resource (CR)

* User-defined Kubernetes object
* Example:

  * MySQLCluster
  * Backup

---

## 🔹 Custom Resource Definition (CRD)

* Extends Kubernetes API
* Allows creating new resource types

### Example:

* Create resource: Database

---

## 🔹 Custom Controller

* Program that manages Custom Resources

### Why needed:

* Kubernetes does not know how to manage custom resources

---

### 🔸 Flow

Custom Resource → Controller watches → Creates Pods/Services → Desired state achieved

---

## 🔹 Built-in vs Custom Controller

| Built-in                       | Custom                       |
| ------------------------------ | ---------------------------- |
| Provided by Kubernetes         | Created by developers        |
| Manages default resources      | Manages custom resources     |
| Example: Deployment Controller | Example: Database Controller |

---

✅ Your notes are now clean, structured, and interview-ready

---

If you want next:
👉 I can convert this into **PDF / revision notes / mind map**
👉 Or create **short Q&A for interview preparation**
