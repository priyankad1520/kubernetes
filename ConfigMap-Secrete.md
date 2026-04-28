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
