# Unit I – Cloud Computing: Introduction, Services, Virtualization, Resource Pooling

**Syllabus:** Introduction, Benefits and challenges, Cloud computing services, Resource Virtualization, Resource pooling sharing and provisioning.

**Paper codes used in this file set:** `PYQ-A` = previous-year paper (Part A 10M / Part B 20M; "Why are managed… / Justify low initial investment" paper), `PYQ-B` = previous-year paper ("Draw lifecycle of a cloud service / Google file system" paper), `Mid-1`, `Mid-2` = current-year mid papers.

---

## 0. Unit I – Priority Map (based ONLY on the 4 papers supplied)

| Topic | Actual appearances | Priority |
|---|---|---|
| Virtualization types: Full / Para / Hardware-assisted | Mid-1 Q6b | **P1** |
| Hypervisor = Virtual Machine Monitor | PYQ-A Q5 | **P1** |
| Server virtualization products & vendors | PYQ-A Q6a | **P1** |
| Resource pooling architectures | PYQ-A Q6b | **P1** |
| Service delivery models (IaaS/PaaS/SaaS) | PYQ-A Q8b | **P1** |
| Benefits & challenges of cloud | PYQ-A Q1 | **P1** |
| Over- / under-provisioning | Mid-1 Q6a | **P1** |
| Cluster vs Grid vs Cloud | Mid-1 Q1 | P2 |
| Sandboxing vs virtualization | Mid-1 Q3 | P2 |
| SOA and cloud | Mid-1 Q7a | P2 |
| 'Low initial investment' philosophy | PYQ-A Q2 | P2 |
| OVF; VM image vs virtual appliance | PYQ-B Q7a | P2 |
| Resource provisioning types (static/dynamic/self), sharing | none directly | P3 (expected) |

> "Expected" means *my judgement from the syllabus*, not an actual question. No prediction is guaranteed.

---

## 1.1 Introduction to Cloud Computing

### A. Definition
**NIST:** Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (networks, servers, storage, applications, services) that can be rapidly provisioned and released with minimal management effort or service-provider interaction.

### B. Simple Explanation
Instead of buying and maintaining your own servers, you *rent* computing power, storage and software over the Internet and pay only for what you use — like electricity from the grid instead of owning a generator.

### C. Detailed Explanation
**Five essential characteristics (NIST):** On-demand self-service • Broad network access • Resource pooling (multi-tenant) • Rapid elasticity • Measured service (pay-per-use).

**Service models:** IaaS, PaaS, SaaS (see 1.3). **Deployment models:** Public, Private, Hybrid, Community.

| Deployment | Owned/used by | Example |
|---|---|---|
| Public | Provider, open to anyone | AWS, Azure, GCP |
| Private | Single organisation | VMware/OpenStack in-house cloud |
| Hybrid | Private + public linked | Bank: core data private, web front-end public |
| Community | Organisations with common concern | Government / research cloud |

### D. Diagram – Cloud Overview
```
 Users (browser/mobile/API)
        |  Internet
 +------v--------------------------------+
 |  SaaS  : Gmail, Salesforce            |
 |  PaaS  : App Engine, Azure App Service|
 |  IaaS  : EC2, Azure VM, Compute Engine|
 +---------------------------------------+
 |  Virtualization layer (Hypervisor)    |
 +---------------------------------------+
 |  Physical servers | storage | network |
 +---------------------------------------+
```

### E. Comparison – Cluster vs Grid vs Cloud (Mid-1 Q1)
| Feature | Cluster | Grid | Cloud |
|---|---|---|---|
| Coupling | Tightly coupled | Loosely coupled | Loosely coupled, virtualized |
| Nodes | Homogeneous | Heterogeneous | Heterogeneous, virtual |
| Location | Same LAN/site | Geographically distributed | Data centres, accessed over Internet |
| Admin domain | Single | Multiple (virtual organisations) | Provider-managed, multi-tenant |
| Purpose | High-performance / availability | Share resources for large scientific jobs | On-demand services, pay-per-use |
| Billing | None (owned) | Mostly none (shared contribution) | Metered, pay-as-you-go |
| Elasticity | No | Limited | Yes |
| Example | Beowulf, HPC cluster | Globus, SETI@home | AWS, Azure |

**Crisp one-liner:** *Cluster = one owner, one site, fast; Grid = many owners, many sites, shared for big jobs; Cloud = provider-owned virtualized resources delivered as metered services.*

### F. Real-world example
A college website: runs on a rented VM (IaaS); during admissions it adds VMs automatically and releases them later.

### G. Exam answer skeleton (2M / 10M)
Definition → 5 characteristics → service + deployment models → diagram → advantages → conclusion.

### H. Keywords
NIST, on-demand self-service, resource pooling, rapid elasticity, measured service, multi-tenancy, pay-as-you-go, public/private/hybrid/community.

---

## 1.2 Benefits and Challenges of Cloud Computing

### A. Definition
**Benefits** are the technical, economic and operational advantages of using cloud services; **challenges** are the risks and limitations that arise from relying on shared, remote, provider-managed infrastructure.

### B. Simple Explanation
Cloud saves money, time and effort and scales easily, but you depend on the Internet and on the provider, and you must trust them with your data.

### C. Detailed Explanation
| Benefits | Challenges |
|---|---|
| Low upfront cost (CapEx → OpEx) | Security & privacy of data |
| Pay-per-use billing | Vendor lock-in; portability/interoperability |
| Elastic scalability, rapid provisioning | Dependence on Internet connectivity, latency |
| High availability, disaster recovery | Downtime / outage of provider |
| Global reach; accessible anywhere | Compliance & data-residency laws |
| Automatic updates & maintenance | Limited control & visibility |
| Faster time-to-market | Hidden / unpredictable costs at scale |
| Focus on core business | Performance variability (noisy neighbours) |
| Green: higher utilisation of servers | Skill gap and migration complexity |

### D. Diagram
```
 CapEx model: [Buy servers] -> [Idle capacity] -> [Waste]
 Cloud model: [Rent on demand] -> [Scale up/down] -> [Pay for use]
```

### F. Example
Startup launches an app with zero hardware spending; Netflix scales streaming servers for peak evening hours.

### G. Exam answer skeleton (PYQ-A Q1, 2M)
Two short lists (3–4 each) with one-line explanation each.

### H. Keywords
CapEx vs OpEx, elasticity, lock-in, SLA, compliance, multi-tenancy, availability.

### 'Low initial investment' philosophy (PYQ-A Q2)
Cloud removes the need to buy servers, build data centres, buy cooling/power and hire large ops teams **before** the business earns revenue. Customers rent resources (OpEx), start small and grow. Justification points: (1) no hardware purchase / no CapEx; (2) pay-as-you-go; (3) no over-provisioning for peak; (4) provider shares cost across tenants (economies of scale); (5) faster break-even and lower financial risk for startups. *Caveat:* over a long period heavy steady workloads can cost more than owned hardware.

---

## 1.3 Cloud Computing Services (Service Delivery Models)

### A. Definition
Cloud services are computing capabilities delivered over the network as a service under one of three models: **IaaS** (infrastructure), **PaaS** (platform) and **SaaS** (software), often generalised as **XaaS**.

### B. Simple Explanation
IaaS gives you a bare computer, PaaS gives you a ready workplace to write programs, SaaS gives you the finished program.

### C. Detailed Explanation
- **IaaS:** virtual machines, storage, networks; user manages OS, middleware, apps. *Examples:* Amazon EC2, Azure VMs, Google Compute Engine. *Users:* system admins.
- **PaaS:** runtime, database, middleware, development tools; user manages app and data only. *Examples:* Google App Engine, Azure App Service, AWS Elastic Beanstalk, Heroku. *Users:* developers.
- **SaaS:** complete application over browser; provider manages everything. *Examples:* Gmail, Salesforce, Microsoft 365, Zoom. *Users:* end users.
- **XaaS / others:** DBaaS, FaaS (serverless), CaaS (containers), Storage-aaS, Security-aaS.

### D. Diagram – Responsibility Stack
```
Layer            | On-premise | IaaS | PaaS | SaaS
-----------------+------------+------+------+------
Application      |    You     | You  | You  | Prov
Data             |    You     | You  | You  | Prov
Runtime/Middlew. |    You     | You  | Prov | Prov
OS               |    You     | You  | Prov | Prov
Virtualization   |    You     | Prov | Prov | Prov
Servers/Storage  |    You     | Prov | Prov | Prov
Network          |    You     | Prov | Prov | Prov
```

### E. Comparison
| | IaaS | PaaS | SaaS |
|---|---|---|---|
| Control | Highest | Medium | Lowest |
| Flexibility | Highest | Medium | Low |
| Target user | IT admin | Developer | End user |
| Effort | High | Medium | Minimal |
| Example | EC2 | App Engine | Gmail |

### F. Example
Hosting a website: EC2 (install everything), Elastic Beanstalk (upload code), WordPress.com (just write posts).

### G. Exam answer skeleton (PYQ-A Q8b, 10M)
Intro → three models (definition, features, examples, users) → stack diagram → comparison table → deployment models (brief) → conclusion.

### H. Keywords
IaaS, PaaS, SaaS, XaaS, FaaS, shared responsibility, hypervisor, runtime, multi-tenancy.

---

## 1.4 Resource Virtualization

### A. Definition
Virtualization is the technique of creating a **virtual (logical) version of physical resources** — servers, storage, network, OS — by inserting a software layer (hypervisor) that abstracts hardware and lets multiple isolated virtual machines share it.

### B. Simple Explanation
One powerful physical computer is "sliced" into several independent virtual computers, each thinking it owns real hardware.

### C. Detailed Explanation
- **Hypervisor / Virtual Machine Monitor (VMM):** software that creates, runs and manages VMs and controls access to CPU, memory, I/O.
  - **Type 1 (bare-metal):** runs directly on hardware – VMware ESXi, Xen, Microsoft Hyper-V, KVM. Used in data centres.
  - **Type 2 (hosted):** runs on a host OS – VirtualBox, VMware Workstation.
- **Why "VMM"?** It *monitors and mediates* every VM's access to hardware: it traps privileged instructions, allocates resources, enforces isolation. Popek–Goldberg: a VMM provides *equivalence, resource control and efficiency*. Hence hypervisor ≡ VMM.
- **Types of virtualization:** server, storage, network, desktop, application, OS-level (containers), data.
- **Benefits:** better utilisation, isolation, consolidation, fast provisioning, migration, snapshots, DR.
- **Drawbacks:** overhead, single-host failure impact, VM sprawl, licensing.

**CPU virtualization techniques (Mid-1 Q6b):**
| | Full virtualization | Para-virtualization | Hardware-assisted |
|---|---|---|---|
| Guest OS | Unmodified | Modified (aware of hypervisor) | Unmodified |
| Mechanism | Binary translation + trap-and-emulate | Hypercalls replace privileged instructions | Intel VT-x / AMD-V; new CPU root mode, VM exit/entry |
| Performance | Moderate (translation overhead) | High | High (near native) |
| Compatibility | Any OS | Only OSes with ported kernel | Any OS on VT-capable CPU |
| Complexity | Hypervisor complex | Guest changes needed | Simplest software |
| Examples | VMware ESX (early), VirtualBox | Xen PV | KVM, VMware ESXi, Hyper-V, Xen HVM |

### D. Diagram – Hypervisor Types
```
 Type 1 (bare metal)         Type 2 (hosted)
 +----+ +----+ +----+        +----+ +----+
 | VM | | VM | | VM |        | VM | | VM |
 +----+ +----+ +----+        +----+ +----+
 +--------------------+      +--------------+
 |     Hypervisor     |      |  Hypervisor  |
 +--------------------+      +--------------+
 |     Hardware       |      |   Host OS    |
 +--------------------+      +--------------+
                             |   Hardware   |
                             +--------------+
```

### E. Server virtualization products & vendors (PYQ-A Q6a)
| Vendor | Product | Type / note |
|---|---|---|
| VMware | vSphere / ESXi, Workstation | Type 1 leader; vMotion, HA, DRS |
| Microsoft | Hyper-V | Type 1 built into Windows Server; Live migration |
| Citrix / Xen Project | XenServer / Xen | Open-source, para-virtualization origin |
| Red Hat / Linux | KVM (RHV), QEMU | Kernel module, hardware-assisted |
| Oracle | VirtualBox, Oracle VM | Type 2 / Xen-based |
| Amazon | Nitro (KVM-based) | Powers EC2 |
(Mention features: live migration, snapshots, high availability, resource scheduling.)

### Sandboxing vs Virtualization (Mid-1 Q3)
| | Sandboxing | Virtualization |
|---|---|---|
| Idea | Restrict an application's access inside an isolated environment | Emulate complete hardware to run a full guest OS |
| OS | Shares host OS kernel; app must suit that OS | Each VM has its own OS; any OS |
| Weight | Lightweight, quick start | Heavy, GBs, slower boot |
| Isolation | Policy-based (permissions) | Strong hardware-level |
| App design view | App is written for one platform; limited resources/permissions; ideal for untrusted code (browser, JVM, mobile apps) | App is unaware; can run unchanged on any OS; supports legacy apps, full environment |
| Example | Chrome sandbox, Java sandbox, Android apps | VMware, KVM VMs |

### OVF; VM Image vs Virtual Appliance (PYQ-B Q7a)
- **OVF (Open Virtualization Format):** DMTF open standard for packaging and distributing virtual machines/appliances in a vendor-neutral way. Package contains an **XML descriptor (.ovf)** (hardware, networks, configuration), **virtual disk files** (e.g., .vmdk), **manifest (.mf)** with checksums, optional certificate; single-file archive = **.ova**.
- **VM image:** a (template) file capturing a VM's disk(+config) – OS and data; generic, may need configuration.
- **Virtual appliance:** a pre-built, pre-configured VM image bundling OS + application stack (e.g., firewall, WordPress) to perform one task; ready to run, usually distributed as OVF.

| VM image | Virtual appliance |
|---|---|
| Generic disk image of OS/data | Purpose-built, pre-installed application + OS |
| Needs installation/config | Ready to deploy |
| Larger scope | Single function, minimal OS |
| e.g., Ubuntu AMI | e.g., pfSense appliance, Bitnami LAMP |

### F. Example
A data centre has 10 physical servers at 10% utilisation; virtualization consolidates them onto 2 hosts running 20 VMs.

### G. Exam answer skeleton
Definition → hypervisor types + diagram → full/para/HW-assisted table → benefits → products → conclusion.

### H. Keywords
Hypervisor, VMM, Type-1/2, binary translation, hypercall, VT-x/AMD-V, live migration, snapshot, OVF, OVA, VMDK, virtual appliance, consolidation, container.

---

## 1.5 Resource Pooling, Sharing and Provisioning

### A. Definition
**Resource pooling:** the provider aggregates physical/virtual resources (CPU, memory, storage, network) into shared pools dynamically assigned to multiple tenants. **Provisioning:** allocating and configuring those resources for a consumer on request. **Sharing:** multiple tenants using the same pool with isolation.

### B. Simple Explanation
Like a car-rental yard: all cars (resources) are in one pool, anyone can rent one, and it returns to the pool afterwards.

### C. Detailed Explanation
- **Types of pools:** physical server pool, virtual server pool, storage pool, network pool, CPU pool, memory pool.
- **Pool relationships:** *sibling pools* (same level, isolated), *nested pools* (sub-pools carved from a parent pool for departments/tenants).
- **Resource management system** tracks free/used capacity, applies allocation policies, enforces quotas/isolation.
- **Provisioning types:** *static* (pre-allocated before use), *dynamic* (on-demand as load changes), *self-service* (user via portal/API).
- **Over-provisioning:** allocating more than needed (waste, cost). **Under-provisioning:** allocating less than needed (SLA violation, poor performance).
- **Over-provisioning of physical capacity (overcommit):** provider allots more virtual CPU than physical to exploit statistical multiplexing.

### D. Diagram – Resource Pooling Architecture (PYQ-A Q6b)
```
 Physical resources            Virtualization          Pools               Tenants
 +----------+                  +-----------+     +--------------+
 | Servers  |---+              |           |---->| CPU pool     |--+   Tenant A
 +----------+   |              | Hypervisor|     +--------------+  |   (VMs)
 | Storage  |---+------------->|  + VMM    |---->| Memory pool  |--+-> Tenant B
 +----------+   |              |           |     +--------------+  |   (VMs)
 | Network  |---+              |           |---->| Storage pool |--+   Tenant C
 +----------+                  +-----------+     | Network pool |
                                     ^           +--------------+
                           Resource Management System
                      (allocation policy, monitoring, quota, billing)
 Nested pools:   Parent pool --> Sub-pool (Dept-1) , Sub-pool (Dept-2)
 Sibling pools:  Pool-1  |  Pool-2  (isolated, same level)
```

### Over-/Under-provisioning – does cloud eliminate it? (Mid-1 Q6a)
**No – cloud reduces but does not entirely eliminate.** Reasons with examples:
1. **Provisioning delay:** VM boot/launch takes minutes; flash crowd (ticket sale) → temporary under-provisioning.
2. **Forecast/threshold errors:** auto-scaling reacts after CPU crosses 70%; spikes shorter than the scale delay still hurt.
3. **Coarse instance sizes:** need 5 GB RAM but smallest suitable instance is 8 GB → over-provisioned.
4. **Reserved/committed capacity:** reserved instances bought for peak sit idle off-peak.
5. **Quotas & regional capacity limits:** provider limit stops scaling.
6. **Stateful/legacy apps** can't scale horizontally.
7. **Idle resources forgotten** (VM sprawl).
Mitigation: right-sizing, auto-scaling with predictive policies, spot/serverless, monitoring.

### E. Comparison
| | Over-provisioning | Under-provisioning |
|---|---|---|
| Resources | More than needed | Less than needed |
| Effect | Waste, higher cost | Slowness, failures, SLA breach, lost customers |
| Cloud remedy | Scale in, right-size | Scale out, auto-scaling |

### F. Example
Online exam portal reserved for peak 5,000 students (over-provisioned) vs. only 500 VMs (under-provisioned at exam start).

### G. Exam answer skeleton
Definition → pool types → architecture diagram → provisioning types → over/under table → benefits.

### H. Keywords
Pool, tenant, sibling/nested pool, multi-tenancy, provisioning, overcommit, quota, right-sizing, statistical multiplexing.

---

## 1.6 SOA and Cloud Computing (Mid-1 Q7a – complementary roles)

### A. Definition
**SOA (Service-Oriented Architecture)** is a design style in which applications are built from loosely coupled, reusable, interoperable *services* with standard interfaces (SOAP/WSDL/REST) that communicate over a network.

### C. Detailed – Complementary role
| SOA contributes | Cloud contributes |
|---|---|
| Architecture/design principles: reuse, loose coupling, composition | Elastic infrastructure to host services |
| Standard interfaces (WSDL, REST, UDDI registry, ESB) | On-demand scaling and pay-per-use |
| Service orchestration/governance | Self-service provisioning, multi-tenancy |
| Interoperability between systems | Global availability |
- Cloud services (SaaS/PaaS/IaaS) are exposed as services through SOA-style APIs.
- SOA services deployed in cloud gain scalability, availability, lower cost; cloud gains composability and integration.
- Both follow the "everything as a service" idea; SOA is *how to design*, cloud is *where/how to deliver*.

### D. Diagram
```
 Consumer -> [Service Registry] -> [ESB / API gateway] -> Service A | Service B | Service C
                                                           \__________ hosted on Cloud (elastic) ______/
```

### H. Keywords
SOA, loose coupling, ESB, WSDL, SOAP, REST, UDDI, service composition, orchestration.
