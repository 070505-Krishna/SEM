# Unit IV – Portability & Interoperability, Cloud Management & Programming Model Case Study, Popular Cloud Services

**Syllabus:** Portability and Interoperability Issues, Cloud Management and a Programming Model Case Study, Popular Cloud Services.

---

## 0. Unit IV – Priority Map (supplied papers)

Unit IV: 7 of 44 questions. Mostly descriptive/theory – relatively easy to score.

| Topic | Actual appearances | Priority |
|---|---|---|
| Cloud portability vs interoperability | PYQ-B Q3, Mid-2 Q3 (repeated) | **P1** |
| Interoperable multi-cloud architecture & limits | Mid-2 Q7a | **P1** |
| Cloud service management – three perspectives | PYQ-B Q7b | **P1** |
| Automated provisioning, orchestration, monitoring, policy mgmt, capacity planning | Mid-2 Q7b | **P1** |
| Lifecycle of a cloud service | PYQ-B Q2 | P2 |
| Role of Service Level Agreement | PYQ-A Q7b | P2 |
| Programming model case study (MapReduce) | none | P3 (expected) |
| Popular cloud services (AWS, Azure, GCP) | none | P3 (expected) |

> **Note on mapping:** Lifecycle of a cloud service, SLA and "three perspectives of cloud service management" fall under *Cloud Management* in the syllabus; I placed them here. The syllabus wording suggests a textbook chapter titled "Cloud Management and a Programming Model Case Study" – **please cross-check the "three perspectives" with your class notes/textbook**, since sources differ (see 4.2).

---

## 4.1 Portability and Interoperability Issues

### A. Definition
- **Portability:** ability to **move** an application or data from one cloud/platform to another (or back on-premise) with minimal re-engineering.
- **Interoperability:** ability of different cloud systems/services to **work together**, exchanging and using information, via common interfaces.

### B. Simple Explanation
Portability = moving house with your furniture fitting in the new house. Interoperability = two houses sharing a power/phone network so they can talk.

### C. Detailed Explanation
- **Portability types:** data portability, application portability, platform portability (VM/ image / container).
- **Interoperability types:** management, functional/API, data/format, identity, network interoperability.
- **Issues causing problems:**
  - **Vendor-specific APIs** (e.g., AWS Lambda API vs Azure Functions) – code rewrite.
  - **Managed/proprietary services** (DynamoDB, BigQuery, Cosmos DB) – no equivalent elsewhere.
  - **Proprietary data formats/schemas**, export cost (egress fees).
  - Different VM image formats (AMI, VHD, VMDK); different hypervisors.
  - Different IAM models, monitoring tools, network configs, SLAs/billing.
  - Lack of universal standards; vendor lock-in.
- **Standards/tools to improve:** OVF, OCCI, CDMI (storage), TOSCA (orchestration templates), libcloud/jclouds, Terraform, **containers + Kubernetes**, open APIs, S3-compatible storage, OIDC/SAML federation.

### E. Comparison
| | Portability | Interoperability |
|---|---|---|
| Concerns | Movement of app/data | Communication/cooperation between systems |
| Direction | One system → another (migration) | Between simultaneously running systems |
| Goal | Avoid lock-in, easy migration | Integrated hybrid/multi-cloud operation |
| Example | Move VM from AWS to Azure | App in private cloud calls storage in public cloud |
| Barrier | Proprietary APIs, formats | Different protocols, identity, data formats |

### Interoperable multi-cloud architecture (Mid-2 Q7a – private + two public clouds)
```
              +---------------- Cloud Management / Orchestration (Terraform, Kubernetes, TOSCA) ----------+
              |   Unified identity (SAML/OIDC federation)   Unified monitoring (Prometheus/Grafana)        |
              +--------------------+---------------------+--------------------------+-------------------+
                                   |                     |                          |
                         +---------v------+    +---------v------+        +---------v------+
                         | Private cloud  |    | Public cloud 1 |        | Public cloud 2 |
                         | (OpenStack/K8s)|    | (AWS)          |        | (Azure)        |
                         +--------+-------+    +--------+-------+        +--------+-------+
                                  |   VPN / Direct Connect / ExpressRoute (secure network mesh)  |
                                  +----- API gateway + message bus / S3-compatible data layer ---+
```
- **Common deployment interface:** containers + Kubernetes, IaC (Terraform/TOSCA), CI/CD.
- **Identity federation:** SSO with SAML/OIDC, central IdP, RBAC mapping.
- **Data exchange:** open formats (JSON/Parquet), S3-compatible APIs, message queues (Kafka), API gateway, replication/ETL.
- **Monitoring:** common tools (Prometheus, Grafana, OpenTelemetry) or cloud-neutral platform.
- **Network connectivity:** site-to-site VPN, dedicated links (Direct Connect, ExpressRoute), overlay/service mesh, DNS/global LB.
- **Limitations of complete interoperability:** proprietary services/APIs; "lowest-common-denominator" loses unique features; latency & egress cost; differing security/compliance models; skill/tool complexity; standards immature; versioning differences; management overhead.

### G. Exam skeleton
Definitions → issues table → standards → diagram → conclusion.
### H. Keywords
Vendor lock-in, OVF, OCCI, CDMI, TOSCA, federation, SAML/OIDC, egress fee, multi-cloud, hybrid cloud, API gateway.

---

## 4.2 Cloud Management

### A. Definition
**Cloud management** is the set of processes, tools and policies used to **provision, orchestrate, monitor, secure, optimise and bill** cloud resources and services throughout their lifecycle.

### B. Simple Explanation
The "control room" of the cloud: it creates resources when asked, watches them, enforces rules and sends the bill.

### C. Detailed Explanation
**Core functions:** automated provisioning, orchestration, monitoring & logging, policy-based management, capacity/cost management, SLA management, security/identity, billing/metering.

**Integrated solution (Mid-2 Q7b – predictable load + sudden promo spikes):**
1. **Automated provisioning:** templates/IaC (CloudFormation/Terraform), self-service portal/API; auto scaling groups launch instances on demand.
2. **Orchestration:** coordinates multi-step workflows – create network → VMs → DB → deploy app → register with LB; Kubernetes/Ansible/Step Functions.
3. **Monitoring:** metrics, logs, alerts (CloudWatch, Prometheus) feed scaling and SLA checks.
4. **Policy-based management:** rules such as "scale out when CPU>70%", "max 50 instances", scheduled scale for promo days, cost/security/tag policies, auto remediation.
5. **Capacity planning:** forecast baseline from history; reserved capacity for predictable load, on-demand/spot for bursts; pre-warm before promotional event.
```
 Portal/API --> [Provisioning] --> [Orchestrator] --> Resources (VMs/containers/DB)
                      ^                  |                     |
              [Policy engine] <-- [Monitoring & Analytics] <---+
                      ^                  |
              [Capacity planner/forecast] +--> Billing & SLA reports
```

### Three perspectives of cloud service management (PYQ-B Q7b)
Sources differ; the commonly used viewpoints (e.g., Open Group SOCCI framework) are those of the **Cloud Service Provider, Cloud Service Consumer and Cloud Service Developer**:
1. **Provider perspective:** manages data centre, infrastructure, capacity, SLA delivery, security, billing, service catalogue, operations (OSS/BSS).
2. **Consumer perspective:** selects services, monitors usage & performance, controls cost, ensures compliance, manages SLA and exit/lock-in.
3. **Developer/Integrator perspective:** designs, builds, tests, deploys and evolves services; integration/APIs; DevOps.
*(If your textbook defines them as business / operational / technical, state those using the same structure: business = billing, SLA, contracts; operational = provisioning, monitoring, incident; technical = architecture, integration.)*

### Lifecycle of a Cloud Service (PYQ-B Q2)
ITU-T describes four stages — **Design → Deployment → Operation → Retirement.**
```
 +--------+    +------------+    +-----------+    +------------+
 | Design | -> | Deployment | -> | Operation | -> | Retirement |
 +--------+    +------------+    +-----------+    +------------+
   ^                                 | monitor, scale, patch, bill |
   +----------- feedback / new version / change management -------+
```
- **Design:** requirements, service metadata, architecture, SLA/QoS definition.
- **Deployment:** build, test, provision resources, publish in service catalogue.
- **Operation:** provisioning for customers, monitoring, scaling, incident, billing, SLA enforcement, updates.
- **Retirement (decommissioning):** notify customers, migrate/archive data, release resources, delete data securely.
(A more detailed 6–8 step variant: requirement → design → development → testing → deployment → operation/monitoring → upgrade → retirement. Use whichever your notes follow.)

### Role of Service Level Agreement (PYQ-A Q7b)
**SLA** = formal contract between provider and consumer defining services, performance targets (availability e.g., 99.9%, response time, throughput), responsibilities, monitoring/reporting, penalties/credits, security, support and termination.
**Roles:** sets clear expectations; defines measurable QoS; basis for monitoring & billing; assigns responsibility; provides remedies/penalties (service credits); aids dispute resolution; builds trust and helps compare providers; drives automated management in cloud (most management is SLA-driven). **SLA lifecycle:** discover provider → define SLA → establish agreement → monitor → terminate/enforce penalties. **SLA components:** SLOs, metrics, exclusions, remedies.
**Keywords:** SLA, SLO, availability, uptime, service credit, QoS, penalty, monitoring.

### H. Keywords (4.2)
Provisioning, orchestration, IaC, policy, OSS/BSS, lifecycle, decommissioning, SLA, service catalogue, metering.

---

## 4.3 Programming Model Case Study – MapReduce / Hadoop (typical)

### A. Definition
**MapReduce** is a programming model for processing large data sets in parallel on a cluster: the **Map** function emits intermediate (key, value) pairs; the **Reduce** function aggregates all values of each key.

### B. Simple Explanation
To count words in a huge library: many people count words in different books (map); then totals for each word are added together (reduce).

### C. Working
Input split → Map tasks (local to data) → **Shuffle & sort** (group by key) → Reduce tasks → output to HDFS. Framework handles scheduling, data locality, fault tolerance (re-execute failed tasks), and speculative execution.
```
 Input -> [Split1]->Map -\
          [Split2]->Map --+--> Shuffle/Sort --> Reduce --> Output (HDFS)
          [Split3]->Map -/
 Word count: Map: (word,1)   Reduce: (word, sum)
```
**Example – word count:** map("cloud is cloud") → (cloud,1)(is,1)(cloud,1); reduce → (cloud,2),(is,1).
**Uses:** log analysis, indexing, ML. **Cloud services:** Amazon EMR, Azure HDInsight, Google Dataproc.
**Other cloud programming models (brief):** thread/task model (Aneka), workflow, actor model, serverless/event-driven (Unit V).
**Keywords:** map, reduce, shuffle, split, combiner, data locality, JobTracker/ResourceManager, fault tolerance.

---

## 4.4 Popular Cloud Services

| Provider | Compute | Storage | Database | PaaS/Other |
|---|---|---|---|---|
| **AWS** | EC2, Lambda, ECS/EKS | S3, EBS, EFS, Glacier | RDS, Aurora, DynamoDB | Elastic Beanstalk, CloudFront, CloudWatch, IAM |
| **Microsoft Azure** | Virtual Machines, Functions, AKS | Blob, Disk, Files | Azure SQL, Cosmos DB | App Service, Azure AD (Entra ID), Azure Monitor |
| **Google Cloud** | Compute Engine, Cloud Functions, GKE | Cloud Storage, Filestore | Cloud SQL, Spanner, Bigtable | App Engine, BigQuery, Dataproc |
| Others | – | Dropbox | – | **Salesforce (SaaS)**, IBM Cloud, Oracle Cloud |

- **Examples by model:** IaaS – EC2; PaaS – App Engine/Elastic Beanstalk; SaaS – Gmail, Salesforce, Office 365.
- Use these as examples in answers on service models, scaling, storage.
**Keywords:** EC2, S3, EBS, RDS, Lambda, Azure VM, Blob, GCE, BigQuery, App Engine.
