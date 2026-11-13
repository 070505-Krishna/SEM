# Mid-2 Question Paper – Answers

**Scan notes (nothing invented):**
- The header of Part A is **not visible** in the scan, so **Part A marks per question are not stated** (not assumed). Part A questions are scenario/analysis type, so answers are ~120–180 words.
- Part B: "PART-B (Answer any TWO complete questions) 2 * 10 = 20 Marks" → **10 marks each**.
- Some questions have pen strike-marks; all are answered. Question text is copied as printed (the first words of Q1 and Q3 are slightly cut by the pen mark at the left margin, otherwise legible).

| Q | Unit | Topic |
|---|---|---|
| 1 | III | Relational vs NoSQL |
| 2 | III | Security threats, IAM, least privilege, audit logging |
| 3 | IV | Portability vs interoperability |
| 4 | V | Microservices vs monolith |
| 5 | V | Jenkins vs Argo CD |
| 6a | III | DB latency, replication, partitioning, caching |
| 6b | III | CDN caching security |
| 7a | IV | Multi-cloud interoperable architecture |
| 7b | IV | Cloud-management solution |
| 8a | V | Kubernetes architecture & pod failure |
| 8b | V | Serverless vs microservices |

---
## Part A (marks not visible in scan)

### Q1. How do relational and NoSQL database technologies differ in consistency guarantees, schema flexibility, and horizontal scalability? Recommend a suitable approach for a globally distributed application.
**Unit III – Database technology**

| Aspect | Relational (SQL) | NoSQL |
|---|---|---|
| Consistency | Strong, **ACID** transactions | Usually **BASE** – eventual consistency, tunable (quorum) |
| Schema | Fixed, predefined; changes are costly | Flexible/schema-less (key-value, document, column, graph) |
| Horizontal scalability | Mainly vertical; scale-out via sharding/replicas is complex | Built for horizontal scale-out and partitioning |
| Examples | MySQL, PostgreSQL, Aurora, Cloud Spanner | DynamoDB, Cassandra, MongoDB, Cosmos DB |

By the **CAP theorem**, a distributed system must choose consistency or availability during network partitions.
**Recommendation for a globally distributed application:** a **polyglot/hybrid approach** – a relational or NewSQL database (e.g., Aurora Global, Spanner) for strongly consistent transactional data (orders, payments); a multi-region NoSQL store (DynamoDB global tables/Cassandra/Cosmos DB) for high-volume, low-latency data (catalogue, sessions, profiles) with tunable consistency; regional read replicas and a cache (Redis) near users; and shard by region/customer ID.

### Q2. A company migrates sensitive customer records to a public cloud. Identify the major security threats and explain how identity and access management, least-privilege access, and audit logging mitigate them.
**Unit III – Security issues**

**Major threats:** data breach/leakage; misconfiguration (public buckets, open ports); insecure APIs; account/credential hijacking & phishing; insider threats; multi-tenancy attacks (VM escape, side channels); DoS/DDoS; data loss/insecure deletion; compliance violations and vendor lock-in.
**Mitigation**
- **IAM:** verifies *who* the user/service is (MFA, SSO, federation) and *what* they may do (RBAC/policies); prevents account takeover and unauthorised access; centralises key/credential rotation.
- **Least-privilege access:** each user, role and service gets only the minimum permissions for the minimum time (just-in-time access, no wildcard policies), limiting the damage from stolen credentials, insider misuse or misconfiguration.
- **Audit logging:** records who accessed what and when (CloudTrail, Azure Monitor); tamper-proof logs feed SIEM alerts for anomaly detection, give forensic evidence after incidents and prove compliance.
Together: IAM prevents, least privilege limits, logging detects and investigates. Add encryption (at rest/in transit, KMS), network isolation (VPC), patching and backups.

### Q3. Differentiate cloud portability from cloud interoperability. Identify how vendor-specific APIs, managed services, and proprietary data formats can complicate migration between cloud providers.
**Unit IV – Portability & interoperability** *(definitions also asked in PYQ-B Q3)*

| | Portability | Interoperability |
|---|---|---|
| Meaning | Ability to **move** apps/data from one cloud to another with minimal change | Ability of different cloud systems to **work together/exchange data** |
| Focus | Migration, avoiding lock-in | Cooperation in hybrid/multi-cloud operation |
| Example | Moving workloads AWS → Azure | Private-cloud app using public-cloud storage |

**Migration complications**
- **Vendor-specific APIs/SDKs** (Lambda vs Azure Functions, different IAM/networking APIs): code must be rewritten and tested.
- **Managed services** (DynamoDB, BigQuery, Cosmos DB, proprietary ML services) have no exact equivalent; features, consistency and limits differ → redesign.
- **Proprietary data formats/schemas** (storage layouts, VM image formats AMI/VHD/VMDK, DB engines) require conversion; large data transfer and **egress fees** add cost/time.
- Different monitoring, identity and billing models also hinder migration.
Remedies: containers/Kubernetes, open standards (OVF, TOSCA, OIDC), open-source databases, abstraction layers, Terraform.

### Q4. Why are cloud-native microservices designed around independent deployment and scaling? Under what conditions might a monolithic architecture be more appropriate?
**Unit V – Microservices**

**Why independent deployment and scaling:** different features see different load (e.g., search ≫ profile update), so scaling only the busy service saves cost; independent deployment lets small teams release frequently, limits blast radius of a bad release or failure, enables rollback per service and technology choice, and fits containers/Kubernetes autoscaling and CI/CD.
**Monolith is more appropriate when:** the application is small/simple or a startup MVP with unclear domain boundaries; the team is small or lacks DevOps/orchestration skills; traffic is low/uniform so scaling parts separately gives no benefit; strong ACID consistency across operations is needed (avoiding distributed transactions); in-process low latency is critical; operating a distributed system would cost more than the benefits.
(See `unit_5.md` §5.1 for comparison table.)

### Q5. Compare Jenkins and Argo CD in terms of their primary responsibilities, execution models, and roles in cloud-native delivery. Can they complement rather than replace each other? Justify.
**Unit V – CI/CD tools**

| | Jenkins | Argo CD |
|---|---|---|
| Primary responsibility | **CI** – build, test, package (CD via scripts/plugins) | **CD** – deploy/sync applications to Kubernetes |
| Execution model | Pipeline jobs on controller/agents, **push-based**, imperative (Jenkinsfile scripts) | In-cluster controller, **pull-based**, declarative; reconciles cluster with Git |
| Source of truth | Pipeline/job definitions | Git repo of manifests (GitOps) |
| Role | Create tested container image & trigger/update config | Deploy, detect drift, self-heal, rollback |

**Yes – complementary.** Jenkins builds, tests and scans code, pushes the image to a registry and commits the new image tag to the config repository; Argo CD detects the Git change and deploys it to the cluster, continuously correcting drift. This separation gives better security (no cluster credentials in CI), auditability (Git history) and easy rollback. Replacing one with the other loses either strong CI features (Jenkins) or GitOps delivery guarantees (Argo CD).

---
## Part B (Answer any TWO – 10 marks each)

### Q6.a. A globally distributed e-commerce application experiences high database latency, inconsistent reads, and increasing storage costs. Analyze how database replication, partitioning, caching, and the choice between relational and NoSQL databases can address these challenges. Justify a suitable combination of techniques.
**Unit III – Database technology | 10 marks**

**Problem analysis:** latency (distance between users and DB, heavy load), inconsistent reads (replica lag, eventual consistency), rising storage cost (data growth, hot data kept on expensive storage).
**1. Replication**
- Read replicas in regions near users → lower read latency, offload primary.
- *Issue:* asynchronous replica lag → stale/inconsistent reads. *Fix:* read-your-writes/session consistency, quorum reads (R+W>N), read critical data (cart/payment) from primary; multi-master/global tables for low write latency with conflict resolution.
**2. Partitioning (sharding)**
- Split data horizontally (by customer ID/region/product) → smaller per-node data, parallelism, region-local data (data residency), lower latency. *Issues:* hot shards, cross-shard joins/transactions – design good shard keys.
**3. Caching**
- Redis/Memcached/CDN for product pages, sessions, hot reads (cache-aside, TTL, invalidation) → large latency reduction and reduced DB load; risk of stale data – set TTL and invalidate on update.
**4. Relational vs NoSQL**
- Relational (or NewSQL): orders, payments, inventory – ACID and joins. NoSQL (DynamoDB/Cassandra/MongoDB): catalogue, sessions, reviews, recommendations – flexible schema, horizontal scale, multi-region replication, low latency.
**5. Storage cost reduction:** tiering/archival of old orders to cold storage, compression, TTL for transient data, right-sized replication factor.
**Suggested combination (justified)**
```
 Users -> CDN/Cache (Redis) ->  App
              | reads                       | writes
   Regional read replicas (async)   Primary relational/NewSQL (orders, payments, ACID)
   Sharded NoSQL (catalogue, sessions, profiles)  -- multi-region replication
   Old data -> archive/cold storage
```
- Strong consistency where money/inventory is involved; eventual consistency + caching where speed matters; sharding for scale; tiering for cost.
**Conclusion:** no single technique solves all three issues; a layered polyglot design balances latency, consistency and cost (CAP trade-off).

### Q6.b. A cloud application uses a CDN to deliver both public website assets and personalized user information. Identify the security and privacy risks associated with caching sensitive responses. Propose appropriate cache-control policies, access restrictions, and content-delivery strategies.
**Unit III – CDN | 10 marks**

**CDN recap:** edge servers (PoPs) cache content from the origin and serve users from the nearest location (cache hit) or fetch from origin (cache miss, TTL).
**Risks of caching sensitive/personalised responses**
1. **Data leakage:** a user-specific page (account, invoice) cached and served to another user (shared cache).
2. **Web cache deception / cache poisoning:** attacker tricks the cache into storing private or malicious content.
3. **Stale or persistent sensitive data** after logout/permission changes; data stored at third-party edge locations (privacy/compliance, residency issues).
4. **Token/session leakage** in cached URLs or headers; unauthorised direct access to origin; unencrypted traffic.
5. Mis-set cache keys (ignoring cookies/Authorization) and DDoS/abuse of cache.
**Cache-control policies**
- Public static assets (JS, CSS, images): `Cache-Control: public, max-age=31536000, immutable` with versioned filenames.
- Personalised/sensitive: `Cache-Control: private` or `no-store`; add `Vary: Cookie/Authorization` or bypass cache for authenticated paths; short TTL if caching is needed.
- Use ETag/validation; purge or invalidate on logout/permission change.
**Access restrictions:** signed URLs/signed cookies and token authentication at edge; origin access control so only the CDN can reach the origin; HTTPS/TLS everywhere; WAF, rate limiting, geo-restriction; encryption and key management; limit logging of sensitive data.
**Content-delivery strategies:** separate domains/paths (`static.example.com` via CDN, `/api/user` uncached); keep the HTML shell cached and load personalised data via authenticated API calls (or edge-side includes); correct cache keys; regional edge selection for compliance; monitoring and regular testing for cache-deception vulnerabilities.
```
 Public assets  --> CDN edge (cache, long TTL)
 Personal data  --> bypass CDN / private,no-store --> Origin (auth, TLS)
```
**Conclusion:** cache public content aggressively but never cache user-specific responses in shared caches.

### Q7.a. A company operates workloads across a private cloud and two public cloud providers. Design a high-level interoperable architecture addressing common deployment interfaces, identity federation, data exchange, monitoring, and network connectivity. Discuss the limitations of achieving complete interoperability.
**Unit IV – Interoperability | 10 marks**

**Architecture**
```
        +--------- Unified management plane: IaC (Terraform/TOSCA), Kubernetes, CI/CD ---------+
        | Identity federation (IdP: SAML/OIDC)   Central monitoring (Prometheus/Grafana/OTel)   |
        +-------------------+-----------------------+-------------------------+----------------+
                            |                       |                         |
                  +---------v-------+     +---------v-------+       +---------v-------+
                  |  Private cloud  |     | Public cloud 1  |       | Public cloud 2  |
                  | (OpenStack/K8s) |     |   (e.g., AWS)   |       |  (e.g., Azure)  |
                  +--------+--------+     +--------+--------+       +--------+--------+
                           +--- VPN / Direct Connect / ExpressRoute / overlay network ---+
                           +--- API gateway, message bus (Kafka), S3-compatible storage -+
```
1. **Common deployment interfaces:** containers + Kubernetes on all three; infrastructure-as-code (Terraform/TOSCA templates); standard CI/CD; use open APIs and avoid proprietary services in the portable core.
2. **Identity federation:** central IdP with SSO via SAML/OIDC, federated roles/RBAC mapping, MFA, uniform policies, service identities (SPIFFE/workload identity).
3. **Data exchange:** open formats (JSON, Parquet), standard protocols (REST/gRPC, S3-compatible API), message queues/event bus, API gateway, replication/ETL; encryption in transit/at rest with consistent key management.
4. **Monitoring:** vendor-neutral stack (OpenTelemetry, Prometheus, Grafana, ELK) aggregating metrics/logs/traces from all clouds; unified alerting and cost dashboards.
5. **Network connectivity:** site-to-site VPN or dedicated links (Direct Connect, ExpressRoute, Interconnect) with private peering, overlay/service mesh, global DNS/load balancing, consistent IP plan and firewall policy.
**Limitations of complete interoperability**
- Proprietary services/APIs (serverless, managed DBs, AI services) differ → "lowest common denominator" loses unique features.
- Immature/competing standards; incompatible versions.
- Latency, bandwidth limits and **egress charges** for cross-cloud traffic.
- Different security, IAM, compliance and billing models.
- Data gravity (large data hard to move) and consistency across clouds.
- Higher operational complexity, skills and tooling cost; vendor business interests.
**Conclusion:** achieve practical (not complete) interoperability by standardising on containers, open APIs and federation while accepting some vendor-specific pieces.

### Q7.b. A cloud application must support predictable workloads during normal operation and sudden demand during promotional events. Explain how automated provisioning, orchestration, monitoring, policy-based management, and capacity planning can be integrated into a cloud-management solution.
**Unit IV – Cloud management | 10 marks**

**Introduction:** a cloud-management solution automates the lifecycle of cloud resources so that the application meets its SLA at minimum cost.
**Components and integration**
1. **Capacity planning:** from historical data, size the *baseline* for normal load (reserved/committed instances for predictable demand) and plan *burst* capacity (on-demand/spot, pre-warmed) for promotions; schedule scale-out before events.
2. **Automated provisioning:** IaC templates (CloudFormation/Terraform) and auto scaling groups/Kubernetes HPA create VMs/containers on demand through APIs, using standard images.
3. **Orchestration:** coordinates multi-step workflows – create network, instances, database, configure load balancer, deploy application, run tests, register health checks (Kubernetes, Ansible, workflow engines); handles dependencies and rollback.
4. **Monitoring:** collects metrics/logs/traces (CPU, latency, queue length, request rate, cost); raises alarms; feeds scaling and capacity models; verifies SLA/SLOs.
5. **Policy-based management:** declarative rules, e.g., *"maintain min 4 instances; scale out when CPU>65% or request latency>300 ms; max 100 instances; scheduled scale-out at promotion start; shut down idle resources; enforce tagging/security/budget limits"*.
```
 Capacity planner (forecast) --> Policy engine <-- SLA/cost/security rules
        |                              |
        v                              v
 Provisioning (IaC/APIs) --> Orchestrator --> Resources (VMs/containers/DB) + Load balancer
        ^                                              |
        +------------ Monitoring & analytics <---------+   (feedback loop, billing/SLA reports)
```
**Handling both situations:** *Normal operation* – baseline capacity, minor reactive scaling, cost optimisation. *Promotion* – scheduled/predictive scale-out beforehand, reactive scaling for unexpected surge, load balancer distribution, scale-in after the event.
**Benefits:** SLA compliance, cost efficiency, reduced manual effort, quick response, auditability.
**Conclusion:** integrating these five functions into a closed monitoring-policy-provisioning loop gives automatic, cost-effective elasticity.

### Q8.a. Explain the architecture of Kubernetes by describing the roles of the API server, scheduler, controller manager, etcd, kubelet, and container runtime. Using a pod-failure scenario, illustrate how the control plane and worker-node components cooperate to restore the desired state.
**Unit V – Kubernetes | 10 marks** *(Compare PYQ-B Q8b, which asks to draw and explain Kubernetes.)*

**Definition:** Kubernetes is an open-source container-orchestration system that keeps a cluster in a **declared desired state** using a control plane and worker nodes.
```
 kubectl --> +-------------- CONTROL PLANE --------------+
             |  API Server <-> etcd                       |
             |  Scheduler      Controller Manager         |
             +----------------+--------------------------+
                              | watch / bind / status
          +--------- Node 1 --------+   +--------- Node 2 --------+
          | kubelet  kube-proxy     |   | kubelet  kube-proxy     |
          | container runtime       |   | container runtime       |
          | [Pod A1] [Pod B1]       |   | [Pod A2] [Pod B2]       |
          +-------------------------+   +-------------------------+
```
**Roles**
- **API server:** central REST front end; authenticates/validates requests; all components communicate through it; the only one that reads/writes etcd.
- **etcd:** consistent, highly available key-value store holding desired and actual state of the cluster.
- **Scheduler:** watches for Pods without a node and binds each to a suitable node (resources, affinity, taints).
- **Controller manager:** runs controllers (ReplicaSet, Deployment, Node) in loops, comparing actual and desired state and making corrections.
- **kubelet:** agent on each node; receives PodSpecs, instructs the runtime, monitors container health (probes), reports status.
- **Container runtime (containerd/CRI-O):** pulls images and runs/stops containers.
- (kube-proxy: Service networking.)
**Pod-failure scenario** (Deployment wants 3 replicas; one Pod crashes/ its node fails)
1. kubelet reports the failed Pod (or node controller marks node *NotReady* after missed heartbeats); status updated in etcd via API server.
2. **ReplicaSet controller** observes 2 running < 3 desired through its watch on the API server.
3. It creates a new Pod object (stored in **etcd** via API server) – state "Pending".
4. **Scheduler** sees an unscheduled Pod, filters/scores nodes, selects a healthy node and writes the binding.
5. **kubelet** on that node notices the assignment, asks the **container runtime** to pull image and start containers, runs readiness/liveness probes.
6. kubelet reports *Running*; Service endpoints updated (kube-proxy) so traffic reaches the new Pod. Actual state = desired state (3 replicas).
**Conclusion:** the continuous reconciliation loop provides self-healing.

### Q8.b. An organization is evaluating AWS Lambda, Azure Functions, and Google Cloud Functions for an event-driven application. Compare serverless functions with continuously running microservices in terms of execution model, scaling, infrastructure management, startup latency, execution constraints, and vendor dependency. Recommend a suitable approach for processing unpredictable workloads.
**Unit V – Serverless | 10 marks**

**Platforms (brief):** AWS Lambda (triggers: S3, API Gateway, SQS, DynamoDB streams; Step Functions), Azure Functions (HTTP, Blob, Queue, Event Grid; Durable Functions; Consumption/Premium plans), Google Cloud Functions (HTTP, Pub/Sub, Cloud Storage; Workflows). All are FaaS: event-triggered, auto-scaled, pay-per-execution.
| Aspect | Serverless functions | Continuously running microservices |
|---|---|---|
| Execution model | Event-triggered, short-lived, stateless functions | Long-running containers/VMs listening for requests |
| Scaling | Automatic per event, up to concurrency limits, to zero when idle | Autoscaler (HPA)/manual; minimum replicas always running |
| Infrastructure management | Provider manages servers, OS, scaling | Team manages cluster, containers, patching, scaling policies |
| Startup latency | **Cold start** (ms–seconds) after idle | Low – already running |
| Execution constraints | Time limits (e.g., minutes), memory/package size, limited local state, concurrency quotas | Few limits; can hold state, long jobs, custom runtimes |
| Cost | Pay per invocation/ms; cheap for sporadic loads | Pay for provisioned capacity; economical for steady high load |
| Vendor dependency | High – triggers, APIs, event formats are provider-specific | Lower – containers/Kubernetes are portable |
**Recommendation for unpredictable workloads:** use **serverless functions** (queue/event-triggered) as the primary approach because they scale instantly from zero to bursts and incur no idle cost. Mitigate drawbacks: provisioned concurrency/warm pools for latency-sensitive paths; keep business logic in portable libraries and use abstractions (Serverless Framework, Terraform, Knative) to limit lock-in; offload long-running or steady-state components to containerised microservices (hybrid). Choose the provider on trigger ecosystem, existing cloud, pricing and limits.
```
 Event (queue/API/file) -> [Function: auto-scale 0..N] -> Managed DB/Storage
 Steady/long jobs       -> [Containers on Kubernetes]
```
