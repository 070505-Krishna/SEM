# Previous Year Question Papers – Answers

Two previous-year papers were supplied (both in the older pattern: **Part A – 10M, 5 questions; Part B – 20M, answer any TWO**). They are labelled here:
- **PYQ-A** – paper beginning "List out the benefits and challenges of the cloud computing."  (header: "All the Questions are compulsory")
- **PYQ-B** – paper beginning "Why are managed and unmanaged cloud storages, called so?"

**Notes on the scans / marks**
- Part A: 10M total for 5 questions → **2 marks each** (derived from the header, not printed per question).
- Part B: "Answer any TWO", 20M → **10 marks each** (derived).
- Question text is reproduced as printed. PYQ-A Q8(a) prints "meany" – preserved as written. Nothing in the scans was unreadable except small scribbles/stains in the margins.
- **Exam pattern difference:** your semester pattern is 6×2M + 4×12M = 60M. These papers are 10M + 20M. Treat answers as 2-mark / 10-mark; for a 12-mark answer, add one more diagram or a comparison table.
- Cross-references: full notes are in `../02_Unit_Wise_Notes/`.

## Unit-wise index of questions
| Unit | PYQ-A | PYQ-B |
|---|---|---|
| I | Q1, Q2, Q5, Q6a, Q6b, Q8b | Q7a |
| II | Q3, Q4, Q7a, Q8a | Q1 (repeat of A-Q4), Q6a, Q6b |
| IV | Q7b (SLA) | Q2, Q3, Q7b |
| V | – | Q4, Q5, Q8a, Q8b |
| III | – | – |

---
---
# PAPER A (PYQ-A)

## Part A (10M) – All questions compulsory

### Q1. List out the benefits and challenges of the cloud computing.
**Unit I – Benefits & challenges | 2 marks**

**Benefits:** (1) Low upfront cost – CapEx converted to OpEx; (2) Pay-as-you-go billing; (3) Elastic scalability & rapid provisioning; (4) High availability and disaster recovery; (5) Accessible from anywhere; (6) Automatic maintenance/updates; (7) Faster time-to-market.
**Challenges:** (1) Security & privacy of data; (2) Vendor lock-in / portability & interoperability; (3) Dependence on Internet & possible downtime; (4) Compliance and data-residency laws; (5) Limited control/visibility; (6) Unpredictable costs at scale; (7) Performance variability in shared (multi-tenant) environment.
**Keywords:** CapEx/OpEx, elasticity, lock-in, SLA, multi-tenancy.

### Q2. Justify the 'low initial investment' philosophy of cloud computing.
**Unit I – Benefits | 2 marks**

Cloud follows *rent, don't buy*: an organisation does not have to purchase servers, build a data centre, or hire large operations staff before earning revenue.
- No hardware/software purchase → **zero or minimal CapEx**; costs become **OpEx (pay-as-you-go)**.
- No over-provisioning for peak load – resources are scaled with demand.
- Provider shares infrastructure cost across many tenants (**economies of scale**).
- Faster setup (minutes), lower financial risk for startups; resources released when not needed.
*Limitation:* for steady heavy workloads, long-term cost can exceed owned hardware.

### Q3. What is auto scaling? How does it works?
**Unit II – Scaling | 2 marks**

**Auto scaling** is the automatic increase or decrease of computing resources (instances) based on defined policies and real-time demand, without manual intervention.
**Working:** (1) Define an Auto Scaling Group with min/max/desired instances and launch template. (2) Monitoring service collects metrics (CPU, requests). (3) When a metric crosses a threshold (e.g., CPU>70%) a scaling policy triggers **scale-out** – new instances launched and registered to the load balancer; when below threshold (<30%) **scale-in** terminates instances. (4) A cooldown period prevents oscillation.
```
 Users -> [LB] -> [VM][VM][VM]  <- Auto Scaling Group
             Metrics -> Alarm -> Policy: add / remove VM
```

### Q4. Why are managed and unmanaged cloud storages, called so?
**Unit II – Storage | 2 marks** *(Same question appears again as PYQ-B Q1.)*

They are named according to **who is responsible for managing the storage**.
- **Managed storage:** the **cloud provider** handles provisioning, replication, backup, scaling, patching and maintenance. User just stores/retrieves data via API. e.g., Amazon S3, Azure Blob, Google Cloud Storage. → "managed" by the vendor.
- **Unmanaged storage:** provider supplies only raw capacity (e.g., a raw disk attached to a VM); **the user** formats it, installs the file system, configures RAID, backups, scaling and security. e.g., raw EBS volume / instance store, self-built NFS/Ceph on VMs. → "unmanaged" by the vendor, managed by the user.

### Q5. Why is hypervisor also called virtual machine monitor?
**Unit I – Virtualization | 2 marks**

A hypervisor is the software layer that creates and runs virtual machines. It is also called a **Virtual Machine Monitor (VMM)** because it **monitors and controls** every VM: it allocates CPU, memory and I/O, traps privileged instructions from guest OSes, mediates their access to hardware and enforces isolation between VMs. (Popek–Goldberg: a VMM must provide *equivalence, resource control, efficiency*.) The terms are therefore used interchangeably. *Types:* Type-1 (bare-metal: ESXi, Xen, KVM, Hyper-V), Type-2 (hosted: VirtualBox).

---
## Part B (20M) – Answer any TWO (10 marks each)

### Q6.a. Explain about the major server virtualization products and vendors?
**Unit I – Virtualization | 10 marks**

**Introduction:** Server virtualization uses a hypervisor to partition one physical server into multiple isolated VMs, improving utilisation, flexibility and disaster recovery. Major products come from the following vendors.

| Vendor | Product | Type | Key features |
|---|---|---|---|
| **VMware** | vSphere/ESXi, Workstation, Fusion | Type-1 (ESXi), Type-2 | Market leader; vMotion (live migration), HA, DRS, snapshots, vCenter management |
| **Microsoft** | Hyper-V | Type-1, integrated in Windows Server | Live migration, replication, Azure integration, dynamic memory |
| **Citrix / Xen Project** | XenServer, Xen | Type-1, open source | Para-virtualization pioneer; used by early AWS; XenMotion |
| **Red Hat / Linux community** | KVM (RHV, QEMU/libvirt) | Type-1-like kernel module, hardware-assisted | Open source, built into Linux, basis of OpenStack |
| **Oracle** | VirtualBox, Oracle VM Server | Type-2 / Xen-based | Free desktop virtualization, cross-platform |
| **Amazon** | Nitro hypervisor (KVM-based) | Type-1 | Powers EC2 instances |

```
 VM1  VM2  VM3
 [ Hypervisor: ESXi / Hyper-V / Xen / KVM ]
 [ Physical server hardware ]
```
**Common features:** live migration, snapshots/cloning, resource scheduling, high availability, templates, central management console.
**Benefits:** server consolidation, cost savings, isolation, fast provisioning, easier DR.
**Conclusion:** VMware is dominant in enterprise, Hyper-V in Microsoft environments, while Xen/KVM underpin public clouds.
**Keywords:** hypervisor, vMotion, live migration, bare-metal, KVM, Xen, ESXi, Hyper-V.

### Q6.b. Draw and explain resource pooling architectures?
**Unit I – Resource pooling | 10 marks**

**Definition:** Resource pooling is aggregating the provider's physical and virtual resources (CPU, memory, storage, network) into shared pools that are dynamically allocated to multiple tenants (multi-tenancy) and released when no longer needed.

**Diagram**
```
 Physical resources         Virtualization        Resource pools            Consumers
 +-----------+              +-----------+       +--------------+
 | Servers   |---+          |           |-----> | CPU pool     |--+
 +-----------+   |          |           |       +--------------+  |--> Tenant A (VMs)
 | Storage   |---+--------> | Hypervisor|-----> | Memory pool  |--+--> Tenant B (VMs)
 +-----------+   |          |   (VMM)   |       +--------------+  |--> Tenant C (VMs)
 | Network   |---+          |           |-----> | Storage pool |--+
 +-----------+              +-----------+       | Network pool |
                                  ^             +--------------+
                      Resource Management System (allocation, monitoring, quota, billing)
 Sibling pools:  [Pool A] [Pool B]  (same level, isolated)
 Nested pools:   [Parent pool] -> [Sub-pool Dept1] [Sub-pool Dept2]
```
**Explanation**
1. **Physical pool** – servers, storage arrays, network gear.
2. **Virtualization layer** abstracts hardware into logical resources.
3. **Pool types:** physical server pool, virtual server pool, storage pool, network pool, CPU pool, memory pool.
4. **Sibling pools** – equivalent pools isolated from each other (e.g., per tenant/region); **nested pools** – hierarchy where a parent pool is divided into child pools for departments.
5. **Resource management system** applies allocation policies, tracks utilisation, enforces isolation/quota, supports dynamic provisioning and metering.
**Working:** tenant request → management system picks free resources from pool → VM provisioned → on release resources return to the pool.
**Advantages:** high utilisation, elasticity, lower cost, simplified management. **Disadvantages:** noisy neighbours, security isolation, complex management.
**Keywords:** pool, tenant, sibling/nested pool, multi-tenancy, hypervisor, provisioning, resource management.

### Q7.a. What are the scaling strategies in the cloud. Explain them.
**Unit II – Scaling | 10 marks**

**Introduction:** Scaling adjusts capacity to match demand. Strategies are classified by *direction* and by *trigger*.
**1. By direction**
- **Vertical scaling (scale up/down):** add CPU/RAM to the same machine. Simple, no app change; limited by hardware, downtime, single point of failure.
- **Horizontal scaling (scale out/in):** add/remove instances behind a load balancer. Scales almost without limit, fault tolerant; needs stateless design.
- **Diagonal scaling:** scale up until limit, then out.
**2. By trigger/timing**
- **Manual scaling** – operator-driven.
- **Scheduled scaling** – predefined times (office hours, month-end, festival).
- **Dynamic/reactive scaling** – metric thresholds (CPU > 70%).
- **Predictive/proactive scaling** – ML/historical patterns add capacity ahead of demand.
**3. Capacity strategies:** *Lead* (before demand – avoids SLA breach, may waste), *Lag* (after demand – cheap, risk), *Match* (small increments).
| Vertical | Horizontal |
|---|---|
| Bigger machine | More machines |
| Limited | Highly scalable |
| Downtime likely | No downtime |
| Single point of failure | Fault tolerant |
```
 Vertical:   [ 2CPU ] -> [ 8CPU ]       Horizontal: [VM] -> [VM][VM][VM][VM]
```
**Conclusion:** best practice combines horizontal scaling with predictive + dynamic auto scaling.
**Keywords:** scale up/out, auto scaling group, threshold, cooldown, elasticity.

### Q7.b. Explain the role of service level agreement?
**Unit IV – Cloud management (SLA) | 10 marks**

**Definition:** An **SLA (Service Level Agreement)** is a formal, legally binding contract between a cloud provider and a consumer that specifies the services, measurable quality levels (**SLOs**), responsibilities, monitoring and reporting methods, and penalties/remedies for non-compliance.
**Typical contents:** service description; availability (e.g., 99.9% monthly uptime); performance (response time, throughput); support & incident response times; security/privacy/data-location commitments; backup/DR; billing; exclusions; **service credits**; termination and exit terms.
**Role of SLA**
1. **Sets expectations** – clear, measurable QoS targets.
2. **Defines responsibilities** of provider and consumer (shared responsibility).
3. **Basis for monitoring and reporting** – metrics measured and compared with targets.
4. **Risk management & remedies** – penalties/credits compensate failures.
5. **Dispute resolution** – evidence for claims.
6. **Builds trust & helps selection** – compare providers.
7. **Drives automation** – cloud management is SLA-driven (auto scaling, provisioning to meet SLOs).
8. **Compliance and security assurance.**
**SLA lifecycle:** discover service provider → define SLA → establish agreement → monitor violations → terminate / enforce penalties.
```
 Consumer <--- SLA (SLOs, penalties) ---> Provider
     |  monitor/reports                       |  meets targets or pays credits
```
**Conclusion:** SLA is the cornerstone of trust between provider and consumer.
**Keywords:** SLA, SLO, availability, uptime, QoS, service credit, penalty, monitoring.

### Q8.a. What is meany by load balancing. Explain how load balancing is done in cloud?
**Unit II – Load balancing | 10 marks**

**Definition:** Load balancing distributes incoming requests/workload across multiple servers or resources to maximise throughput, minimise response time, avoid overload of any single resource, and ensure high availability.
**Need:** performance, availability/failover, scalability, efficient utilisation.
**Types:** *Static* (fixed rules) vs *Dynamic* (current load); *Layer-4* (network) vs *Layer-7* (application/content-aware); *local* vs *global (multi-region)*.
**Algorithms:** round robin, weighted round robin, least connections, least response time, IP hash, resource-based.
**How it is done in cloud**
1. Clients access a single DNS name / virtual IP of the **load balancer**.
2. LB performs **health checks**; unhealthy servers are removed.
3. LB selects a backend using an algorithm and forwards the request.
4. **Session persistence** (sticky sessions) if needed; **SSL termination** at LB.
5. LB integrates with **auto scaling** – new instances automatically registered, terminated ones de-registered.
6. **Global load balancing** (DNS, anycast) routes users to nearest/healthy region.
7. Cloud offerings: AWS ELB (ALB/NLB/GLB), Azure Load Balancer & Application Gateway, Google Cloud Load Balancing.
```
 Clients -> [Load Balancer: health check + algorithm] -> Server1
                                                      -> Server2
                                                      -> Server3 (failed - skipped)
                 ^ auto scaling adds/removes servers
```
**Advantages:** high availability, scalability, performance, maintenance without downtime. **Disadvantages:** LB can be single point of failure (mitigated by redundant LBs), cost, configuration complexity.
**Keywords:** VIP, health check, round robin, least connections, sticky session, L4/L7, failover.

### Q8.b. Discuss different service delivery models.
**Unit I – Cloud services | 10 marks**

**Introduction:** Cloud services are delivered in three NIST service models — **IaaS, PaaS, SaaS** — described as layers; others are grouped as XaaS.
**1. IaaS – Infrastructure as a Service:** provides virtualized compute, storage, networking on demand; user installs OS, middleware, applications. Features: pay-per-use, scalable, full control. Examples: Amazon EC2, Azure VM, Google Compute Engine. Users: system administrators. Pros: flexibility, no hardware. Cons: user must manage OS/security.
**2. PaaS – Platform as a Service:** provides runtime, middleware, databases and development tools to build/deploy applications without managing servers/OS. Examples: Google App Engine, Azure App Service, Heroku, AWS Elastic Beanstalk. Users: developers. Pros: faster development, auto scaling. Cons: less control, platform lock-in.
**3. SaaS – Software as a Service:** complete application delivered via browser; provider manages everything. Examples: Gmail, Salesforce, Office 365, Zoom. Users: end users. Pros: no install, automatic updates. Cons: limited customisation, data dependency.
**4. XaaS:** DBaaS, FaaS, CaaS, Storage-as-a-Service, Security-as-a-Service.
```
 SaaS  [ Application ]          <- end users
 PaaS  [ Runtime / Middleware ] <- developers
 IaaS  [ VM / Storage / Network ] <- administrators
 [ Data centre hardware ]
```
| | IaaS | PaaS | SaaS |
|---|---|---|---|
| Customer manages | OS, middleware, app, data | App, data | Nothing/only data usage |
| Control | High | Medium | Low |
| Example | EC2 | App Engine | Gmail |
**Deployment models (brief):** public, private, hybrid, community.
**Conclusion:** choose IaaS for control, PaaS for development speed, SaaS for ready software.
**Keywords:** IaaS, PaaS, SaaS, XaaS, hypervisor, runtime, shared responsibility.

---
---
# PAPER B (PYQ-B)

## Part A (10M) – Answer ALL questions

### Q1. Why are managed and unmanaged cloud storages, called so?
**Unit II – Storage | 2 marks** → *Repeated from PYQ-A Q4; the answer is the same:* **Managed** storage is operated by the provider (provisioning, replication, backup, scaling – e.g., S3, Azure Blob), **unmanaged** storage is raw capacity that the user must format, configure, protect and scale himself (e.g., raw disk attached to a VM). The names indicate *who manages the storage*.

### Q2. Draw the lifecycle of a cloud service.
**Unit IV – Cloud management | 2 marks**
```
 +--------+    +------------+    +-----------+    +------------+
 | Design | -> | Deployment | -> | Operation | -> | Retirement |
 +--------+    +------------+    +-----------+    +------------+
      ^             feedback / change management / new version  |
      +------------------------------------------------------------+
```
**Design:** requirements, architecture, SLA/metadata. **Deployment:** build, test, provision, publish in catalogue. **Operation:** provisioning, monitoring, scaling, billing, updates. **Retirement:** notify users, migrate/archive data, release resources, secure deletion.

### Q3. Define cloud interoperability and cloud portability.
**Unit IV – Portability & interoperability | 2 marks**
- **Interoperability:** the ability of different cloud systems/services (possibly from different vendors, or private+public) to **communicate and work together**, exchanging and using data through common interfaces/standards.
- **Portability:** the ability to **move** applications/data/VM images from one cloud (or on-premise) to another with minimal change or cost.
*Example:* moving a VM from AWS to Azure = portability; a private-cloud app calling a public-cloud storage API = interoperability.

### Q4. Define cloud native micro services.
**Unit V – Microservices | 2 marks**

Cloud-native microservices are **small, independently deployable services, each implementing a single business capability with its own data, packaged in containers, communicating through lightweight APIs and managed by orchestrators (Kubernetes)**, designed to exploit cloud elasticity, automation and resilience. Features: loose coupling, independent scaling/deployment, fault isolation, DevOps/CI-CD friendly.

### Q5. What are the stages of a CI/CD pipeline?
**Unit V – CI/CD | 2 marks**
1. **Source/commit** (code pushed to Git triggers pipeline) 2. **Build** (compile/package, Docker image) 3. **Test** (unit, integration, security scan) 4. **Release/Artifact** (push image to registry) 5. **Deploy** (staging → approval → production) 6. **Operate/Monitor** (feedback, rollback).
```
 Commit -> Build -> Test -> Release -> Deploy -> Monitor     (CI = first 3, CD = remaining)
```

---
## Part B (20M) – Answer any TWO (10 marks each)

### Q6.a. Discuss Google file system in detail.
**Unit II – File system | 10 marks**

**Introduction:** GFS is Google's proprietary scalable distributed file system for large, data-intensive applications running on commodity hardware, designed for huge files, high throughput and fault tolerance.
**Design assumptions:** component failures are common; files are huge (GBs); mostly sequential reads and append-writes; high sustained bandwidth over low latency.
**Architecture**
```
              +----------+  metadata ops
   Client --->|  MASTER  |  (namespace, file->chunk map, chunk locations, operation log)
     |        +----------+
     |            ^ heartbeat / chunk reports
     | data       |
     +-------> [Chunkserver 1] [Chunkserver 2] [Chunkserver 3]   chunk replicas (3x)
```
- **Master (single):** keeps all metadata in memory, controls chunk placement, leases, garbage collection, re-replication; logs operations + checkpoints.
- **Chunkservers:** store **64 MB chunks** as Linux files, each with a 64-bit chunk handle, replicated (default **3 replicas**) across racks; verify with checksums.
- **Client library:** asks master for metadata, then communicates directly with chunkservers (master isn't a data bottleneck).
**Operations**
- **Read:** client sends (file, offset) → master returns chunk handle + replica locations (cached) → client reads from nearest chunkserver.
- **Write:** master grants **lease** to a *primary* replica; client pushes data to all replicas; primary assigns serial order; secondaries apply; success acknowledged.
- **Record append:** atomic "at least once" append, suitable for producer-consumer logs.
- **Snapshots:** copy-on-write.
**Fault tolerance:** heartbeats, automatic re-replication, operation log + checkpoints, shadow masters, checksums.
**Consistency model:** relaxed – defined/consistent regions.
**Advantages:** very high throughput, scalable, fault tolerant, cheap hardware. **Disadvantages:** single master bottleneck, not suited to small files/low latency, relaxed consistency.
**Keywords:** master, chunkserver, 64 MB chunk, lease, record append, heartbeat, replication, operation log.

### Q6.b. Draw and explain a model of HDFS cluster.
**Unit II – File system | 10 marks**

**Introduction:** HDFS (Hadoop Distributed File System) is a fault-tolerant, distributed file system of Apache Hadoop for storing very large files across commodity machines in a **master–slave** architecture (open-source implementation of GFS ideas).
**Diagram**
```
                 +----------------------+
  Client ------->|      NameNode        |  FsImage + EditLog (metadata)
    |   ^        +----------------------+ <-- Secondary/Checkpoint NameNode (merges edits)
    |   | block locations   ^ heartbeat + block reports
    |   +-------------------+----------------------+
    v                       |                      |
 Rack 1:  [DataNode1: blk A,B] ---pipeline---> [DataNode2: blk A,C]
 Rack 2:  [DataNode3: blk A,B,C]   ... (each block replicated 3x)
```
**Components**
- **NameNode (master):** stores namespace and block mapping (FsImage, EditLog); single point of metadata; decides replica placement; HA with Standby NameNode.
- **Secondary NameNode:** periodically checkpoints (merges EditLog with FsImage) – not a failover node.
- **DataNodes (slaves):** store blocks, serve read/write, send **heartbeats** (every 3 s) and **block reports** to NameNode.
- **Block:** large unit, **128 MB** default; file split into blocks; replication factor **3** with **rack awareness** (replica 1 local, replicas 2–3 on another rack).
- **Client:** contacts NameNode for metadata, transfers data directly with DataNodes.
**Working**
- *Write:* client → NameNode for DataNode list → writes block to first DataNode → pipeline replication to others → acks → block report.
- *Read:* client asks NameNode for block locations → reads from the closest DataNode.
- *Failure:* missing heartbeat → DataNode marked dead → NameNode re-replicates its blocks.
**Features:** fault tolerance, scalability, write-once-read-many, data locality, high throughput.
**Limitations:** small-files problem, NameNode memory, not low latency.
**Keywords:** NameNode, DataNode, block, replication factor, rack awareness, FsImage, EditLog, heartbeat, pipeline.

### Q7.a. Define open virtualization format (OVF) and differentiate virtual machine image and virtual appliance.
**Unit I – Virtualization | 10 marks**

**OVF:** Open Virtualization Format is an open, vendor-neutral standard from the **DMTF** for packaging and distributing virtual machines/virtual appliances so they can run on different hypervisors (VMware, VirtualBox, Xen, KVM, etc.).
**Structure of an OVF package**
```
 OVF package
  |- descriptor.ovf  (XML: hardware, networks, virtual systems, config)
  |- disk1.vmdk      (virtual disk images)
  |- manifest .mf    (checksums for integrity)
  |- certificate .cert (optional signature)
 Single-file archive = .OVA (tar of the above)
```
**Features:** portability across platforms, integrity (SHA hashes) & signing, multi-VM packages, license/EULA, extensibility, easy distribution.
**Virtual machine image:** a stored file (template) that contains the disk contents (OS + data/config) from which VMs are created; generic, usually needs installing/configuring software (e.g., Ubuntu image, AWS AMI).
**Virtual appliance:** a **pre-built, pre-configured, ready-to-run VM image** containing OS + application stack for a specific purpose, with metadata (usually distributed as OVF) – e.g., pfSense firewall, Bitnami WordPress, VMware vCenter appliance.
| Basis | VM image | Virtual appliance |
|---|---|---|
| Content | OS (+ optional data) | OS + pre-installed, tuned application |
| Purpose | General | Single dedicated function |
| Setup effort | Install/configure software | Ready to use |
| Size / OS | Full general OS | Minimal (JeOS) |
| Packaging | Disk file(s) | Disk + metadata, usually OVF/OVA |
| Example | Ubuntu .vmdk | Bitnami LAMP OVA |
**Keywords:** DMTF, OVF, OVA, VMDK, descriptor, manifest, virtual appliance, portability.

### Q7.b. Draw and explain three perspectives of cloud service management.
**Unit IV – Cloud management | 10 marks**

**Introduction:** Cloud service management comprises the activities to plan, deliver, operate and improve cloud services. It is viewed from three stakeholder perspectives (as in the Open Group SOCCI viewpoints) — **cloud service provider, cloud service consumer, and cloud service developer/integrator**. *(Textbooks differ; if your class notes use a different triad – e.g., business, operational, technical – write those, keeping the same diagram and structure.)*
**Diagram**
```
              +-------------------------------+
              |      CLOUD SERVICE MANAGEMENT |
              +--------------+----------------+
     +------------------+    |    +-------------------+
     | Provider         |    |    | Consumer          |
     | (deliver/operate)|<-- SLA -->| (use/govern)    |
     +------------------+    |    +-------------------+
                       +-----v--------------+
                       | Developer/Integrator|
                       | (design/build/deploy)|
                       +---------------------+
```
1. **Provider perspective:** build and operate data centres; capacity planning; provisioning; service catalogue; SLA fulfilment; security; monitoring; billing/metering; support (OSS/BSS).
2. **Consumer perspective:** choose services, cost and usage monitoring, SLA tracking, security & compliance of own data, identity management, avoiding lock-in/exit strategy, integrating with in-house IT.
3. **Developer/Integrator perspective:** design, develop, test and deploy services; integration through APIs; DevOps automation; versioning and lifecycle upgrades.
**Common management functions:** provisioning, monitoring, SLA management, security, billing, lifecycle management.
**Conclusion:** effective cloud service management requires coordination of all three perspectives through SLAs and standards.
**Keywords:** provider, consumer, developer, SLA, OSS/BSS, lifecycle, provisioning, monitoring.

### Q8.a. Explain in detail about AWS Lambda.
**Unit V – Serverless | 10 marks**

**Definition:** AWS Lambda is Amazon's **serverless, event-driven compute service (FaaS)** that runs your code in response to events without provisioning or managing servers; you pay only for compute time used.
**Key concepts:** *Function* (code + configuration) • *Handler* (entry point) • *Runtime* (Python, Node.js, Java, Go, .NET, Ruby, custom/container) • *Event* & *Trigger* • *Execution role (IAM)* • *Layers* • *Environment variables* • *Versions/aliases* • *Concurrency*.
**Working**
```
 Event source ---> [ Lambda service ] ---> creates/reuses execution environment ---> Function runs
 (API Gateway, S3, DynamoDB Stream,        (cold start vs warm)                         |
  SQS/SNS, EventBridge, schedule)                                          S3 / DynamoDB / CloudWatch logs
```
1. Write and upload code; configure memory (128 MB–10 GB; CPU proportional), timeout (max 15 min), IAM role.
2. Attach a trigger.
3. On event, Lambda starts an isolated execution environment (**cold start**) or reuses a warm one, runs handler, returns result and logs to CloudWatch.
4. Scales automatically by running many copies concurrently; scales to zero when idle.
**Pricing:** per request + duration (GB-seconds); free tier.
**Use cases:** image/thumbnail processing on S3 upload, REST/mobile backends with API Gateway, real-time stream/file processing, scheduled jobs, chatbots, IoT.
**Advantages:** no server management, auto scaling, pay-per-use, high availability, integration with AWS ecosystem. **Limitations:** cold start latency, time/memory/package limits, stateless, vendor lock-in, harder debugging/testing, unsuitable for long-running tasks.
**Keywords:** FaaS, handler, trigger, cold start, execution role, concurrency, CloudWatch, API Gateway, pay-per-invocation.

### Q8.b. Draw and explain kubernetes.
**Unit V – Kubernetes | 10 marks**

**Definition:** Kubernetes (K8s) is an open-source **container orchestration platform** (originated at Google) that automates deployment, scaling, load balancing, self-healing and management of containerised applications across a cluster of machines.
**Diagram**
```
 kubectl / CI-CD
       |
 +-----v------------------- CONTROL PLANE ------------------------+
 |  API Server  <---->  etcd (cluster state)                       |
 |      ^                                                          |
 |  Scheduler        Controller Manager                            |
 +-----+------------------------------------------------------- --+
       |
 +-----v----- WORKER NODE 1 ----+      +---- WORKER NODE 2 -----+
 | kubelet   kube-proxy         |      | kubelet   kube-proxy   |
 | Container runtime (containerd)|      | Container runtime      |
 | [Pod: C1 C2] [Pod: C3]       |      | [Pod: C4] [Pod: C5]    |
 +------------------------------+      +------------------------+
```
**Components**
- **API Server:** entry point, REST API, authentication/validation, only gateway to etcd.
- **etcd:** consistent key-value store for desired & current state.
- **Scheduler:** places Pods on nodes based on resources/constraints.
- **Controller Manager:** control loops (node, replica, deployment) reconcile actual state to desired state.
- **kubelet:** node agent that runs and monitors Pods. **kube-proxy:** networking & service load balancing. **Container runtime:** runs containers.
- **Objects:** Pod, ReplicaSet, Deployment, Service, Ingress, ConfigMap/Secret, PersistentVolume, StatefulSet, Namespace.
**Working:** user submits YAML → API server stores in etcd → scheduler assigns Pods → kubelet starts containers → controllers keep desired state (self-healing, auto-scaling via HPA, rolling updates).
**Features:** self-healing, horizontal scaling, service discovery & load balancing, rolling updates/rollback, storage orchestration, portability across clouds.
**Keywords:** Pod, node, API server, etcd, scheduler, controller manager, kubelet, kube-proxy, Deployment, Service, desired state.
