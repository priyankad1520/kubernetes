# Service
Service is a Kubernetes object that exposes a group of Pods using labels and selectors.  It provides a stable IP and DNS name to access Pods. 
- Why we use Service: Pod IPs are dynamic and change on restart.  Service provides stable access to Pods. Acts as a middle layer between users and Pods. 
- How we use Service: Service uses labels & selectors to identify Pods. It routes traffic to Pods and performs load balancing. Uses ports: targetPort (container), port (service), nodePort (external). 
- What kind of problem it solves: Fixes dynamic Pod IP issue.  Enables reliable communication inside and outside cluster. Distributes traffic across multiple Pods. 

## Types of Services
1. ClusterIP: Default service type with internal IP. 
- Why: For communication between Pods inside cluster. 
- How: Pods access using service name (DNS). 
- Problem it solves: Stable internal communication. 
- Key: Only inside cluster ❌ external access 

2. NodePort: Exposes service using Node IP + Port. 
- Why: To allow external users to access application. 
- How: User → NodeIP:NodePort → ClusterIP → Pods 
- Problem it solves: Basic external access without cloud LB 
- Key: Uses existing Node IP 
-	Example: 192.168.1.10:30007 
-	✔ Works but ❌ not production friendly 

3. LoadBalancer: Provides a single public IP using cloud provider. 
- Why: Simple and production-ready external access. 
- How: User → LoadBalancer → NodePort → ClusterIP → Pods 
- Problem it solves: Removes need to use Node IP + Port. Provides automatic load balancing 
- Key: LoadBalancer = NodePort + Cloud LB. LoadBalancer works for one cluster, not multiple clusters 
-	Example: http://35.12.45.100 
-	✔ User-friendly ✔ Production ready 

4. ExternalName: Maps service name to external DNS. 
- Why: To access external services using Kubernetes name. 
- How: Service → External DNS → External system 
- Problem it solves: Avoids hardcoding external URLs 

Important Concepts
-	Service Name (DNS) → Pods use name instead of IP 
-	Endpoints → Define which Pods receive traffic 
-	Ports → targetPort (container), port (service), nodePort (external) 

# Ingress:
Routes external traffic to multiple services using one entry point 
- Why: Avoid multiple LoadBalancers 
- How: User → Ingress → Services → Pods 
- Problem it solves: Single IP for multiple services. Domain & path-based routing 
- Benefits: Single entry point , Domain & path routing , HTTPS support, Cost reduction 
> "A Service in Kubernetes provides a stable IP and DNS to access dynamic Pods, while different types like ClusterIP, NodePort, and LoadBalancer handle internal and external communication, and Ingress enables advanced routing using a single entry point."

#### Why we use Ingress 
- Cloud providers charge money for each LoadBalancer and static IP, so Ingress helps reduce cost and simplify traffic management.
- Every LoadBalancer costs money in cloud providers like Amazon Web Services, Google Cloud, Microsoft Azure
- ❌ Many static public IPs are created, Hard to manage traffic, SSL certificates must be configured multiple times. So cost and complexity increase.

Solution With Ingress: Instead of creating many load balancers, we create one LoadBalancer for the Ingress Controller.
- Ingress uses a load balancer internally.
- Most commonly used controller:
- NGINX Ingress Controller
- NGINX performs: Traffic routing, Load balancing, SSL termination. So it does support load balancing.

Ingress Controller in Kubernetes
- Simple idea: Ingress → only defines rules
- Ingress Controller → actually processes traffic using those rules
- Without an Ingress Controller, an Ingress resource does nothing.

Why an Ingress Controller is Needed
- An Ingress object only contains configuration like: Domain name, Path routing, Target service
- Example rule:
- Myapp.com/api → api-service or Myapp.com/web → web-service
- But Kubernetes itself does not route traffic. So we need a controller (like NGINX) that:
- Watches Ingress resources, Configures routing, Forwards traffic to services
- That controller is called the Ingress Controller.

How an Ingress Controller Works
Explanation:
- A user sends a request from the internet.
- The request reaches the Ingress Controller.
- The controller checks Ingress rules.
- It forwards the request to the correct service.
- The service sends it to the pod.
- The Ingress Controller routes the request to the api-service.

Popular Ingress Controllers: NGINX Ingress Controller, Traefik, HAProxy, **Istio Gateway

Responsibilities of an Ingress Controller: HTTP / HTTPS routing, Load balancing, SSL termination, Path-based routing, Domain-based routing, Traffic management 

Simple Real-World Example:  Think of a hotel reception desk. Guests enter the hotel. The receptionist checks where they need to go. 
- The receptionist directs them to the correct room.
- Ingress itself does not perform load balancing. To use NGINX for routing and load balancing, we deploy an NGINX Ingress Controller in Kubernetes.
- Ingress defines routing rules, while an Ingress Controller (such as NGINX) implements those rules and performs load balancing and traffic management.
- Ingress routing is the process of directing incoming HTTP/HTTPS requests to the appropriate Kubernetes service based on rules such as hostnames or URL paths.
- Ingress routing is executed by an Ingress Controller like NGINX, which reads the rules defined in the Ingress resource and forwards traffic accordingly. So Ingress decides where the request should go.

### Types of Ingress Routing
1. Path-Based Routing: Traffic is routed using the URL path.
Example: URL --> Service --> Myapp.com/(Frontend-service) --> Myapp.com/api (Api-service) --> Myapp.com/cart (Cart-service)
Example configuration:
```Yaml

Rules:
-	Host: myapp.com
  http:
    paths:
-	Path: /
      Backend:
        Service:
          Name: frontend-service
-	Path: /api
      Backend:
        Service:
          Name: api-service
```
2. Host-Based Routing: Traffic is routed based on domain names.
Example: Domain --> Service --> Api.myapp.com (Api-service) --> Shop.myapp.com (Shop-service) --> Admin.myapp.com (Admin-service)
Example configuration:
```Yaml
Copy code
Rules:
-	Host: api.myapp.com
  http:
    paths:
-	Path: /
      Backend:
        Service:
          Name: api-service
```
### Secure Ingress in Kubernetes
In Kubernetes, Secure Ingress means protecting external traffic entering the cluster using HTTPS and SSL/TLS encryption.
- Normally users access applications using HTTP, which is not secure.
- Secure Ingress ensures that communication between the user and the cluster is encrypted.
- Problems: Data is not encrypted, Hackers can intercept data, Passwords and sensitive data may be exposed
- Example attack: Man-in-the-Middle Attack. To prevent this, we use HTTPS.

HTTPS uses SSL/TLS encryption.
- How Secure Ingress Works:Secure Ingress uses TLS certificates and an Ingress Controller such as NGINX.
- What happens: User sends HTTPS request --> Ingress Controller decrypts the SSL traffic --> Traffic is forwarded to the service and pods. This process is called SSL termination.

TLS Secret in Kubernetes:The SSL certificate must be stored inside Kubernetes as a Secret.
```
Kubectl create secret tls my-tls-secret \
--cert=cert.crt \
--key=key.key
```
This secret contains: SSL certificate and Private key

##  TLS / SSL Passthrough
**SSL Passthrough = Encrypted traffic goes through load balancer without decryption and is handled only by backend server**
---
* **Client sends request using HTTPS**: Data is **encrypted from start (client side)*
* **Load Balancer receives the request**: It **does NOT decrypt the data**, It just **forwards the encrypted traffic**
* **Encrypted data goes directly to backend server (app)** and **Decryption happens at backend server**
---
### Key Points
*  Load balancer is just a **traffic forwarder**
*  It cannot: inspect data, filter content, apply security rules
* **Security Risk**: If attacker sends malicious data → it goes directly to backend and Load balancer **cannot detect it**
* **Performance Impact**: Backend server handles decryption → more CPU usage
Here’s **why we use SSL Passthrough**, explained in simple line format 👇

---

#### Why use SSL Passthrough?
> SSL Passthrough is used to maintain end-to-end encryption where the load balancer forwards encrypted traffic directly to backend servers without decrypting it.
* **End-to-end encryption**: Data stays encrypted from **client → backend server**. No one (even load balancer) can read it
* **Higher security / compliance**: Required in industries like banking, healthcare. Sensitive data is never exposed in the middle
* **Backend controls SSL**: Certificates are managed on backend servers. Full control over encryption, TLS versions, policies
* **No data inspection needed**: Use when load balancer **doesn’t need to check content**. Only job = route traffic
* **Useful for non-HTTP protocols**: Works with TCP-based apps (not just HTTP/HTTPS)
#### When NOT to use (Important)
* If you need:
  * Web Application Firewall (WAF)
  * Content-based routing (URL/path-based)
  * Logging or monitoring request data

Because load balancer **can’t see inside encrypted traffic**
---
## SSL offloading
**SSL Offloading = Load balancer decrypts HTTPS and sends plain HTTP to backend to reduce load and improve performance**
#### What happens in SSL Offloading?
* **Client sends request using HTTPS** --> Data is **encrypted from client**
* **Load Balancer receives the request** --> It **decrypts the HTTPS traffic** --> Now data becomes **plain HTTP**
* **Load Balancer sends request to backend** --> Data goes as **HTTP (not encrypted)**
* **Backend server (app)**: No need to handle SSL. Just processes normal HTTP request
#### Key Points
* Load balancer does **decryption work**
* Backend server is **lightweight (less CPU usage)**
* Data between LB → backend is **not secure (HTTP)**
#### Why we use SSL Offloading?
* **Improves performance**:  Backend servers don’t waste CPU on SSL and Faster response time
* **Centralized SSL management**: Certificates handled in one place (load balancer) and Easy to update and manage
* **Better security features**: Load balancer can: inspect traffic, apply firewall (WAF), do routing (URL-based)
* **Cost efficient**: No need SSL setup on every backend server
#### Disadvantage (Important)
* Data between LB → backend is **not encrypted**
* Risk of: Man-in-the-middle attacks. Data theft inside network

## SSL Bridge
**SSL Bridging = Load balancer decrypts HTTPS, inspects it, then re-encrypts and sends it as HTTPS to backend**
####  What happens in SSL Bridging?

* **Client sends request using HTTPS**: Data is **encrypted from client**
* **Load Balancer receives the request**: It **decrypts the HTTPS traffic**. Now it can **inspect/check the data**
* **Load Balancer re-encrypts the data again**: Converts back to **HTTPS**
* **Data is sent to backend server**: Still **encrypted (HTTPS)**
* **Backend server**: Decrypts again and processes request
#### Key Points
* Encryption from **client → LB → backend (end-to-end)**
* Load balancer can **inspect traffic**
* Backend is still **secure (HTTPS)**
#### Why we use SSL Bridging?
*  **Best security**: Data is always encrypted (no plain HTTP anywhere)
*  **Traffic inspection** Load balancer can: detect attacks, apply WAF, filter requests
*  **Balance of security + control**: Better than Passthrough (no inspection). More secure than Offloading (no plain HTTP)
*  **Used in enterprises**: Banking, healthcare, secure applications
####  Disadvantage
* **More CPU usage**: Decrypt + Encrypt again → extra processing

##  SSL passthough vs Offloding vs Bridging
#### SSL passthough
**1. Costly for server:** Backend does all decryption → more CPU

**2.Secure:** End-to-end encryption

**3. L4 (TCP) load balancing:** Works at network level (no content check)

**4. Use when:** You don’t need filtering, rules, cookies, etc.

**5. No inspection:** Load balancer cannot see data

**6. Recommended (in some cases):** When strict encryption is required
---
#### SSL Offloding
**1. Fast processing:** Load balancer handles SSL → backend is free

**2. Less secure:** Data from LB → backend is HTTP (not encrypted)

**3. L7 load balancing:** Can inspect data (URL, headers)

**4. Use when:** Need speed and low latency

**5. Inspection possible:** LB can filter, route, apply WAF

**6. Not recommended for high security:** Because internal traffic is not encrypted
---
#### SSL Bridging
**1. Costly:** Decrypt + re-encrypt → more CPU

**2. Very secure:** Encryption everywhere (no plain HTTP)

**3. L7 load balancing:** Full inspection + security
 
**4. Use when:** Need both security + advanced LB features

**5. Inspection possible:** LB checks traffic + backend still secure

**6. Recommended:** Best for enterprise/security-critical apps
---
| Feature               | Passthrough | Offloading    | Bridging            |
| --------------------- | ----------- | ------------- | ------------------- |
| Decryption            | Backend     | Load Balancer | Both (LB + Backend) |
| Encryption End-to-End | Yes         | No Partial    | Yes                 |
| Inspection            | No          | Yes           | Yes                 |
| Security              | High        | Medium        | Very High           |
| Performance           | Medium      | Fast          | Slower              |
| Speed                 | Medium      | Fast          | Slow                |
| Inspection            | No          | Yes           | Yes                 |
