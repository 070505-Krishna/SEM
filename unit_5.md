# Unit V – Cloud Native Microservices, Kubernetes, GitOps, CI/CD, Serverless

**Syllabus:** Cloud Native Micro services: How and Why, Kubernetes Architecture Overview, Stateless and Stateful micro services. GitOps: Cloud Native Continuous Delivery, Creating CI/CD Pipelines for Microservices, CI/CD tools: Jenkins, Spinnaker, Argo CD, GitHub Actions, GitLab CI. What is Serverless Computing?, Getting Started with AWS Lambda, Azure Functions, Google Cloud Functions.

---

## 0. Unit V – Priority Map (supplied papers)

Unit V: 8 of 44 questions, and it dominates the newest papers (4 of 11 questions in Mid-2). The syllabus highlights this unit (newer content).

| Topic | Actual appearances | Priority |
|---|---|---|
| Kubernetes architecture & components | PYQ-B Q8b, Mid-2 Q8a (repeated) | **P1** |
| Cloud-native microservices (why; vs monolith) | PYQ-B Q4, Mid-2 Q4 (repeated) | **P1** |
| Serverless / AWS Lambda / Azure / Google Functions | PYQ-B Q8a, Mid-2 Q8b | **P1** |
| CI/CD pipeline stages | PYQ-B Q5 | **P1** |
| Jenkins vs Argo CD | Mid-2 Q5 | **P1** |
| GitOps, Spinnaker, GitHub Actions, GitLab CI | none | P2 (expected) |
| Stateless vs stateful microservices | none | P2 (expected) |

---

## 5.1 Cloud-Native Microservices – How and Why

### A. Definition
**Microservices** architecture structures an application as a collection of small, independently deployable services, each owning one business capability and its data, communicating via lightweight APIs (REST/gRPC/messaging). **Cloud-native** means designed to exploit cloud properties: containers, dynamic orchestration, elasticity, automation and resilience.

### B. Simple Explanation
Instead of one giant program (monolith), the app is split into small programs (login, cart, payment, search) that can be built, updated and scaled separately.

### C. Detailed Explanation
**How:** decompose by business domain (DDD) → one service = one container image → each has own DB → expose APIs via API gateway → deploy on Kubernetes → automate with CI/CD → observe with logs/metrics/tracing → resilience (retries, circuit breaker, health probes).
**Why:** independent deployment and scaling; fault isolation; technology freedom; faster releases; team autonomy; efficient resource use; fits containers/cloud elasticity.
**Challenges:** distributed-system complexity, network latency, data consistency, testing, monitoring, operational overhead.

### E. Microservices vs Monolith (Mid-2 Q4)
| | Monolith | Microservices |
|---|---|---|
| Deployment | Whole app together | Independent per service |
| Scaling | Scale entire app | Scale only hot service |
| Fault | One bug can bring down all | Isolated failures |
| Tech | Single stack | Polyglot |
| Complexity | Simple initially, grows hard | Distributed complexity from start |
| Latency | In-process calls | Network calls |
| Data | One shared DB | DB per service |

**Why cloud-native microservices are designed around independent deployment and scaling:** demand differs per feature (e.g., search ≫ profile); independent scaling saves cost; independent deployment enables continuous delivery and small blast radius.
**When a monolith is more appropriate:** small team/simple domain; early-stage startup/MVP with unclear boundaries; low/uniform traffic; need strong transactional consistency (ACID); tight latency requirements; limited DevOps/ops skills; cost of operating distributed system outweighs benefits.

```
 Client -> [API Gateway] -> [Auth svc] [Catalog svc] [Cart svc] [Payment svc]
                               |DB        |DB          |DB         |DB
 (each: container, own DB, independent CI/CD, scaled separately on Kubernetes)
```
**Keywords:** microservice, container, Docker, API gateway, service discovery, circuit breaker, DDD, polyglot, DB-per-service, 12-factor, observability.

---

## 5.2 Kubernetes Architecture Overview

### A. Definition
**Kubernetes (K8s)** is an open-source **container orchestration platform** that automates deployment, scaling, networking, self-healing and management of containerised applications across a cluster.

### B. Simple Explanation
A "manager" that decides which machine runs which container, restarts crashed ones and adds more copies when load rises.

### C. Components
**Control plane (master):**
- **API Server (kube-apiserver):** front door; REST API; validates/authenticates requests; only component that talks to etcd.
- **etcd:** distributed, consistent key-value store holding the cluster's desired and current state.
- **Scheduler:** assigns unscheduled Pods to suitable nodes (resources, affinity, taints).
- **Controller Manager:** runs controllers (ReplicaSet, Node, Deployment) in reconciliation loops: *desired state vs actual state*.
- (Cloud controller manager – cloud-specific).

**Worker node:**
- **kubelet:** node agent; ensures containers in assigned Pods run and healthy; reports status.
- **Container runtime:** containerd/CRI-O; pulls images, runs containers.
- **kube-proxy:** networking rules/Service load balancing.
- **Pod:** smallest unit (one or more containers sharing network/storage). Others: Deployment, ReplicaSet, Service, Ingress, ConfigMap, Secret, PersistentVolume, Namespace.

### D. Diagram (easy to draw)
```
 kubectl / CI-CD
       |
 +-----v---------------- CONTROL PLANE -----------------+
 |  API Server <--> etcd                                 |
 |     ^  ^                                              |
 |  Scheduler   Controller Manager                       |
 +-----+------------------------------------------------+
       | (watch / assign / status)
 +-----v--------- WORKER NODE 1 ---+   +--- WORKER NODE 2 ---+
 | kubelet  kube-proxy             |   | kubelet kube-proxy  |
 | Container runtime               |   | Container runtime   |
 | [Pod:c1,c2] [Pod:c3]            |   | [Pod:c4] [Pod:c5]   |
 +---------------------------------+   +---------------------+
```

### Pod-failure scenario (Mid-2 Q8a)
1. A node crashes / pod dies → kubelet/node heartbeat stops; **Node controller** marks node NotReady; **ReplicaSet/Deployment controller** sees actual replicas (2) < desired (3) via API server watch.
2. Controller creates a new Pod object → stored in **etcd** via API server.
3. **Scheduler** notices unscheduled Pod → selects healthy node → binds Pod (writes to API server/etcd).
4. **kubelet** on chosen node sees the assignment → asks **container runtime** to pull image and start containers → runs probes.
5. kubelet reports Running; **kube-proxy/Service** updates endpoints so traffic flows to new Pod. Desired state restored.

### Stateless vs Stateful microservices
| | Stateless | Stateful |
|---|---|---|
| State | No local persistent state; externalised (DB/cache) | Keeps data across requests/restarts |
| Scaling | Easy – any replica serves any request | Hard – data replication/consistency |
| Recovery | Replace instantly | Needs same data/identity |
| K8s object | Deployment + Service | **StatefulSet** + PersistentVolume + headless Service |
| Example | Web API, auth token check | Database, Kafka, Redis cluster |

**Keywords:** Pod, node, control plane, etcd, scheduler, kubelet, kube-proxy, controller, reconciliation, ReplicaSet, StatefulSet, PV/PVC.

---

## 5.3 GitOps – Cloud-Native Continuous Delivery

### A. Definition
**GitOps** is an operational model where **Git is the single source of truth for the declarative desired state** of infrastructure and applications; an automated agent continuously reconciles the live cluster to match Git.

### B. Simple Explanation
You change a YAML file in Git (pull request); the system notices and updates the cluster automatically. Rollback = `git revert`.

### C. Principles & flow
Declarative config • versioned & immutable in Git • automatically pulled/applied by agent (pull-based) • continuous reconciliation/drift correction.
```
 Developer -> PR -> [Git repo: app code]-> CI (build, test, push image)
                                   |  updates manifest (new image tag)
                            [Git repo: config/manifests]
                                   ^ pulls & compares
                          [GitOps agent: Argo CD / Flux] -> Kubernetes cluster (sync)
```
**Benefits:** audit trail, easy rollback, consistency, security (no cluster credentials in CI), faster recovery. **Tools:** Argo CD, Flux.
**Keywords:** declarative, single source of truth, drift, reconciliation, pull-based, manifest, Argo CD, Flux.

---

## 5.4 CI/CD Pipelines for Microservices

### A. Definition
**CI (Continuous Integration):** frequently merging code to a shared repo with automated build and tests. **CD:** Continuous *Delivery* (always releasable; manual approval to prod) / Continuous *Deployment* (automatic to prod).

### B. Stages of a CI/CD pipeline (PYQ-B Q5)
1. **Source/Commit** – push to Git, trigger.
2. **Build** – compile, package, build Docker image.
3. **Test** – unit, integration, static analysis, security scan.
4. **Artifact/Release** – push image to registry (versioned).
5. **Deploy** – to staging then production (Kubernetes, blue-green/canary/rolling).
6. **Operate/Monitor** – metrics, logs, rollback on failure.
```
 Code -> Build -> Test -> Package/Registry -> Deploy(Stage) -> Approve -> Deploy(Prod) -> Monitor
 |<---------------- CI ------------------>|<------------------ CD ----------------------->|
```
**For microservices:** one pipeline per service (independent repos/pipelines), contract tests, container images, Helm/Kustomize, canary/blue-green, rollback.

---

## 5.5 CI/CD Tools

| Tool | Type | Key points |
|---|---|---|
| **Jenkins** | CI server (also CD via plugins) | Open-source; master/agent; **Jenkinsfile** (pipeline-as-code); 1000+ plugins; **push-based**; builds, tests, packages, can trigger deployments |
| **Spinnaker** | Multi-cloud CD | Netflix-origin; deployment pipelines, canary/blue-green strategies, multi-cloud (AWS, GCP, K8s); not a CI tool |
| **Argo CD** | GitOps CD for Kubernetes | Declarative, **pull-based**, watches Git, auto-sync, drift detection, rollback, UI |
| **GitHub Actions** | CI/CD service in GitHub | YAML workflows in `.github/workflows`, event-triggered, runners, marketplace actions |
| **GitLab CI/CD** | Integrated in GitLab | `.gitlab-ci.yml`, stages/jobs, runners, built-in registry, Auto DevOps |

### Jenkins vs Argo CD (Mid-2 Q5)
| | Jenkins | Argo CD |
|---|---|---|
| Primary responsibility | **CI** (build, test, package) – can script CD | **CD** (deploy & sync to Kubernetes) |
| Execution model | Pipeline jobs run on agents; **push-based**, imperative (scripts) | Controller inside cluster; **pull-based**, declarative, continuous reconciliation |
| Source of truth | Pipeline/job config | Git repository of manifests |
| Role in cloud-native delivery | Builds image, runs tests, updates manifest | Deploys image from Git to cluster, detects drift |
**They complement, not replace:** Jenkins builds/tests/pushes image and commits new image tag to the config repo; Argo CD detects the commit and deploys it. Separation of concerns improves security and auditability.
**Keywords:** Jenkinsfile, agent, plugin, push vs pull, GitOps, sync, drift, rollback, runner, pipeline-as-code.

---

## 5.6 Serverless Computing

### A. Definition
**Serverless computing** is a cloud execution model where the provider fully manages servers and automatically allocates resources; developers deploy **functions/code (FaaS)** that run in response to events and are billed only for execution time (pay-per-invocation).

### B. Simple Explanation
You upload a function; the cloud runs it when something happens (file upload, HTTP request) and you pay only for the milliseconds it ran. Servers still exist, but you never manage them.

### C. Characteristics
No server management • event-driven • automatic scaling (to zero) • pay-per-use • stateless, short-lived functions • built-in high availability.
**Limits:** cold-start latency, execution time limits (e.g., Lambda 15 min), memory/package limits, statelessness, vendor lock-in, debugging difficulty, not for long-running/steady heavy workloads.

### D. Diagram
```
 Event source (HTTP/API Gateway, S3 upload, queue, timer)
        |
        v
 [ Function (Lambda / Azure Func / Cloud Function) ] --> DynamoDB / S3 / other services
   (provider provisions container, scales per event, bills per ms)
```

### Getting started – three platforms
| | AWS Lambda | Azure Functions | Google Cloud Functions |
|---|---|---|---|
| Triggers | API Gateway, S3, DynamoDB Streams, SQS/SNS, EventBridge, schedule | HTTP, Blob, Queue, Timer, Event Grid, Service Bus | HTTP, Pub/Sub, Cloud Storage, Firestore, Scheduler |
| Languages | Python, Node.js, Java, Go, .NET, Ruby, containers | C#, JS, Python, Java, PowerShell | Node.js, Python, Go, Java, .NET, Ruby, PHP |
| Hosting plans | Memory-based config; provisioned concurrency | Consumption, Premium, Dedicated | Event-driven functions (2nd gen on Cloud Run) |
| Orchestration | Step Functions | Durable Functions | Workflows |
| Limits | 15-min timeout (check current docs) | Consumption plan time limit (check docs) | Time limit varies by generation (check docs) |

**AWS Lambda in detail (PYQ-B Q8a):**
- **Concepts:** function (code + config), handler, runtime, event, trigger, execution environment (microVM/container), layers, concurrency, versions & aliases, IAM execution role, environment variables.
- **Working:** upload code → configure memory (128 MB–10 GB; CPU scales with memory), timeout, role → connect trigger → on event Lambda creates execution environment (cold start) or reuses warm one → runs handler → returns/logs to CloudWatch → scales concurrently.
- **Pricing:** number of requests + duration (GB-seconds); free tier.
- **Use cases:** image thumbnailing on S3 upload, REST APIs with API Gateway, stream processing, scheduled jobs, chatbots/IoT backend.
- **Getting started:** console → Create function → choose runtime → write handler (e.g., Python `def lambda_handler(event, context): return {...}`) → add trigger → Test → monitor in CloudWatch.

### Serverless functions vs continuously running microservices (Mid-2 Q8b)
| | Serverless functions | Always-running microservices (containers/VMs) |
|---|---|---|
| Execution model | Event-triggered, short-lived | Long-running processes |
| Scaling | Automatic, per request, to zero | Autoscaler/HPA, min replicas running |
| Infrastructure mgmt | None | Cluster/containers managed by team |
| Startup latency | Cold start possible | Low (already running) |
| Constraints | Time/memory/package limits, stateless | Few limits, can be stateful |
| Cost | Pay per use; cheap for sporadic | Pay for provisioned capacity; cheaper for steady high load |
| Vendor dependency | High (platform-specific triggers/APIs) | Lower (portable with containers/K8s) |
**Recommendation for unpredictable event-driven workloads:** serverless functions (queue/event triggered) — automatic scaling and no idle cost; mitigate cold starts using provisioned concurrency; use an abstraction/open frameworks (Serverless Framework, Knative) or keep business logic portable to reduce lock-in; fall back to containers for long/steady jobs (hybrid).

**Keywords:** FaaS, BaaS, event-driven, trigger, cold start, handler, concurrency, pay-per-invocation, execution role, Step Functions, Durable Functions.
