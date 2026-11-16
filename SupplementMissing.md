# Supplement – Missing / Thin Syllabus Topics (All Units)

Use this together with the unit notes. Each section says which syllabus topic it completes.
**Please verify Unit IV items against your class textbook**, since the syllabus wording ("Cloud Management and a Programming Model Case Study") points to a specific book chapter whose exact case study I could not confirm.

---
# UNIT I

## S1.1 Resource Sharing and Provisioning (completes "Resource pooling sharing and provisioning")

### A. Definition
- **Resource sharing:** multiple tenants/VMs use the same physical resources at the same time, with isolation and fair allocation (multi-tenancy).
- **Resource provisioning:** the process of selecting, allocating, configuring and delivering resources (VMs, storage, network) to a consumer so an application can run.

### B. Simple Explanation
Sharing = many people use one apartment building. Provisioning = giving a person the key to a flat that matches the request.

### C. Detailed Explanation
**How sharing works**
- Hypervisor schedules vCPUs on physical cores (time-slicing), maps virtual memory, shares storage/network via virtual switches.
- **Overcommitment:** total virtual resources > physical resources, exploiting the fact that tenants rarely peak together (**statistical multiplexing**).
- **Isolation and fairness:** quotas, shares/limits/reservations, resource groups, QoS.
- Risks: noisy neighbour, security leakage, unpredictable performance.

**Types of provisioning**
| Type | Meaning | Pros | Cons |
|---|---|---|---|
| Static (advance) | Resources reserved up front for peak | Predictable performance | Over-provisioning, cost |
| Dynamic (on-demand) | Allocated/released as load changes | Cost-efficient, elastic | Delay, needs monitoring |
| Self-provisioning (user) | Customer requests via portal/API | Fast, no admin | Needs governance |
| Predictive | Forecast-based | Avoids lag | Forecast errors |

**VM provisioning steps:** request (portal/API) → authentication & quota check → scheduler picks host (placement policy) → image fetched → vCPU/RAM/disk/network attached → VM boots → IP assigned → metering starts.

**VM placement policies:** first-fit, best-fit, load-balancing (spread), energy-aware (consolidate).

**Provisioning models by commitment:** on-demand, reserved, spot/preemptible.

### D. Diagram
```
 User request -> [Portal/API] -> [Admission & quota check] -> [Scheduler / placement]
      -> [Resource pool] -> VM created (image + CPU/RAM/disk/net) -> [Metering/Billing]
```

### E. Comparison – Static vs Dynamic provisioning
| | Static | Dynamic |
|---|---|---|
| Timing | Before use | During run time |
| Sizing | Peak | Actual demand |
| Cost | Higher | Lower |
| Risk | Over-provisioning | Under-provisioning during lag |

### F. Example
Exam portal: static reservation of 100 VMs for the exam week vs. auto-provisioning 10→100 VMs only during exam hours.

### G. Exam answer outline (10M)
Definition → sharing mechanism (hypervisor, overcommit) → provisioning types table → steps diagram → over/under-provisioning → conclusion.

### H. Keywords
Multi-tenancy, overcommit, statistical multiplexing, quota, placement policy, on-demand, reserved, spot, self-service, metering.

---
# UNIT II

## S2.1 Storage Technologies in Cloud (completes "File System and Storage")

### A. Definition
Cloud storage technologies are the storage architectures and services (DAS, NAS, SAN, block, file, object) that providers use to store data durably, scalably and accessibly.

### C. Detailed Explanation
| Technology | Access | Description | Cloud example |
|---|---|---|---|
| **DAS** (Direct Attached Storage) | Block, local | Disk attached to one server | Instance store (ephemeral) |
| **NAS** (Network Attached Storage) | File (NFS/SMB) | Shared file server over LAN | Amazon EFS, Azure Files |
| **SAN** (Storage Area Network) | Block over Fibre Channel/iSCSI | Pooled block storage network | Amazon EBS (conceptually) |
| **Object storage** | HTTP/REST, key + metadata | Flat namespace of objects | S3, Azure Blob, Google Cloud Storage |

**Object storage features:** unlimited scale, 11 nines durability (S3 advertises 99.999999999%), versioning, lifecycle rules, storage classes (hot/cool/archive), eventual/strong consistency, accessed by API not mount.

**RAID basics:** RAID 0 striping (speed, no redundancy), RAID 1 mirroring, RAID 5 striping + parity, RAID 10 mirror+stripe. Cloud providers mostly use **replication or erasure coding** across nodes instead of traditional RAID.

**Storage tiering:** hot (frequent access) → cool/infrequent → archive (Glacier). Reduces cost.

**Data durability techniques:** replication (3 copies, multi-AZ), erasure coding, checksums, versioning, backup/snapshots.

**Storage as a service:** pay for GB-month + requests + transfer; features: encryption, access control (IAM/ACLs), geo-replication.

### D. Diagram
```
 Applications
   |-- block (SAN/EBS)  --> VM disks, databases
   |-- file  (NAS/EFS)  --> shared folders, containers
   |-- object (S3)      --> images, backups, logs, big data
 Storage cluster: replicated/erasure-coded across racks and availability zones
```

### E. Comparison – Block vs File vs Object
| | Block | File | Object |
|---|---|---|---|
| Unit | Fixed blocks | Files/folders | Objects + metadata |
| Access | Attached to VM | NFS/SMB mount | REST API |
| Scale | Moderate | Moderate–high | Massive |
| Best for | Databases, boot disks | Shared content | Media, backups, data lakes |

### G. Exam answer outline
Definition → storage types table → object storage features → managed vs unmanaged → durability/tiering → conclusion.

### H. Keywords
DAS, NAS, SAN, block, file, object, RAID, replication, erasure coding, durability, tiering, versioning, lifecycle policy.

---
# UNIT III
*Unit III topics were all covered but briefly. Two short additions for completeness:*

## S3.1 Multi-tenancy – extra points
- **Tenant isolation levels:** network (VPC/VLAN), compute (VM/container), data (separate DB/schema/row-level), encryption keys per tenant.
- **Tenant-aware features:** per-tenant configuration/branding, metering/billing, throttling/quotas to prevent noisy neighbours, tenant-aware backup/restore.
- **Trade-off:** cost efficiency ↑ with sharing; isolation/security ↓ with sharing.

## S3.2 Database technology – cloud database services
| Type | Service examples |
|---|---|
| Managed relational | Amazon RDS/Aurora, Azure SQL, Cloud SQL |
| Key-value/document | DynamoDB, Cosmos DB, MongoDB Atlas, Firestore |
| Wide-column | Cassandra, Bigtable |
| In-memory | ElastiCache (Redis), Azure Cache |
| Data warehouse | Redshift, BigQuery, Synapse |
| Deployment options | DBaaS (managed) vs self-managed on VMs |

---
# UNIT IV

## S4.1 Cloud Management – Standards, Tools and Responsibilities (completes "Cloud Management")

### A. Definition
Cloud management comprises the standards, tools and shared responsibilities through which providers and consumers manage cloud services across provisioning, operation, SLA and billing.

### C. Detailed Explanation
**Cloud management standards (examples)**
| Standard / body | Purpose |
|---|---|
| **DMTF CIMI** (Cloud Infrastructure Management Interface) | Standard REST API for managing IaaS |
| **OVF** (DMTF) | VM packaging/portability |
| **OCCI** (Open Grid Forum) | Open API for IaaS management |
| **CDMI** (SNIA) | Cloud data/storage management interface |
| **TOSCA** (OASIS) | Topology & orchestration templates |
| **NIST Cloud Reference Architecture** | Roles: consumer, provider, auditor, broker, carrier |
| **ITU-T Y.3500 series, ISO/IEC 17788/17789** | Cloud vocabulary, reference architecture |

**Cloud management tools**
| Category | Tools |
|---|---|
| Provider-native | AWS Management Console/CloudFormation/CloudWatch, Azure Portal/ARM/Monitor, Google Cloud Console |
| Open source IaaS management | OpenStack, Apache CloudStack, Eucalyptus |
| Orchestration/IaC | Terraform, Ansible, Kubernetes, Puppet, Chef |
| Multi-cloud management | RightScale, VMware vRealize/Aria, Scalr, CloudHealth |
| Monitoring | Nagios, Prometheus, Grafana, Datadog |

**Responsibilities – provider vs consumer**
| Provider | Consumer |
|---|---|
| Data-centre facilities, hardware, network | Choosing right service/provider and plan |
| Hypervisor/platform security & patching | Securing own OS/apps/data (depending on IaaS/PaaS/SaaS) |
| Capacity, availability, meeting SLA | Monitoring usage and cost, ensuring SLA compliance |
| Metering, billing, support | Identity & access management, encryption keys |
| Compliance certifications | Own regulatory compliance, backups, exit strategy |

**SLA lifecycle (detail):** 1) Discover service provider & SLA templates → 2) Define SLA (SLOs, metrics, penalties) → 3) Establish agreement (negotiate/sign) → 4) Monitor SLA violations → 5) Terminate or renew; enforce penalties. 

### D. Diagram
```
 Consumer <-- SLA --> Provider
   |  uses CIMI/OCCI/CDMI APIs, tools (Terraform, OpenStack, monitoring)
   v
 Cloud resources: provision -> operate/monitor -> bill -> retire
```

### G. Exam answer outline
Definition → management functions → standards table → tools → provider/consumer responsibilities → SLA lifecycle → conclusion.

### H. Keywords
CIMI, OCCI, CDMI, TOSCA, OVF, OpenStack, CloudStack, Terraform, CloudWatch, SLA lifecycle, responsibilities.

---
## S4.2 Programming Model Case Study – additional models (completes "Programming Model Case Study")
*My earlier notes used MapReduce. Since the syllabus does not name the case study, add whichever your textbook/class used. Common ones:*

**1. Hadoop MapReduce:** (see unit_4.md §4.3) – batch processing on HDFS.

**2. Aneka (Manjrasoft) – .NET-based cloud application platform (PaaS)**
- Supports multiple programming models: **Task model** (independent tasks), **Thread model** (distributed threads), **MapReduce model**.
- Components: Aneka Container (runs on each node, hosts services), Master/Worker nodes, scheduling & resource provisioning (can acquire VMs from public clouds), SLA/QoS-aware scheduling, accounting.
```
 Application -> Programming model (Task | Thread | MapReduce)
            -> [Aneka Master: scheduler, provisioning] -> [Aneka Workers (containers)] -> local/cloud VMs
```

**3. Message-passing / workflow models:** workflow engines orchestrate dependent tasks (DAGs), e.g., Apache Airflow, AWS Step Functions.

**4. Event-driven / serverless model** – see Unit V.

**5. Google App Engine programming model:** stateless request handlers, datastore, auto-scaling sandbox.

Keywords: task model, thread model, container, scheduler, workflow, DAG, sandbox.

---
## S4.3 Popular Cloud Services – detail (completes "Popular Cloud Services")

### Amazon Web Services (AWS)
- **EC2:** resizable virtual servers; instance types (general, compute, memory, GPU), AMIs, security groups, EBS volumes, pricing (on-demand, reserved, spot).
- **S3:** object storage with buckets, versioning, lifecycle, storage classes.
- **EBS** block volumes, **EFS** file system, **RDS/Aurora** managed SQL, **DynamoDB** NoSQL, **VPC** networking, **ELB**, **Auto Scaling**, **CloudFront** CDN, **IAM**, **Lambda**, **CloudWatch**, **Elastic Beanstalk** (PaaS), **EKS** (Kubernetes).

### Microsoft Azure
- **Virtual Machines**, **Blob/Disk/File Storage**, **Azure SQL, Cosmos DB**, **App Service** (PaaS), **Azure Functions**, **AKS**, **Azure AD/Entra ID**, **Azure Monitor**, **Virtual Network**, **CDN**.

### Google Cloud Platform (GCP)
- **Compute Engine** (IaaS), **App Engine** (PaaS – languages, auto scaling, sandbox, Datastore/Firestore), **Cloud Storage**, **Cloud SQL, Spanner, Bigtable**, **BigQuery** (data warehouse), **GKE**, **Cloud Functions**, **Pub/Sub**.

### Salesforce (SaaS/PaaS)
CRM SaaS; **Force.com/Heroku** as PaaS; multi-tenant metadata-driven architecture.

### Others
Dropbox/Google Drive (storage SaaS), Microsoft 365/Google Workspace (productivity SaaS), IBM Cloud, Oracle Cloud, Alibaba Cloud.

| Service need | AWS | Azure | GCP |
|---|---|---|---|
| Virtual machine | EC2 | Virtual Machines | Compute Engine |
| Object storage | S3 | Blob Storage | Cloud Storage |
| PaaS | Elastic Beanstalk | App Service | App Engine |
| Serverless | Lambda | Functions | Cloud Functions |
| Kubernetes | EKS | AKS | GKE |
| NoSQL | DynamoDB | Cosmos DB | Bigtable/Firestore |

Keywords: EC2, S3, AMI, EBS, VPC, App Engine, BigQuery, Cosmos DB, Force.com.

---
# UNIT V

## S5.1 Stateless and Stateful Microservices – detail

### A. Definition
- **Stateless microservice:** does not retain client/session data between requests in its own memory/disk; every request carries all information needed; state is externalised (database, cache, token).
- **Stateful microservice:** stores data/session state that must persist across requests or restarts (databases, message brokers).

### C. Detailed Explanation
- Stateless: any replica can serve any request → trivial horizontal scaling, rolling updates, self-healing (replace pod); use **JWT tokens**, Redis for sessions. Run as Kubernetes **Deployment**.
- Stateful: needs stable identity, stable storage and ordered start/stop. Kubernetes **StatefulSet**: stable pod names (db-0, db-1), **PersistentVolumeClaims** per pod, **headless Service** for DNS. Challenges: replication, backup, leader election, scaling.
- Design best practice: keep business services stateless, push state into managed data services.

| | Stateless | Stateful |
|---|---|---|
| Example | Payment API, product catalogue API | MySQL, Cassandra, Kafka, Redis cluster |
| Scaling | Simple | Complex |
| Recovery | Replace instantly | Needs same volume/identity |
| K8s resource | Deployment | StatefulSet + PV |

```
 Stateless:  Client -> LB -> [Pod1][Pod2][Pod3] -> external DB/cache (state)
 Stateful :  [db-0 + PV0]  [db-1 + PV1]  [db-2 + PV2]   (replicated, stable identity)
```
Keywords: stateless, stateful, StatefulSet, PV/PVC, headless service, JWT, externalised state.

---
## S5.2 CI/CD Tools – Spinnaker, GitHub Actions, GitLab CI (detail)

### Spinnaker
- Open-source, multi-cloud **continuous delivery** platform (created at Netflix, now with Google/CD Foundation).
- **Concepts:** Applications, Clusters, Server groups, Pipelines (stages: bake, deploy, manual judgment, canary analysis), Triggers (Git, Jenkins, image push).
- **Deployment strategies:** blue/green (red/black), rolling, **canary** analysis (Kayenta).
- Cloud targets: AWS, GCP, Azure, Kubernetes.
- **Services:** Deck (UI), Gate (API), Orca (orchestration), Clouddriver, Front50, Igor, Rosco, Echo.
- Not a CI tool – pairs with Jenkins/GitHub Actions to supply images.
```
 Trigger (new image) -> Bake -> Deploy to staging -> Manual judgment -> Canary -> Deploy to prod
```

### GitHub Actions
- CI/CD integrated in GitHub; **workflows** (YAML in `.github/workflows/`) triggered by **events** (push, pull_request, schedule); contain **jobs** made of **steps** running on **runners** (GitHub-hosted/self-hosted); reuse via **Actions marketplace**; secrets store.
```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build image
        run: docker build -t myapp:${{ github.sha }} .
      - name: Test
        run: pytest
```

### GitLab CI/CD
- Built into GitLab; pipeline defined in `.gitlab-ci.yml`; **stages** (build, test, deploy) containing **jobs**; executed by **GitLab Runners**; built-in container registry, environments, Auto DevOps, review apps.
```yaml
stages: [build, test, deploy]
build-job:
  stage: build
  script: [docker build -t myapp .]
test-job:
  stage: test
  script: [pytest]
deploy-job:
  stage: deploy
  script: [kubectl apply -f k8s/]
  when: manual
```

### Jenkins pipeline example (for completeness)
```groovy
pipeline {
  agent any
  stages {
    stage('Build'){ steps { sh 'mvn package' } }
    stage('Test') { steps { sh 'mvn test' } }
    stage('Docker'){ steps { sh 'docker build -t myapp .' } }
    stage('Deploy'){ steps { sh 'kubectl apply -f k8s/' } }
  }
}
```

### Comparison
| Tool | Hosted/self | Config | Strength |
|---|---|---|---|
| Jenkins | Self-hosted | Jenkinsfile (Groovy) | Plugins, flexibility |
| Spinnaker | Self-hosted | UI/JSON pipelines | Multi-cloud CD, canary |
| Argo CD | In-cluster | K8s manifests in Git | GitOps for K8s |
| GitHub Actions | SaaS (+self runners) | YAML workflows | Tight GitHub integration |
| GitLab CI | SaaS/self-managed | `.gitlab-ci.yml` | All-in-one DevOps platform |

Keywords: pipeline-as-code, runner, stage, job, workflow, canary, blue/green, bake.

---
## S5.3 Getting Started with Azure Functions (detail)

### A. Definition
**Azure Functions** is Microsoft's serverless compute service that runs event-triggered code without managing servers, billed per execution (Consumption plan).

### C. Concepts
- **Function App:** container/host for one or more functions sharing config and scaling.
- **Triggers** (what starts a function – exactly one): HTTP, Timer, Blob, Queue, Service Bus, Event Grid, Cosmos DB change feed.
- **Bindings** (declarative input/output connections to data): e.g., read a Blob, write to a Queue without SDK code.
- **Hosting plans:** Consumption (auto-scale, pay per execution), Premium (pre-warmed, no cold start, VNET), Dedicated/App Service plan.
- **Durable Functions:** stateful workflows (function chaining, fan-out/fan-in, human interaction).
- Languages: C#, JavaScript/TypeScript, Python, Java, PowerShell.

### Getting started steps
1. Create **Function App** (portal / VS Code / Azure CLI / Core Tools).
2. Choose runtime and plan; create storage account (required).
3. Add a function from template (e.g., HTTP trigger).
4. Write code, test locally (`func start`), deploy (`func azure functionapp publish <app>`).
5. Monitor with Application Insights.
```python
import azure.functions as func
app = func.FunctionApp()

@app.route(route="hello")
def hello(req: func.HttpRequest) -> func.HttpResponse:
    name = req.params.get("name", "World")
    return func.HttpResponse(f"Hello, {name}!")
```
```
 Trigger (HTTP/Queue/Timer) -> [Function] -> Output binding (Blob/Queue/Cosmos DB)
```
Keywords: function app, trigger, binding, Consumption plan, Premium plan, Durable Functions, Application Insights.

---
## S5.4 Getting Started with Google Cloud Functions (detail)

### A. Definition
**Google Cloud Functions** is Google Cloud's serverless FaaS service that runs single-purpose functions in response to events or HTTP requests, with automatic scaling and pay-per-use billing. (Newer 2nd-generation functions are built on Cloud Run and Eventarc; check current docs for limits.)

### C. Concepts
- **Triggers:** HTTP(S), **Pub/Sub**, **Cloud Storage** events (object created/deleted), Firestore, Firebase, Eventarc events.
- **Runtimes:** Node.js, Python, Go, Java, .NET, Ruby, PHP.
- **Scaling:** automatic instances, can scale to zero; configurable min/max instances (to reduce cold starts), concurrency in 2nd gen.
- **Security:** IAM service account per function, VPC connector for private resources.
- Related: Cloud Run (containers), Workflows (orchestration), Cloud Scheduler.

### Getting started steps
1. Enable Cloud Functions and Cloud Build APIs in a project.
2. Write function (e.g., Python HTTP function).
3. Deploy: `gcloud functions deploy hello --runtime python312 --trigger-http --allow-unauthenticated`
4. Invoke via URL; view logs in Cloud Logging.
```python
import functions_framework

@functions_framework.http
def hello(request):
    name = request.args.get("name", "World")
    return f"Hello, {name}!"
```

### Comparison of the three platforms
| | AWS Lambda | Azure Functions | Google Cloud Functions |
|---|---|---|---|
| Unit | Function | Function App | Function |
| Key triggers | S3, API Gateway, SQS, DynamoDB | Blob, Queue, Timer, Event Grid | Pub/Sub, Cloud Storage, HTTP |
| Workflow/orchestration | Step Functions | Durable Functions | Workflows |
| Special | 
