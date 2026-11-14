# Mid-1 Question Paper – Answers

**Scan notes (nothing invented):**
- The header of Part A and its marks are **cut off** in the scan, so **Part A marks per question are not visible** (not assumed here).
- Part B is printed as "PART-B (Answer any TWO complete questions) 2 * 10 = 20 Marks" → **10 marks each**.
- Several questions carry pen strike-marks (already attempted/ticked) – they are still answered below. Questions are copied as printed.
- Units covered: I and II (see unit notes for detail).

| Q | Unit | Topic |
|---|---|---|
| 1 | I | Cluster vs Grid vs Cloud |
| 2 | II | Elasticity vs Scalability |
| 3 | I | Sandboxing vs Virtualization |
| 4 | II | Dynamic vs Automatic scaling |
| 5 | II | Class-aware vs class-agnostic LB |
| 6a | I | Under/over-provisioning |
| 6b | I | Full / Para / Hardware-assisted virtualization |
| 7a | I | SOA and cloud |
| 7b | II | Cloud native file system |
| 8a | II | Capacity planning |
| 8b | II | Static vs dynamic load balancing |

---
## Part A (compulsory – marks not visible in scan)

### Q1. Give a very crisp distinction between, Cluster computing, Grid computing and Cloud Computing.
**Unit I – Introduction | marks: not visible**

| Basis | Cluster | Grid | Cloud |
|---|---|---|---|
| Coupling / nodes | Tightly coupled, homogeneous | Loosely coupled, heterogeneous | Loosely coupled, virtualized, heterogeneous |
| Location | Same site/LAN | Geographically distributed | Provider data centres over Internet |
| Ownership/admin | Single organisation | Multiple organisations (virtual organisation) | Provider-owned, multi-tenant |
| Purpose | High performance / availability | Pool resources for large collaborative jobs | On-demand services (IaaS/PaaS/SaaS) |
| Resource model | Fixed | Shared/contributed | Elastic, pay-per-use, metered |
| Example | Beowulf cluster | Globus, SETI@home | AWS, Azure |

*Crisp line:* cluster = one owner, one site; grid = many owners, many sites, shared; cloud = virtualized, elastic, metered services.

### Q2. What is the difference between Elasticity and Scalability in Cloud Computing
**Unit II – Scaling | marks: not visible**

| Scalability | Elasticity |
|---|---|
| Ability to handle increased workload by adding resources (capacity to grow) | Ability to automatically **add and remove** resources to match demand in real time |
| Long-term, planned | Short-term, dynamic |
| Mostly growth direction | Both scale-out and scale-in |
| Can be manual | Requires automation (auto scaling) |
| Example: adding DB shards as company grows | Web servers 4→20→4 over a sale day, billed per use |

### Q3. What is the difference between sandboxing and virtualization from application design perspective ?
**Unit I – Virtualization | marks: not visible**

| Basis | Sandboxing | Virtualization |
|---|---|---|
| Concept | Runs app in isolated environment with restricted permissions on host OS | Emulates complete hardware so a full guest OS runs in a VM |
| OS dependence | Shares host kernel – app must be designed for that OS | Each VM has own OS – app can be any OS, unchanged |
| Application design view | Design with limited permissions/APIs, controlled resources; good for untrusted/third-party code (browser, JVM, mobile apps) | App is unaware of virtualization; legacy/complete stacks can be moved as-is; isolation at OS level |
| Overhead | Lightweight, fast start | Heavy (GBs), slower boot |
| Isolation strength | Policy-based, weaker | Strong hardware-level |
| Example | Chrome sandbox, Java sandbox | VMware, KVM |

### Q4. Are dynamic scaling and automatic scaling not synonymous? Justify your answer.
**Unit II – Scaling | marks: not visible**

**Answer: They are not strictly synonymous (though often used loosely together).**
- **Automatic scaling:** scaling carried out *without human intervention* following predefined policies — can be **scheduled**, **predictive**, or **reactive**.
- **Dynamic scaling:** scaling that *responds to real-time changes* in workload/metrics (reactive). It is generally implemented automatically but can also be done manually by an operator reacting to load.
- Hence: *dynamic scaling is one type of automatic scaling*. Example: scheduling 10 extra VMs every day at 9 AM is automatic but not dynamic; adding VMs when CPU>70% is both dynamic and automatic; an administrator adding VMs after seeing a traffic spike is dynamic but not automatic.

### Q5. Distinguish between Class Aware and Class Agnostic Load Balancers. Where are each of these required?
**Unit II – Load balancing | marks: not visible**

| Basis | Class-aware (content-aware, L7) | Class-agnostic (content-blind, L4) |
|---|---|---|
| Routing decision | Based on request class/content (URL, type, priority, user type) | Based only on network info/server load (IP, port, connection count) |
| Overhead | Higher (inspects request) | Low, fast |
| Granularity | Different servers/pools for different classes | Same treatment for all requests |
| Required where | Heterogeneous services (static vs dynamic, video vs API), QoS/priority differentiation (premium vs free users), e-commerce microservice routing, specialised servers, sticky sessions | Homogeneous, stateless server farms, very high throughput TCP/UDP traffic, simple web hosting |
| Example | AWS Application Load Balancer path routing | AWS Network Load Balancer, round-robin DNS |

---
## Part B (Answer any TWO – 10 marks each)

### Q6.a. Does cloud computing entirely eliminate resource under- and over-provisioning problems? Justify with examples.
**Unit I – Resource provisioning | 10 marks**

**Answer: No.** Cloud greatly *reduces* both problems through on-demand provisioning and elasticity, but cannot *entirely eliminate* them.
**Definitions:** *Over-provisioning* – allocating more resources than the workload needs (wasted cost). *Under-provisioning* – allocating fewer than needed (slow response, SLA violation, lost revenue).
**How cloud helps:** pay-as-you-go, auto scaling, rapid provisioning, right-sizing tools, serverless.
**Why the problems persist (with examples)**
1. **Provisioning delay/cold start:** a VM takes minutes to boot; a ticket-booking site hit by a sudden flash crowd is under-provisioned until instances come up.
2. **Imperfect forecasting and thresholds:** auto scaling reacts after CPU crosses 70%; short spikes end before scale-out completes.
3. **Coarse instance sizes:** needing 5 GB RAM but smallest fit is 8 GB → over-provisioned.
4. **Reserved/committed capacity:** instances reserved for peak sit idle off-peak.
5. **Quotas/regional capacity limits:** during a global event, the provider's limit stops scaling.
6. **Stateful/legacy applications** cannot scale horizontally, forcing larger-than-needed VMs.
7. **Idle/forgotten resources (VM sprawl)** and conservative safety margins to protect SLAs.
8. **Cost trade-off:** scale-in too early may cause under-provisioning; scale-out too early causes waste.
| | Over-provisioning | Under-provisioning |
|---|---|---|
| Effect | Wasted money | Poor performance, SLA breach |
| Example | VMs left running overnight | Exam portal crashing at start of exam |
**Mitigation:** predictive/scheduled scaling, right-sizing, pre-warming, spot/serverless, monitoring and cost alerts.
```
 Demand ~~~~/\~~~~       Capacity ---___--- (steps)   gap above demand = over, below demand = under
```
**Conclusion:** cloud makes resource matching far better but because of delays, granularity, forecasting error and limits, some over/under-provisioning always remains.

### Q6.b. Distinguish between – Full virtualization, Para virtualization and Hardware assisted Virtualization.
**Unit I – Virtualization | 10 marks**

**Introduction:** CPU virtualization techniques differ in how the hypervisor handles privileged instructions of the guest OS.
- **Full virtualization:** hypervisor completely emulates hardware; guest OS runs **unmodified**. Privileged/sensitive instructions are handled through **binary translation** and trap-and-emulate. e.g., VMware ESX (early), VirtualBox.
- **Para-virtualization:** guest OS **kernel is modified** to replace privileged instructions with **hypercalls** to the hypervisor; guest is aware it is virtualized. e.g., Xen PV, KVM virtio drivers.
- **Hardware-assisted virtualization:** CPU provides extensions (**Intel VT-x, AMD-V**) with a new privilege (root/non-root) mode; the guest executes directly and sensitive instructions cause hardware **VM exit** to the hypervisor. e.g., KVM, Hyper-V, ESXi, Xen HVM.
```
 Full:     Guest OS (unmodified) -> [Hypervisor: binary translation] -> Hardware
 Para:     Guest OS (modified)   -- hypercall --> [Hypervisor] -> Hardware
 HW-asst:  Guest OS (unmodified) -> Hardware (VT-x/AMD-V traps to) -> [Hypervisor]
```
| Basis | Full | Para | Hardware-assisted |
|---|---|---|---|
| Guest OS modification | None | Required | None |
| Technique | Binary translation, trap & emulate | Hypercalls | CPU extensions (root mode, VM exit) |
| Performance | Moderate (translation overhead) | High | High, near native |
| Hypervisor complexity | High | Medium | Low |
| Guest awareness | No | Yes | No |
| Compatibility | Any OS | Only portable/modified OS (not closed-source Windows) | Any OS on VT-capable CPU |
| Examples | VMware ESX (early), VirtualBox | Xen PV | KVM, Hyper-V, VMware ESXi, Xen HVM |
**Conclusion:** modern systems prefer hardware-assisted virtualization (often with para-virtual I/O drivers) for the best mix of compatibility and speed.

### Q7.a. Discuss the complementary role of SOA and cloud computing.
**Unit I – SOA | 10 marks**

**SOA:** Service-Oriented Architecture builds applications from loosely coupled, reusable, interoperable services with standard interfaces (SOAP/WSDL/REST, registry/UDDI, ESB). **Cloud computing:** on-demand, elastic, pay-per-use delivery of IT resources as services.
**Complementary roles**
| SOA provides | Cloud provides |
|---|---|
| Design principles – loose coupling, reuse, composition | Elastic infrastructure on which services run |
| Standard service interfaces and contracts | Pay-per-use, self-service provisioning |
| Orchestration/governance of services | Scalability, availability, global reach |
| Interoperability across heterogeneous systems | Multi-tenant shared resource pooling |
1. Cloud services (IaaS/PaaS/SaaS) are *themselves exposed as services* via SOA-style APIs; "everything as a service".
2. SOA services hosted in cloud gain elasticity, high availability and lower cost.
3. Cloud-hosted SOA services can be composed across organisations (hybrid/multi-cloud integration).
4. SOA supplies service governance, registry, ESB; cloud supplies runtime scaling and metering.
5. Together they speed development (service reuse) and deployment (cloud provisioning).
```
 Consumers -> [Service registry/API gateway/ESB] -> Service A | B | C  (loosely coupled)
                              hosted on  [ Cloud: IaaS + PaaS, elastic, pay-per-use ]
```
**Examples:** Salesforce APIs, AWS Lambda behind API Gateway, enterprise SOA moved to Azure.
**Conclusion:** SOA is the architectural style, cloud is the delivery platform – each enhances the other.

### Q7.b. What is Cloud native file system ? What are the challenges before a Cloud Native File System
**Unit II – File system | 10 marks**

**Definition:** A cloud-native file system is a **distributed, elastic file system designed specifically for cloud and containerised environments**, offering shared POSIX-like or API access, automatic scaling of capacity/performance, replication, multi-tenancy and integration with orchestrators (Kubernetes CSI, persistent volumes). Examples: Amazon EFS, Azure Files, Google Filestore, CephFS/Rook, GlusterFS, Portworx.
**Characteristics:** elastic scale, high availability via replication, pay-per-use, API-driven provisioning, multi-tenant isolation, container-native (dynamic volumes).
```
 Pods/VMs (many nodes) --> [CSI driver / NFS / API] --> Distributed storage cluster
                                  |-- metadata service (MDS) -- data nodes (replicated) --|
```
**Challenges**
1. **Scalability of metadata** – metadata servers become bottleneck.
2. **Consistency vs performance** (CAP trade-off, distributed locking).
3. **Network latency and throughput** – remote access vs local disks; throughput variability.
4. **Elasticity** – scaling capacity and performance dynamically without disruption.
5. **Multi-tenancy** – isolation, quotas, noisy neighbours, security and encryption.
6. **High availability and durability** – replication/erasure coding cost, failure recovery.
7. **Persistence for ephemeral containers** – data must outlive pods; data locality/data gravity.
8. **Portability and vendor lock-in** across clouds; compatibility (POSIX vs object semantics).
9. **Cost and operational complexity** – tiering, monitoring, backup.
**Conclusion:** cloud-native file systems must balance elasticity, performance, consistency and cost for stateful applications.

### Q8.a. What are the main challenges in "Determining the Expected Demand"? What are the Main Steps for Capacity Planning?
**Unit II – Capacity planning | 10 marks**

**Capacity planning (definition):** determining the amount of computing resources required to meet present and future demand at the required service level and minimum cost.
**Challenges in determining expected demand**
1. Unpredictable bursts/flash crowds and viral growth.
2. Seasonality and events (festivals, sales, exams) hard to model.
3. No historical data for new applications.
4. Forecast errors – wrong assumptions on user behaviour.
5. Diverse workload mix (CPU/memory/I-O intensive) and changing patterns.
6. Multi-tenant interference and shared-resource variability.
7. Insufficient/inaccurate monitoring data and metrics.
8. Trade-off between SLA risk (under-provisioning) and cost (over-provisioning).
9. Provisioning lead times, quotas, regional capacity.
10. Dependencies on other services and external factors.
**Main steps of capacity planning**
```
 1 Determine expected demand -> 2 Measure current capacity -> 3 Set service-level targets
 -> 4 Gap analysis -> 5 Choose strategy (lead/lag/match) & plan resources
 -> 6 Implement/provision -> 7 Monitor, review, re-plan  (loop back to 1)
```
1. **Determine expected demand** (characterise workload, forecast).
2. **Measure current capacity/utilisation** (baseline).
3. **Define SLA/performance requirements.**
4. **Analyse the gap** between demand and capacity.
5. **Select strategy** – lead, lag or match; reserved vs on-demand vs spot; scale-up vs scale-out.
6. **Implement/provision** the resources.
7. **Monitor, review and update** the plan continuously.

### Q8.b. For a relatively smaller and simpler system, the static load balancing approach can be more effective than dynamic approach. Justify the statement.
**Unit II – Load balancing | 10 marks**

**Static load balancing:** distribution decisions are made in advance by fixed rules (round robin, weighted round robin, hashing) using prior knowledge, ignoring current server state. **Dynamic load balancing:** decisions are made at run time using current load (least connections, response time).
**Justification for small, simple systems**
1. **Uniform workload and identical servers** – simple round robin already gives near-optimal balance.
2. **No monitoring overhead** – dynamic needs continuous status collection, communication and computation that can exceed the benefit.
3. **Simplicity/low cost** – easy configuration, deployment and debugging; no complex controller.
4. **Predictability** – deterministic behaviour, easier capacity analysis.
5. **Fewer failure points** – no extra monitoring/decision components to fail; no stale-information problems.
6. **Resource efficient** – no CPU/network spent on load information; small systems have little imbalance to correct.

| Static | Dynamic | |
|---|---|---|
| Information | Not needed at run time | Needs real-time state |
| Overhead | Very low | High |
| Complexity | Simple | Complex |
| Adapts to change | No | Yes |
| Best for | Small, uniform, predictable | Large, heterogeneous, bursty |

**Conclusion:** when load is uniform and predictable, the overhead and complexity of dynamic balancing outweigh its gains, so static balancing is more effective; dynamic becomes preferable as size and variability grow.
