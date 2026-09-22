# k8s-A-to-Z-
learning k8s-->

# ☸️ Kubernetes — Complete Concept + Commands + Examples

## 1. What is Kubernetes?

Kubernetes, commonly called **K8s**, is an open-source **container orchestration platform** used to deploy, manage, scale, network, and maintain containerized applications. Docker can run containers on a machine, but when you have hundreds of containers across many servers, manually managing them becomes difficult. Kubernetes automates tasks such as starting containers, restarting failed containers, distributing workloads across machines, exposing applications, scaling replicas, rolling out new versions, and maintaining the desired application state.

### Easy memory

> **Docker runs containers. Kubernetes manages containers at scale.**

---

# 2. Why Do We Need Kubernetes?

Suppose your application has:

```text
Frontend → 3 containers
Backend  → 5 containers
Database → 1 container
```

If one backend container crashes, someone would need to manually restart it. If traffic increases, someone would need to manually create more containers. Kubernetes solves these problems by continuously monitoring the desired state and taking corrective actions automatically. For example, if you tell Kubernetes that you need **5 backend replicas**, Kubernetes attempts to keep 5 healthy replicas running.

---

# 3. Kubernetes Cluster

A **Kubernetes cluster** is a group of machines that work together to run containerized applications. A cluster consists mainly of a **control plane** and **worker nodes**. The control plane makes decisions and manages the cluster, while worker nodes actually run application workloads. In a production environment, you may have multiple control-plane components and many worker nodes.

```text
Kubernetes Cluster
│
├── Control Plane
│
└── Worker Nodes
    ├── Node 1
    ├── Node 2
    └── Node 3
```

---

# 4. Control Plane

The **control plane** is the management layer of Kubernetes. It receives requests, stores cluster state, decides where workloads should run, and continuously works to maintain the desired state. Important control-plane components include the **API Server, etcd, Scheduler, Controller Manager**, and in some setups **Cloud Controller Manager**.

### Easy memory

> **Control Plane = Brain of Kubernetes**

---

# 5. Worker Node

A worker node is a machine that **runs application workloads**. It contains components such as the **kubelet**, a container runtime, and networking components. When you create a Pod, Kubernetes schedules it onto an appropriate worker node, and the kubelet on that node makes sure the Pod's containers are running.

```text
Worker Node
│
├── kubelet
├── Container Runtime
├── Network components
└── Pods
```

---

# 6. Pod

A **Pod is the smallest deployable unit in Kubernetes**. A Pod contains one or more containers that share the same network namespace and can share storage volumes. Most applications use one main application container per Pod, but multiple tightly coupled containers can be placed in the same Pod when they need to share resources and lifecycle closely.

For example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

Create it:

```bash
kubectl apply -f pod.yaml
```

### Remember

> **Pod → contains container(s)**

---

# 7. Pod vs Container

A **container** is the actual runtime environment for an application process, while a **Pod** is Kubernetes' smallest deployment unit that wraps one or more containers. Kubernetes generally doesn't manage individual containers directly; it manages Pods. For example, an Nginx container can run inside an `nginx-pod`. If the Pod is recreated, a new container is created as part of the new Pod.

---

# 8. Pod Lifecycle

A Pod can move through different phases such as:

```text
Pending
   ↓
Running
   ↓
Succeeded / Failed
```

A Pod may also be terminated and recreated by a controller such as a Deployment. You can check Pod status using:

```bash
kubectl get pods
```

For example, if you see:

```text
NAME         READY   STATUS    RESTARTS
nginx-pod    1/1     Running   0
```

the Pod is currently running with its expected container ready.

---

# 9. Namespace

A **Namespace** provides a logical separation inside a Kubernetes cluster. Organizations can use namespaces to separate environments or teams, such as:

```text
development
testing
staging
production
```

Create one:

```bash
kubectl create namespace dev
```

Then run:

```bash
kubectl get pods -n dev
```

The `-n dev` tells Kubernetes to look inside the `dev` namespace.

### Remember

> **Namespace = logical isolation/grouping**

---

# 10. Kubernetes API Server

The **API Server** is the main entry point into the Kubernetes control plane. When you execute:

```bash
kubectl get pods
```

or:

```bash
kubectl apply -f deployment.yaml
```

`kubectl` communicates with the Kubernetes API Server. The API Server validates requests, updates cluster state, and interacts with other control-plane components.

### Simple flow

```text
kubectl
   ↓
API Server
   ↓
Kubernetes Control Plane
```

---

# 11. etcd

**etcd** is a distributed key-value store used by Kubernetes to store important cluster state and configuration. Information such as Kubernetes objects and their desired state is stored through the API server in etcd. For example, when you create a Deployment, Kubernetes stores the relevant cluster state so the control plane can understand what should exist.

### Interview memory

> **etcd = Kubernetes cluster state database**

---

# 12. Scheduler

The **Kubernetes Scheduler** decides which worker node should run a newly created Pod that has not yet been assigned to a node. It considers factors such as resource availability, constraints, affinity/anti-affinity, taints/tolerations, and other scheduling rules. For example, if you create a Pod requiring 1 CPU and 512 MB memory, the scheduler selects a suitable node based on the cluster's scheduling rules.

### Memory

> **Scheduler = decides WHERE a Pod runs**

---

# 13. Controller Manager

The Controller Manager runs various controllers that continuously compare the **desired state** with the **current state** and take actions to reconcile them. For example, if a Deployment specifies 3 replicas but only 2 Pods are running, the relevant controllers work to create another Pod. This reconciliation model is one of the fundamental ideas behind Kubernetes.

### Memory

> **Controllers = continuously maintain desired state**

---

# 14. kubelet

The **kubelet** is an agent running on each worker node. It receives Pod specifications through the Kubernetes control plane and makes sure the containers described by those specifications are running correctly on that node. If a container inside a Pod fails, the kubelet and container runtime can work together according to the Pod's restart policy to restore the expected state.

### Memory

> **kubelet = worker-node agent**

---

# 15. Container Runtime

The container runtime is the software responsible for actually running containers on Kubernetes nodes. Modern Kubernetes commonly uses runtimes such as **containerd** or **CRI-O** through the Kubernetes Container Runtime Interface (CRI). Kubernetes itself is the orchestrator; the runtime performs the low-level container execution.

---

# 16. `kubectl`

`kubectl` is the **command-line tool used to communicate with a Kubernetes cluster** through the Kubernetes API Server. For example, `kubectl get pods` retrieves Pod information, `kubectl apply -f app.yaml` creates or updates resources from a YAML file, and `kubectl delete pod nginx-pod` requests deletion of a Pod.

### Memory

> **kubectl = command-line interface for Kubernetes**

---

# 17. `kubectl get`

```bash
kubectl get pods
```

This command displays resources in Kubernetes. For example, `kubectl get pods` shows Pods in the current namespace, including their name, readiness, status, restart count, and age. You can use it for many resources:

```bash
kubectl get pods
kubectl get services
kubectl get deployments
kubectl get nodes
```

---

# 18. `kubectl get pods -o wide`

```bash
kubectl get pods -o wide
```

This displays additional Pod information such as the Pod IP and the worker node where the Pod is running. For example, if you want to troubleshoot networking or determine which node is hosting `nginx-pod`, this command provides more information than a normal `kubectl get pods`.

---

# 19. `kubectl describe`

```bash
kubectl describe pod nginx-pod
```

`kubectl describe` displays detailed information about a Kubernetes resource. For a Pod, it can show container configuration, node assignment, conditions, volumes, probes, and especially **Events**. If a Pod is stuck in `Pending` or repeatedly failing, `kubectl describe pod` is one of the first troubleshooting commands you should use.

---

# 20. `kubectl logs`

```bash
kubectl logs nginx-pod
```

This command displays logs produced by a container in a Pod. For example, if an application starts but returns errors, `kubectl logs nginx-pod` can show application output. If a Pod contains multiple containers, you can specify the container using `-c`, such as `kubectl logs nginx-pod -c app`.

---

# 21. `kubectl exec`

```bash
kubectl exec -it nginx-pod -- sh
```

This command opens a shell or executes a command inside a running container in a Pod. For example, once inside the container, you can run commands such as `ls`, `pwd`, or `cat /etc/os-release` to troubleshoot the application environment. `-it` provides an interactive terminal, and `--` separates kubectl options from the command being executed inside the container.

---

# 22. Deployment

A **Deployment** is a Kubernetes controller used to manage a set of replicated Pods for usually stateless applications. Instead of manually creating Pods, you define the desired number of replicas and the container image, and the Deployment manages ReplicaSets and Pods for you.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

This tells Kubernetes to maintain **3 nginx Pod replicas**.

---

# 23. Replica

A replica is another running copy of an application Pod. If you configure:

```yaml
replicas: 3
```

Kubernetes attempts to maintain three matching Pods.

```text
Deployment
    │
    └── ReplicaSet
        ├── Pod
        ├── Pod
        └── Pod
```

If one Pod fails, the controller works to create another so the desired replica count can be restored.

---

# 24. ReplicaSet

A **ReplicaSet** maintains a specified number of matching Pod replicas. Deployments normally manage ReplicaSets rather than requiring you to manage ReplicaSets directly. For example, when a Deployment specifies `replicas: 3`, the Deployment creates or updates a ReplicaSet that attempts to maintain three Pods.

### Interview answer

> Deployment manages application rollout and ReplicaSets; ReplicaSet maintains the desired number of Pods.

---

# 25. Service

A Kubernetes **Service** provides a stable network endpoint for accessing a group of Pods. Pods can be recreated and their IP addresses can change, so clients should not normally connect directly to individual Pod IPs. A Service selects Pods using labels and provides a stable virtual IP/DNS name.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

Apply:

```bash
kubectl apply -f service.yaml
```

---

# 26. Service Types

The main Service types you should know are:

```text
ClusterIP
NodePort
LoadBalancer
ExternalName
```

**ClusterIP** is the default and exposes the Service internally within the cluster. **NodePort** exposes a Service through a port on each node. **LoadBalancer** requests an external load-balancing integration when supported by the environment, such as a cloud provider. `ExternalName` maps a Service to an external DNS name rather than selecting Pods.

---

# 27. ClusterIP

```yaml
type: ClusterIP
```

ClusterIP is the default Service type and provides an internal stable endpoint for communication within the cluster. For example, a backend application can communicate with a MySQL or Redis service using its Kubernetes DNS name. It is normally not directly accessible from outside the cluster.

---

# 28. NodePort

```yaml
type: NodePort
```

NodePort exposes a Service through a port on each node, allowing traffic to reach the Service from outside the cluster when network access to the node is available. For example, a development environment could expose Nginx using a NodePort, though cloud production environments often use an Ingress or LoadBalancer depending on architecture.

---

# 29. LoadBalancer

```yaml
type: LoadBalancer
```

A LoadBalancer Service asks the underlying infrastructure, commonly a cloud provider, to provision or integrate with an external load balancer. For example, in a managed Kubernetes environment on AWS, Azure, or Google Cloud, a LoadBalancer Service can be integrated with cloud load-balancing infrastructure. The exact behavior depends on the Kubernetes environment and cloud controller implementation.

---

# 30. Labels

Labels are **key-value pairs attached to Kubernetes objects** and are heavily used for organization and selection. For example:

```yaml
labels:
  app: nginx
  environment: production
```

A Service can select Pods using:

```yaml
selector:
  app: nginx
```

This means the Service can route traffic to Pods carrying the matching label.

### Memory

> **Labels identify/group resources; selectors find them.**

---

# 31. Selectors

A selector defines how one Kubernetes object identifies other objects. For example, if Pods have:

```yaml
labels:
  app: backend
```

a Service can use:

```yaml
selector:
  app: backend
```

to select those Pods. Deployments also use selectors to identify the Pods they manage.

---

# 32. ConfigMap

A **ConfigMap** stores non-sensitive configuration data separately from your container image. For example, application configuration such as `APP_ENV=production` or a service URL can be stored in a ConfigMap and injected into a Pod as environment variables or mounted files.

Create:

```bash
kubectl create configmap app-config --from-literal=APP_ENV=production
```

The application can then consume that configuration without rebuilding the Docker image.

---

# 33. Secret

A Kubernetes **Secret** is designed to hold sensitive configuration such as passwords, tokens, and keys. For example:

```bash
kubectl create secret generic db-secret \
  --from-literal=password=myPassword
```

Pods can consume Secret values as environment variables or mounted files. Kubernetes Secrets provide a mechanism for handling sensitive data, but production environments often add stronger secret-management controls and encryption/access policies.

---

# 34. ConfigMap vs Secret

The main distinction is:

```text
ConfigMap → non-sensitive configuration
Secret    → sensitive configuration
```

For example:

```text
ConfigMap:
APP_ENV=production

Secret:
DB_PASSWORD=******
```

Don't put passwords or API keys into a normal ConfigMap.

---

# 35. Ingress

An **Ingress** is an API object that defines HTTP/HTTPS routing rules from outside the cluster to Services inside the cluster. It can allow multiple applications to share an entry point based on hostnames or URL paths.

For example:

```text
example.com/api
      ↓
Backend Service

example.com/
      ↓
Frontend Service
```

An Ingress requires an **Ingress controller** to actually implement the routing behavior.

---

# 36. Ingress Controller

An Ingress object only defines routing rules; an **Ingress Controller** is the component that watches those rules and implements them. Examples include NGINX Ingress Controller and cloud-provider-specific controllers. The controller receives external traffic and routes it to the appropriate Kubernetes Service according to the Ingress configuration.

---

# 37. StatefulSet

A **StatefulSet** is designed for stateful applications that require stable identities, stable network names, or persistent storage associations. Databases such as some PostgreSQL, MySQL, and distributed systems may use StatefulSets when Kubernetes is responsible for their lifecycle. Unlike ordinary Deployment Pods, StatefulSet Pods have stable ordinal identities such as `database-0`, `database-1`, and so on.

---

# 38. DaemonSet

A **DaemonSet** ensures that a Pod runs on each eligible node, or on each node matching specified scheduling rules. For example, a logging agent or monitoring agent may need to run on every worker node, so a DaemonSet can deploy one Pod per node. When a new eligible node joins the cluster, the DaemonSet can create the corresponding Pod there.

---

# 39. Job

A **Job** creates Pods to perform a task that is expected to complete. For example, you might use a Job for a database migration or batch processing task. Unlike a normal Deployment, which is designed to keep application Pods running, a Job is designed to reach a successful completion state.

---

# 40. CronJob

A **CronJob** creates Jobs according to a schedule. For example, you could configure a CronJob to execute a database backup every night. A conceptual schedule might be:

```text
Every day at 2:00 AM
        ↓
CronJob
        ↓
Job
        ↓
Pod
        ↓
Backup
```

---

# 41. PersistentVolume

A **PersistentVolume (PV)** represents storage available to Kubernetes. It abstracts storage resources from individual Pods. A PV can be backed by cloud disks, network storage, local storage, or other supported storage systems depending on the environment.

### Memory

> **PV = actual persistent storage resource**

---

# 42. PersistentVolumeClaim

A **PersistentVolumeClaim (PVC)** is a request for storage made by a workload. Instead of a Pod directly specifying the underlying storage implementation, it can request something like:

```text
10 GB storage
ReadWriteOnce
```

Kubernetes can then bind the PVC to a suitable PV, often through dynamic provisioning.

### Memory

> **PVC = application's request for storage**

---

# 43. StorageClass

A **StorageClass** defines how storage should be dynamically provisioned. For example, in a cloud Kubernetes cluster, a StorageClass can specify the type of cloud storage to provision when a PVC requests storage. This avoids manually creating every PersistentVolume.

```text
PVC
 ↓
StorageClass
 ↓
Dynamic provisioning
 ↓
PV
 ↓
Storage
```

---

# 44. Requests and Limits

Containers can specify CPU and memory **requests and limits**.

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

A request represents the resources Kubernetes uses when making scheduling decisions, while a limit defines an upper bound enforced by the container runtime/kernel mechanisms for that resource. Proper resource settings help Kubernetes schedule workloads and prevent one workload from consuming uncontrolled amounts of resources.

---

# 45. Liveness Probe

A **liveness probe** checks whether an application is still functioning and whether Kubernetes should restart its container. For example, if an application becomes permanently stuck but its process still exists, a liveness probe can fail and Kubernetes can restart the container.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

### Memory

> **Liveness = Should I restart it?**

---

# 46. Readiness Probe

A **readiness probe** determines whether a Pod is ready to receive traffic. For example, a backend may take 30 seconds to initialize. Until the readiness check succeeds, the Service should not route normal traffic to that Pod.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

### Memory

> **Readiness = Should I send traffic to it?**

---

# 47. Startup Probe

A **startup probe** is useful for applications that take a long time to start. Kubernetes can use the startup probe to determine when the application has successfully started before applying liveness and readiness checks. This helps prevent a slow-starting application from being restarted prematurely.

### Easy memory

```text
Startup   → Has it started?
Readiness → Can it receive traffic?
Liveness  → Is it still healthy?
```

---

# 48. Rolling Update

A rolling update allows Kubernetes to deploy a new application version gradually rather than stopping all existing Pods at once. For example, if you have 5 replicas running version `v1` and update the Deployment to `v2`, Kubernetes can gradually replace old Pods with new ones according to the Deployment's rollout strategy.

Update an image:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.29
```

---

# 49. Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```

This command shows the progress of a Deployment rollout. For example, after updating an image, you can use it to wait for and observe whether the new Pods become ready successfully.

---

# 50. Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

This displays the Deployment's rollout history when revisions are recorded. It is useful when you need to understand which versions have previously been deployed.

---

# 51. Rollback

If a Deployment update causes problems, you can roll back to a previous revision:

```bash
kubectl rollout undo deployment/nginx-deployment
```

This tells Kubernetes to revert the Deployment to its previous rollout revision, assuming the necessary revision history is available.

### Interview memory

> **Rolling update = move forward gradually.**
> **Rollback = move back to previous revision.**

---

# 52. Horizontal Pod Autoscaler — HPA

The **Horizontal Pod Autoscaler** automatically adjusts the number of Pod replicas based on resource utilization or other supported metrics. For example, if your backend normally runs 3 replicas but CPU utilization increases significantly, HPA can increase the replica count according to its configuration. When demand decreases, it can scale down.

Example:

```bash
kubectl autoscale deployment backend \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

This configures a target CPU utilization and replica boundaries.

---

# 53. Vertical Pod Autoscaler — VPA

VPA can recommend or automatically adjust CPU and memory resource requests for Pods based on observed usage, depending on how it is configured and installed in the cluster. It addresses **vertical scaling** of individual workloads, while HPA primarily changes the number of replicas.

### Memory

```text
HPA → More/Fewer Pods
VPA → More/Less CPU or Memory per Pod
```

---

# 54. Node Selector

A `nodeSelector` allows you to tell Kubernetes that a Pod should run only on nodes with a particular label.

For example:

```yaml
nodeSelector:
  disktype: ssd
```

The Pod can then be scheduled only on nodes labeled with `disktype=ssd`.

---

# 55. Taints and Tolerations

A **taint** allows a node to repel Pods that do not explicitly tolerate the taint. A **toleration** allows a Pod to be considered for scheduling onto a node with a matching taint. For example, you might dedicate certain nodes to specialized workloads and prevent ordinary workloads from being scheduled there.

### Memory

> **Taint = keep Pods away.**
> **Toleration = Pod is allowed onto the tainted node.**

---

# 56. Affinity and Anti-Affinity

**Affinity** allows you to express preferences or requirements about where Pods should run based on labels, while **anti-affinity** helps spread Pods apart. For example, you may want replicas of a backend application distributed across different nodes so that failure of one node doesn't remove all replicas. These rules give you more control than a simple `nodeSelector`.

---

# 57. RBAC

**Role-Based Access Control (RBAC)** controls what users, groups, or service accounts are allowed to do in a Kubernetes cluster. For example, you can allow a developer to view Pods in the `dev` namespace without giving them permission to delete production Deployments.

Important RBAC objects include:

```text
Role
ClusterRole
RoleBinding
ClusterRoleBinding
```

---

# 58. ServiceAccount

A **ServiceAccount** provides an identity that Pods can use when interacting with the Kubernetes API or other systems that integrate with Kubernetes identities. For example, a workload can run under a specific ServiceAccount that has only the Kubernetes permissions it needs.

### Security principle

> Give workloads only the permissions they actually require.

---

# 59. Helm

**Helm** is a package manager for Kubernetes. It allows you to package Kubernetes resources into reusable **charts** and configure them through values. Instead of maintaining many separate YAML files manually, teams can use a Helm chart with environment-specific values for development, staging, and production.

Example:

```bash
helm install myapp ./mychart
```

---

# 60. Kubernetes YAML

Kubernetes resources are commonly defined declaratively using YAML.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: myapp

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
        - name: myapp
          image: myapp:1.0
```

The YAML says **what you want Kubernetes to maintain**, rather than manually specifying every individual action Kubernetes must perform.

---

# 61. `kubectl apply`

```bash
kubectl apply -f deployment.yaml
```

This command creates or updates Kubernetes resources defined in a YAML manifest. For example, if `deployment.yaml` describes a Deployment with 3 replicas, `kubectl apply -f deployment.yaml` sends the desired configuration to the API Server. Kubernetes then reconciles the actual cluster state toward that desired state.

### Memory

> **apply = create/update desired configuration**

---

# 62. `kubectl delete`

```bash
kubectl delete -f deployment.yaml
```

This deletes the Kubernetes resources described by the specified manifest. For example, if the YAML contains a Deployment and Service, Kubernetes will request deletion of those resources. You can also delete a specific resource:

```bash
kubectl delete pod nginx-pod
```

---

# 63. `kubectl create`

```bash
kubectl create deployment nginx --image=nginx
```

This command creates a Kubernetes resource imperatively. In this example, Kubernetes creates a Deployment called `nginx` using the Nginx image. `kubectl create` is convenient for quick commands and learning, while declarative YAML with `kubectl apply` is commonly preferred for reproducible infrastructure/application configuration.

---

# 64. `kubectl scale`

```bash
kubectl scale deployment nginx --replicas=5
```

This changes the desired number of replicas for the Deployment to five. Kubernetes then creates or removes Pods until the desired number is reached, subject to scheduling and resource constraints. This is an example of Kubernetes declaratively maintaining the desired state.

---

# 65. `kubectl get nodes`

```bash
kubectl get nodes
```

This command displays the worker/control-plane nodes registered with the cluster and their status. For example:

```text
NAME       STATUS   ROLES
node-1     Ready    worker
node-2     Ready    worker
```

A `Ready` node indicates that Kubernetes currently considers the node healthy enough to participate in scheduling, subject to other conditions.

---

# 66. `kubectl get all`

```bash
kubectl get all
```

This displays several common Kubernetes resource types in the current namespace, such as Pods, Services, Deployments, and ReplicaSets. It is useful for getting a quick overview of an application's resources, although it does not literally show every possible Kubernetes resource type.

---

# 67. `kubectl get events`

```bash
kubectl get events
```

This displays cluster events that can help explain scheduling, image-pull, mount, startup, and other problems. For example, if a Pod is stuck in `Pending`, events may indicate insufficient CPU, a scheduling constraint, or another reason. Events are extremely useful during troubleshooting.

---

# 68. Kubernetes Self-Healing

One of Kubernetes' most important features is **self-healing**. If a Deployment requires 3 replicas and one Pod crashes, Kubernetes detects that the actual state has fallen below the desired state and creates another Pod to restore the replica count. Similarly, failed containers may be restarted according to their restart policy.

### Flow

```text
Desired = 3 Pods
Actual = 3 Pods

One crashes

Desired = 3
Actual = 2

Kubernetes reconciles

Desired = 3
Actual = 3
```

---

# 69. Desired State vs Current State

Kubernetes is fundamentally **declarative**. You specify the desired state, such as "I want 3 replicas of this application," and Kubernetes continuously works to make the current state match that desired state. For example, `replicas: 3` in a Deployment is a desired state, while the actual number of healthy Pods is the current state.

### Interview answer

> Kubernetes continuously reconciles current state with desired state.

---

# 70. Kubernetes DNS

Kubernetes provides internal DNS so applications can discover Services using DNS names rather than hard-coded Pod IP addresses. For example, if you have a Service named `backend` in a namespace, another workload in the cluster can generally access it using a DNS name such as `backend` or a fully qualified service name depending on the namespace. This is one reason Services are preferred over direct Pod IP connections.

---

# 71. Kubernetes Architecture — Full Picture

```text
                    Kubernetes Cluster
                           │
             ┌─────────────┴─────────────┐
             │                           │
       CONTROL PLANE                 WORKER NODES
             │                           │
      ┌──────┼──────┐              ┌─────┴─────┐
      │      │      │              │           │
   API     etcd  Scheduler       kubelet    Runtime
   Server        Controller          │
               Manager              Pods
                                      │
                         ┌────────────┼────────────┐
                         │            │            │
                      Container    Container    Container
```

---

# 🔥 Kubernetes Application Flow

Understand this flow very clearly for interviews:

```text
Developer
    ↓
Dockerfile
    ↓
Docker Image
    ↓
Container Registry
    ↓
Kubernetes Deployment
    ↓
ReplicaSet
    ↓
Pods
    ↓
Service
    ↓
Ingress / LoadBalancer
    ↓
Users
```

For example, suppose your application is a Java Spring Boot application. You first create a Docker image containing the application, push it to a registry such as Amazon ECR, and then create a Kubernetes Deployment referencing that image. Kubernetes creates the required Pods, the Service provides a stable endpoint for those Pods, and an Ingress or cloud load balancer can expose the application externally.

---

# 🧠 Kubernetes Interview Questions

### 1. What is Kubernetes?

> Kubernetes is a container orchestration platform used to automate deployment, scaling, networking, and management of containerized applications.

### 2. What is a Pod?

> A Pod is the smallest deployable unit in Kubernetes and contains one or more containers that share networking and optionally storage.

### 3. What is a Node?

> A Node is a machine in a Kubernetes cluster that runs Pods.

### 4. What is a Cluster?

> A Cluster is a group of machines managed by Kubernetes to run containerized workloads.

### 5. What is the Control Plane?

> The Control Plane manages the Kubernetes cluster and maintains its desired state.

### 6. What is kubelet?

> kubelet is the node-level agent that ensures Pods assigned to its node are running correctly.

### 7. What is Scheduler?

> Scheduler decides which node should run an unscheduled Pod.

### 8. What is etcd?

> etcd is Kubernetes' distributed key-value store containing cluster state.

### 9. What is Deployment?

> Deployment manages replicated application Pods and supports controlled updates and rollbacks.

### 10. What is ReplicaSet?

> ReplicaSet maintains the desired number of matching Pods.

### 11. What is Service?

> Service provides a stable network endpoint for a group of Pods.

### 12. Why do we need Service?

> Pod IP addresses can change, so Service provides stable discovery and access to Pods.

### 13. What is ConfigMap?

> ConfigMap stores non-sensitive configuration separately from application images.

### 14. What is Secret?

> Secret is designed to store sensitive configuration such as passwords and tokens.

### 15. What is Ingress?

> Ingress defines HTTP/HTTPS routing rules from outside the cluster to Services.

### 16. What is HPA?

> HPA automatically adjusts the number of Pod replicas based on configured metrics.

### 17. What is StatefulSet?

> StatefulSet manages applications requiring stable identity and persistent storage associations.

### 18. What is DaemonSet?

> DaemonSet ensures a Pod runs on each eligible node.

### 19. What is Job?

> Job runs a workload that is expected to complete successfully.

### 20. What is CronJob?

> CronJob creates Jobs according to a schedule.

### 21. What is PV?

> PersistentVolume represents persistent storage available to Kubernetes.

### 22. What is PVC?

> PersistentVolumeClaim is a workload's request for persistent storage.

### 23. What is RBAC?

> RBAC controls who or what can perform which Kubernetes API operations.

### 24. What is Helm?

> Helm is a package manager for Kubernetes that uses reusable charts to install and manage applications.

### 25. What is self-healing?

> Kubernetes continuously compares desired and actual state and takes corrective action when they differ.

---

# 🚨 Kubernetes Troubleshooting Interview Scenario

If the interviewer asks:

> **"Your Pod is not running. How will you troubleshoot it?"**

Don't immediately guess. Follow a logical process:

```bash
kubectl get pods
```

First check the Pod status. If it is not running normally:

```bash
kubectl describe pod <pod-name>
```

Check conditions and Events. Then:

```bash
kubectl logs <pod-name>
```

Check application logs. If the container is running and you need to inspect it:

```bash
kubectl exec -it <pod-name> -- sh
```

Then check the Deployment:

```bash
kubectl get deployment
```

Check Services:

```bash
kubectl get svc
```

Check nodes:

```bash
kubectl get nodes
```

And check recent events:

```bash
kubectl get events
```

### Interview answer

> "I would first check the Pod status with `kubectl get pods`. Then I would use `kubectl describe pod` to inspect conditions and events, followed by `kubectl logs` to investigate application errors. If necessary, I would use `kubectl exec` to inspect the container. Then I would verify the Deployment, Service, node health, resource availability, image, configuration, and recent cluster events."

---

# 🧠 Final Kubernetes Memory Map

```text
                    KUBERNETES
                         │
          ┌──────────────┴──────────────┐
          │                             │
     CONTROL PLANE                  WORKER NODE
          │                             │
     ┌────┼────┐                   ┌────┴────┐
     │    │    │                   │         │
    API  etcd Scheduler          kubelet   Runtime
     │
 Controller
 Manager
          │
          ↓
      Deployment
          ↓
      ReplicaSet
          ↓
        Pods
          ↓
      Containers
          │
      ┌───┴────┐
      │        │
   Service   Volume
      │
   Ingress
      │
    Users
```

## ⭐ Most Important Things to Master First

For a **Cloud/DevOps fresher**, learn these in this order:

```text
1. Kubernetes architecture
2. Cluster
3. Control Plane
4. Worker Node
5. Pod
6. Deployment
7. ReplicaSet
8. Service
9. Labels & Selectors
10. Namespace
11. ConfigMap
12. Secret
13. Volumes / PV / PVC
14. Ingress
15. Probes
16. Resource Requests/Limits
17. HPA
18. StatefulSet
19. DaemonSet
20. Job / CronJob
21. RBAC
22. Helm
23. Scheduling
24. Troubleshooting
25. Kubernetes + Docker + CI/CD + AWS
```

### 🔑 One paragraph to memorize for interviews

> **Kubernetes is a container orchestration platform that manages containerized applications across a cluster of machines. The Control Plane manages the cluster, while Worker Nodes run application Pods. A Pod is the smallest deployable unit and contains one or more containers. Deployments manage replicated Pods through ReplicaSets, while Services provide stable network access to Pods. ConfigMaps and Secrets provide configuration, PersistentVolumes and PersistentVolumeClaims provide persistent storage, and Ingress provides HTTP/HTTPS routing into the cluster. Kubernetes also provides self-healing, rolling updates, rollbacks, autoscaling, scheduling, and declarative desired-state management. In a DevOps environment, Docker is commonly used to build container images, a registry stores those images, and Kubernetes deploys and manages them in production.**

**Core flow to remember:**
**Docker Image → Registry → Deployment → ReplicaSet → Pod → Container → Service → Ingress → User**.
