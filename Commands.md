# Kubernetes (K8s) Commands List 

## Cluster Commands

* `kubectl cluster-info` → Display cluster information
* `kubectl version` → Check kubectl and cluster version
* `kubectl get nodes` → List all worker/master nodes
* `kubectl describe node <node-name>` → Detailed node information
* `kubectl top nodes` → Show node CPU and memory usage

---

## Namespace Commands

* `kubectl get ns` → List namespaces
* `kubectl create ns <namespace-name>` → Create namespace
* `kubectl delete ns <namespace-name>` → Delete namespace
* `kubectl config set-context --current --namespace=<ns>` → Set default namespace

---

## Pod Commands

* `kubectl get pods` → List pods in current namespace
* `kubectl get pods -A` → List pods in all namespaces
* `kubectl get pod <pod-name> -o wide` → Pod details with node/IP
* `kubectl describe pod <pod-name>` → Detailed pod info
* `kubectl logs <pod-name>` → View pod logs
* `kubectl logs -f <pod-name>` → Live logs streaming
* `kubectl exec -it <pod-name> -- /bin/bash` → Enter pod terminal
* `kubectl delete pod <pod-name>` → Delete pod
* `kubectl top pod` → Show pod resource usage

---

## Deployment Commands

* `kubectl get deployments` → List deployments
* `kubectl create deployment nginx --image=nginx` → Create deployment
* `kubectl scale deployment nginx --replicas=3` → Scale replicas
* `kubectl rollout status deployment/nginx` → Check rollout status
* `kubectl rollout history deployment/nginx` → View rollout history
* `kubectl rollout undo deployment/nginx` → Rollback deployment
* `kubectl set image deployment/nginx nginx=nginx:latest` → Update image
* `kubectl delete deployment nginx` → Delete deployment

---

## Service Commands

* `kubectl get svc` → List services
* `kubectl describe svc <service-name>` → Service details
* `kubectl expose deployment nginx --type=NodePort --port=80` → Expose deployment
* `kubectl delete svc <service-name>` → Delete service

---

## ReplicaSet Commands

* `kubectl get rs` → List ReplicaSets
* `kubectl describe rs <rs-name>` → ReplicaSet details
* `kubectl delete rs <rs-name>` → Delete ReplicaSet

---

## ConfigMap & Secret Commands

* `kubectl get configmap` → List ConfigMaps
* `kubectl create configmap app-config --from-literal=env=prod` → Create ConfigMap
* `kubectl get secrets` → List secrets
* `kubectl create secret generic db-secret --from-literal=password=1234` → Create secret

---

## Namespace Resource Commands

* `kubectl get all` → Show all resources in namespace
* `kubectl get all -A` → Show all resources in all namespaces

---

## YAML File Commands

* `kubectl apply -f app.yaml` → Create/update resources from YAML
* `kubectl create -f app.yaml` → Create resource from YAML
* `kubectl delete -f app.yaml` → Delete resource from YAML
* `kubectl replace -f app.yaml` → Replace existing resource
* `kubectl diff -f app.yaml` → Compare changes before apply

---

## Describe & Debug Commands

* `kubectl describe pod <pod-name>` → Detailed troubleshooting info
* `kubectl events` → View cluster events
* `kubectl get events --sort-by=.metadata.creationTimestamp` → Events in time order

---

## Resource Monitoring Commands

* `kubectl top nodes` → Node CPU/memory usage
* `kubectl top pods` → Pod CPU/memory usage

---

## Context & Config Commands

* `kubectl config view` → View kubeconfig
* `kubectl config get-contexts` → List contexts
* `kubectl config use-context <context-name>` → Switch cluster context

---

## Persistent Volume Commands

* `kubectl get pv` → List Persistent Volumes
* `kubectl get pvc` → List Persistent Volume Claims
* `kubectl describe pvc <pvc-name>` → PVC details

---

## DaemonSet Commands

* `kubectl get daemonset` → List DaemonSets
* `kubectl describe daemonset <name>` → DaemonSet details

---

## StatefulSet Commands

* `kubectl get statefulset` → List StatefulSets
* `kubectl describe statefulset <name>` → StatefulSet details

---

## Job & CronJob Commands

* `kubectl get jobs` → List jobs
* `kubectl get cronjobs` → List cronjobs
* `kubectl create job test --image=busybox` → Create job

---

## Important Shortcuts (Very Useful)

* `po` → pods
* `deploy` → deployments
* `svc` → services
* `ns` → namespaces
* `rs` → ReplicaSets

Example:

* `kubectl get po`
* `kubectl get svc`

---

## Most Used Real-Time DevOps Commands

* `kubectl get pods -A`
* `kubectl logs -f <pod>`
* `kubectl describe pod <pod>`
* `kubectl exec -it <pod> -- bash`
* `kubectl rollout restart deployment <name>`
* `kubectl apply -f deployment.yaml`

---

# Easy Flow to Remember

👉 Cluster → Namespace → Pod → Deployment → Service → Logs → Debugging → Scaling → Rollback
