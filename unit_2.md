# Unit II – Scaling, Capacity Planning, Load Balancing, File System and Storage

**Syllabus:** Scaling in the Cloud, Capacity Planning, Load Balancing, File System and Storage.
**Paper codes:** `PYQ-A`, `PYQ-B` (previous-year papers), `Mid-1`, `Mid-2` (current-year mids).

---

## 0. Unit II – Priority Map (actual appearances in supplied papers)

Unit II is the **most frequently examined unit** in the supplied papers (13 of 44 questions).

| Topic | Actual appearances | Priority |
|---|---|---|
| Scaling strategies / auto scaling | PYQ-A Q3, Q7a | **P1** |
| Load balancing (concept, cloud LB) | PYQ-A Q8a | **P1** |
| Managed vs unmanaged storage | PYQ-A Q4, PYQ-B Q1 (repeated) | **P1** |
| Google File System | PYQ-B Q6a | **P1** |
| HDFS cluster | PYQ-B Q6b | **P1** |
| Elasticity vs scalability | Mid-1 Q2 | **P1** |
| Dynamic vs automatic scaling | Mid-1 Q4 | P2 |
| Class-aware vs class-agnostic LB | Mid-1 Q5 | P2 |
| Static vs dynamic load balancing | Mid-1 Q8b | P2 |
| Capacity planning – steps; expected-demand challenges | Mid-1 Q8a | P2 |
| Cloud-native file system & challenges | Mid-1 Q7b | P2 |
| Storage types (block/file/object), RAID, SAN/NAS | none directly | P3 (expected) |

---

## 2.1 Scaling in the Cloud

### A. Definition
**Scaling** is the ability of a system to increase or decrease its resource capacity (CPU, memory, instances) to meet changing workload demand while maintaining performance.

### B. Simple Explanation
When more customers arrive, you add capacity (bigger server or more servers); when they leave, you remove it.

### C. Detailed Explanation
**Types**
- **Vertical scaling (scale up/down):** increase CPU/RAM of one machine. Simple, but limited by hardware, needs restart, single point of failure.
- **Horizontal scaling (scale out/in):** add/remove machines (instances) behind a load balancer. Highly scalable, fault-tolerant, needs stateless/distributed design.
- **Diagonal scaling:** scale up first, then out when limit reached.

**Scaling strategies (PYQ-A Q7a):**
1. **Manual scaling** – administrator changes capacity.
2. **Scheduled scaling** – capacity changed at known times (e.g., 9 AM–6 PM, month-end).
3. **Reactive / dynamic scaling** – triggered by real-time metrics (CPU > 70% → add instance).
4. **Proactive / predictive scaling** – uses historical patterns/ML to scale before demand.
5. **Vertical vs horizontal** (as above).
6. Capacity strategies: **lead** (add capacity ahead of demand), **lag** (add after demand exceeds), **match** (incrementally track demand).

**Auto scaling – how it works (PYQ-A Q3):**
1. Define *Auto Scaling Group* with **min, max, desired** instances and a launch template (image, type).
2. A monitoring service (e.g., CloudWatch) collects metrics (CPU, requests, queue length).
3. A **scaling policy** compares metric with threshold (alarm).
4. **Scale-out:** launch new instances, register to load balancer after health check. **Scale-in:** drain connections, terminate instances.
5. **Cooldown period** prevents rapid flapping.

### D. Diagram – Auto Scaling
```
 Users --> [Load Balancer] --> [ VM1 ][ VM2 ][ VM3 ]  <-- Auto Scaling Group (min=2,max=6)
                                   ^
        Metrics (CPU, requests) -> [Monitor/Alarm] -> [Scaling Policy]
                                       if CPU>70%: add VM   if CPU<30%: remove VM
```

### E. Elasticity vs Scalability (Mid-1 Q2)
| | Scalability | Elasticity |
|---|---|---|
| Meaning | Capability to handle growing load by adding resources | Ability to automatically acquire **and release** resources as demand changes |
| Direction | Mostly growth | Both up and down |
| Time-scale | Long-term, planned | Short-term, real-time |
| Mechanism | Architecture/design, manual or automatic | Automation (auto scaling) |
| Cost focus | Capacity to grow | Pay only for what you use |
| Example | Adding database shards as company grows | Web servers 4→20→4 across a sale day |

### Dynamic scaling vs Automatic scaling (Mid-1 Q4)
They are **not strictly synonymous**:
- **Automatic scaling** = scaling carried out *without human intervention* by predefined rules — may be scheduled, predictive or reactive.
- **Dynamic scaling** = scaling that *responds to live changes* in load/metrics (reactive). It is usually automatic, but a human operator could also scale dynamically in response to load.
- So *dynamic ⊂ automatic* (dynamic scaling is one kind of auto scaling); scheduled scaling is automatic but not dynamic.

### F. Example
IRCTC-like booking site: scheduled scale-out before 10 AM Tatkal, dynamic scale-out if traffic exceeds forecast.

### G. Exam answer skeleton
Definition → types (vertical/horizontal) → strategies → auto-scaling workflow with diagram → benefits/limitations.

### H. Keywords
Scale up/out, ASG, min/max/desired, threshold, cooldown, predictive scaling, scheduled scaling, elasticity, load balancer.

---

## 2.2 Capacity Planning

### A. Definition
**Capacity planning** is the process of determining the amount of computing resources (servers, storage, network) needed to meet current and future workload demand at required service levels at minimum cost.

### B. Simple Explanation
Estimating in advance how much "room" you need so the system is neither overloaded nor wastefully empty.

### C. Detailed Explanation
**Main steps of capacity planning (Mid-1 Q8a):**
1. **Determine expected demand** (workload characterisation & forecasting).
2. **Measure current capacity/usage** – baseline utilisation, performance metrics.
3. **Define service-level requirements** (response time, availability – SLA).
4. **Analyse gap** between demand and capacity.
5. **Select strategy** – lead, lag or match; choose reserved/on-demand/spot, scale type.
6. **Implement / provision** capacity.
7. **Monitor, review and re-plan** continuously.

**Main challenges in 'Determining the Expected Demand' (Mid-1 Q8a):**
- Unpredictable, bursty/flash-crowd traffic.
- Seasonality and events (festivals, sales).
- No historical data for new applications.
- Forecast inaccuracy and changing user behaviour (viral growth).
- Varying workload mix (CPU-, I/O-, memory-intensive).
- Interference among multi-tenant workloads; shared-resource variability.
- Inaccurate or insufficient monitoring data.
- Balancing cost vs SLA risk (over- vs under-provisioning).
- Provisioning lead time and quotas.

| Strategy | Description | Risk |
|---|---|---|
| Lead | Add capacity before demand | Over-provisioning cost |
| Lag | Add after demand appears | SLA violation |
| Match | Add in small increments | Needs good monitoring |

### D. Diagram
```
 Forecast demand --> Compare with --> Gap? --> Provision / Scale --> Monitor
        ^            current capacity                                  |
        +------------------ feedback & re-planning <-------------------+
```

### G. Exam skeleton
Definition → importance → steps → challenges (demand) → strategies → conclusion.

### H. Keywords
Workload characterisation, forecasting, baseline, utilisation, SLA, headroom, lead/lag/match, right-sizing.

---

## 2.3 Load Balancing

### A. Definition
**Load balancing** is the technique of distributing incoming network traffic or computing workload across multiple servers/resources to maximise throughput, minimise response time, avoid overload and ensure availability.

### B. Simple Explanation
Like a supermarket manager who directs customers to the shortest queue so no cashier is overloaded.

### C. Detailed Explanation
**How load balancing is done in cloud (PYQ-A Q8a):**
1. Clients send requests to the LB's single virtual IP/DNS name.
2. LB runs **health checks** and keeps a list of healthy servers.
3. It chooses a server using an **algorithm** and forwards the request.
4. Supports **session persistence (sticky sessions)**, SSL termination, and integrates with **auto scaling** (new instances auto-registered).
5. **Global LB** (DNS/anycast) routes between regions; **local LB** within a data centre.
- Cloud examples: AWS Elastic Load Balancer (ALB L7 / NLB L4), Azure Load Balancer / Application Gateway, Google Cloud Load Balancing.

**Algorithms:** Round robin, weighted round robin, least connections, least response time, IP-hash, resource-based (adaptive).

**Static vs Dynamic LB (Mid-1 Q8b):**
| | Static | Dynamic |
|---|---|---|
| Decision basis | Fixed rules, prior knowledge | Current state/load of servers |
| Overhead | Very low (no monitoring) | Higher (status exchange, monitoring) |
| Complexity | Simple | Complex |
| Adaptability | None | High |
| Best for | Small/simple system, uniform & predictable requests | Large/heterogeneous, fluctuating workloads |

*Why static can be better for a small simple system:* uniform tasks and identical servers make round robin nearly optimal; no monitoring traffic/CPU overhead; cheaper, deterministic, easier to debug/maintain; dynamic scheme's gathering and decision cost may exceed its gain; no extra single point of failure/ complex controller.

**Class-aware vs Class-agnostic LB (Mid-1 Q5):**
| | Class-aware (content-aware, L7) | Class-agnostic (content-blind, L4) |
|---|---|---|
| Decision using | Request type/class (URL, content, user type, priority) | Only network info (IP/port, load) |
| Overhead | Higher (inspects requests) | Lower, faster |
| Where required | Heterogeneous services (static vs dynamic content, video vs API), QoS/priority classes (premium vs free), e-commerce, specialised servers, sticky sessions | Homogeneous stateless servers, simple high-throughput traffic, TCP/UDP load |
| Example | AWS ALB path routing | AWS NLB, round-robin DNS |

### D. Diagram
```
 Clients --> [ Load Balancer ] --+--> Server 1  (healthy)
             (algorithm,         +--> Server 2  (healthy)
              health check)      +--> Server 3  (failed - skipped)
                    ^
            Auto-scaling adds/removes servers
```

### F. Example
Flipkart sale: ALB routes /images to static servers and /checkout to application servers (class-aware).

### G. Exam skeleton
Definition → need → types/algorithms → cloud working + diagram → advantages → conclusion.

### H. Keywords
Health check, VIP, round robin, least connections, sticky session, L4/L7, SSL offload, static/dynamic, class-aware/agnostic, failover.

---

## 2.4 File System and Storage

### A. Definition
**Cloud storage** is a service model in which data is stored, managed and backed up remotely on provider-managed servers and accessed over the network. A **distributed file system** stores files across many machines while presenting a single namespace.

### B. Simple Explanation
Instead of a pen drive in your pocket, your files live in the provider's data centre, replicated on several machines.

### C. Detailed Explanation
**Storage types:** *Block* (raw volumes, e.g., EBS), *File* (shared folders NFS/SMB, e.g., EFS), *Object* (objects + metadata via HTTP API, e.g., S3, Azure Blob, Google Cloud Storage).

**Managed vs Unmanaged cloud storage (PYQ-A Q4 / PYQ-B Q1):** named after *who manages the storage*.
| | Managed storage | Unmanaged storage |
|---|---|---|
| Who manages | **Provider** – provisioning, replication, backup, scaling, patching | **User** – raw disk attached to VM; user formats, builds file system, RAID, backups |
| Effort | Low | High |
| Scalability | Automatic, virtually unlimited | Manual |
| Control | Less | Full |
| Examples | S3, Azure Blob, Google Drive, DynamoDB | Raw EBS/instance-store disks, self-built NFS/Ceph on VMs |
Called "managed" because the service is fully managed by the vendor; "unmanaged" because the customer has to manage it.

### Google File System – GFS (PYQ-B Q6a)
Scalable distributed file system for large data-intensive apps on commodity hardware.
- **Architecture:** one **Master** (metadata: namespace, file→chunk mapping, chunk locations, operation log) + many **Chunkservers** (store chunks as Linux files) + **Clients** (library).
- **Chunk:** fixed **64 MB**, identified by 64-bit handle, **replicated 3×** (default) on different racks.
- **Read:** client asks master for chunk location → caches → reads data directly from a chunkserver (master not in data path).
- **Write:** master grants a **lease** to a primary replica; data pushed to all replicas along a pipeline; primary orders mutations; secondaries apply; ack.
- **Record append:** atomic append at least once — ideal for logs/producer-consumer.
- **Fault tolerance:** heartbeat messages, re-replication, checkpoint + operation-log for master, shadow masters, checksums.
- **Consistency:** relaxed (consistent/defined regions).
- **Pros:** high throughput, fault tolerant, large files; **Cons:** single master bottleneck, poor for small files/low latency.

```
         +---------+  metadata   +-----------+
 Client -|  Master |<--heartbeat-| Chunkserver|
   |     +---------+             +-----------+
   +---------- data (direct) ---> [Chunkserver 1][Chunkserver 2][Chunkserver 3]
                                    chunk C1 replicated 3x
```

### HDFS cluster model (PYQ-B Q6b)
Hadoop Distributed File System – open-source GFS equivalent (master–slave).
- **NameNode (master):** namespace, metadata (FsImage + EditLog), block-to-DataNode mapping; **Secondary/Checkpoint NameNode** merges edits (not a hot standby; HA uses Standby NameNode).
- **DataNodes (slaves):** store blocks, send **heartbeats** and **block reports**.
- **Block size** 128 MB default; **replication factor 3** with **rack awareness** (1 replica local rack, 2 on another rack).
- **Read/Write:** client contacts NameNode, then streams directly to/from DataNodes; write uses replication pipeline.
- **Features:** fault tolerance, write-once-read-many, data locality (move computation to data), scalability.

```
                 +-----------------+
 Client -------> |    NameNode     |  (metadata: FsImage/EditLog)
   |  ^          +-----------------+
   |  | block locations     ^ heartbeat/block reports
   |  +---------------------+----------------------+
   v                        |                      |
 +--------+  pipeline  +--------+  pipeline  +--------+
 |DataNode|----------->|DataNode|----------->|DataNode|   Rack 1 / Rack 2
 | blk A  |            | blk A  |            | blk A  |
 +--------+            +--------+            +--------+
```

| GFS | HDFS |
|---|---|
| Google proprietary | Apache open source |
| Master / Chunkserver | NameNode / DataNode |
| Chunk 64 MB | Block 128 MB |
| Multiple writers, record append | Write-once; append only (single writer) |
| Lease based | Single-writer model |

### Cloud-Native File System (Mid-1 Q7b)
**Definition:** a file system designed *for* cloud/containerised environments — distributed, elastic, API/CSI-integrated, supporting dynamic scaling, multi-tenancy and persistent volumes for stateless-oriented platforms like Kubernetes. Examples: Amazon EFS, Azure Files, CephFS, GlusterFS, Portworx, Lustre-as-a-service, Google Filestore.
**Challenges:** (1) scalability of metadata servers; (2) consistency vs performance (CAP); (3) latency/throughput over network; (4) multi-tenant isolation & security; (5) elasticity — scaling capacity and performance dynamically; (6) data portability/lock-in across clouds; (7) high availability & replication cost; (8) persistence for ephemeral containers (persistent volumes, data gravity); (9) cost and monitoring; (10) POSIX compatibility vs object semantics.

### F. Example
Photos in Google Photos (managed object storage) vs a disk you attach to an EC2 VM and format yourself (unmanaged).

### H. Keywords
Block/file/object storage, managed/unmanaged, GFS, chunk, master, chunkserver, lease, record append, HDFS, NameNode, DataNode, replication factor, rack awareness, heartbeat, CSI, persistent volume.
