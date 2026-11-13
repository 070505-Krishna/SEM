# Unit III – Multi-tenancy, Data in Cloud, Databases, CDN, Security, Privacy & Compliance

**Syllabus:** Multi-tenant Software, Data in Cloud, Database Technology, Content Delivery Network, Security Reference Model, Security Issues, Privacy and Compliance Issues.

---

## 0. Unit III – Priority Map

Unit III appears **only in Mid-2** (4 of 44 supplied questions) and never in the two older papers. It has seven broad topics (heavy to cover fully). Treated as **basic preparation + short-answer unit** unless you choose it for descriptive preparation.

| Topic | Actual appearances | Priority |
|---|---|---|
| Relational vs NoSQL; replication, partitioning, caching | Mid-2 Q1, Q6a | **P1** |
| CDN & caching security | Mid-2 Q6b | **P1** |
| Security threats, IAM, least privilege, audit logging | Mid-2 Q2 | **P1** |
| Multi-tenant software | none | P2 (expected) |
| Security reference model / shared responsibility | none | P2 (expected) |
| Privacy & compliance (GDPR, HIPAA, PCI-DSS, DPDP) | none | P2–P3 (expected) |
| Data in cloud (lifecycle, location, encryption) | none | P3 |

---

## 3.1 Multi-tenant Software

**Definition:** Architecture in which a single instance of software serves multiple customers (**tenants**), with each tenant's data and configuration logically isolated.

**Simple:** An apartment building — many families (tenants), one building, private flats.

**Detail**
- Isolation models: (1) *separate database per tenant* (strongest isolation, costly); (2) *shared database, separate schema*; (3) *shared database, shared schema* with TenantID column (cheapest, weakest isolation).
- Benefits: lower cost, higher utilisation, easy upgrades, central maintenance.
- Challenges: security isolation, "noisy neighbour" performance, customisation per tenant, tenant-aware backup/billing, compliance.

```
 Tenant A  Tenant B  Tenant C
    \         |         /
   [ Single app instance + tenant-aware logic ]
   [ DB: Tenant-ID  | data ]
```
**Examples:** Salesforce, Gmail for Business, Microsoft 365. **Keywords:** tenant, isolation, shared schema, noisy neighbour, customisation, TenantID.

---

## 3.2 Data in Cloud

**Definition:** Data stored, processed and transferred using cloud storage and database services, covering its full lifecycle: create → store → use → share → archive → destroy.

**Detail**
- Forms: structured (tables), semi-structured (JSON), unstructured (files, media).
- Storage services: object, block, file, DB-as-a-service, data warehouse/lake.
- Key issues: **location/residency**, **durability & replication**, **backup/DR**, **encryption at rest/in transit**, **access control**, **data lineage**, **deletion/remanence**, **vendor lock-in**.
- Data security lifecycle & big-data growth drive choice of storage tiers (hot/cool/archive).

**Keywords:** data lifecycle, durability, replication, residency, encryption at rest, tiering, backup.

---

## 3.3 Database Technology in Cloud

**Definition:** Cloud databases are database systems delivered as managed or self-managed services on cloud infrastructure, spanning relational (SQL) and NoSQL models.

### Relational vs NoSQL (Mid-2 Q1)
| | Relational (SQL) | NoSQL |
|---|---|---|
| Consistency | Strong, **ACID** transactions | Often eventual consistency (**BASE**), tunable |
| Schema | Fixed, predefined | Flexible/schema-less |
| Scaling | Mainly vertical; horizontal is hard (sharding) | Designed for horizontal scale-out |
| Data model | Tables, joins | Key-value, document, column-family, graph |
| Examples | MySQL, PostgreSQL, Amazon RDS/Aurora, Azure SQL, Cloud Spanner | DynamoDB, MongoDB, Cassandra, Cosmos DB, Bigtable, Redis, Neo4j |
| Best for | Banking, orders, joins | Big data, catalogs, sessions, real-time, global scale |

**CAP theorem:** distributed system can guarantee only two of Consistency, Availability, Partition tolerance (partitions unavoidable → choose CP or AP).

**Recommendation for a globally distributed application:** polyglot approach — relational (or NewSQL such as Spanner/Aurora Global) for transactional, strongly consistent data (orders, payments) + NoSQL (DynamoDB/Cassandra/Cosmos) for catalog, sessions, user profiles with multi-region replication + cache (Redis) for hot reads.

### Techniques for latency, inconsistent reads, storage cost (Mid-2 Q6a)
- **Replication:** master–replica (read replicas near users → lower read latency, but replica lag causes stale reads); multi-master/multi-region (low write latency, conflict resolution). Fix inconsistent reads using read-your-writes, session consistency, quorum reads (R+W>N).
- **Partitioning/sharding:** horizontal split by key (customer ID/region) → spreads load, reduces per-node size; pitfalls: hot shards, cross-shard joins.
- **Caching:** Redis/Memcached/CDN in front of DB; cache-aside, TTL, invalidation.
- **Choice of DB:** relational for ACID entities; NoSQL for high-volume denormalised reads.
- **Storage cost:** tiering/archiving old data, compression, TTL, columnar formats.
- **Suggested combination:** regional read replicas + sharded NoSQL for catalog + cache + relational primary for orders + archive to cold storage.

```
 Users(IN)--> Cache --> Read replica (Asia)  \
 Users(EU)--> Cache --> Read replica (EU)     >-- async replication <-- Primary DB (orders, ACID)
 Users(US)--> Cache --> Shard 1 | Shard 2 | Shard 3 (NoSQL catalog)
```
**Keywords:** ACID, BASE, CAP, replication lag, sharding, quorum, eventual consistency, NewSQL, cache-aside, TTL.

---

## 3.4 Content Delivery Network (CDN)

**Definition:** A geographically distributed network of **edge servers (PoPs)** that cache and deliver web content from the location nearest to the user.

**Simple:** Local warehouses of a courier company – items are delivered from the nearest warehouse, not from the factory.

**Working:** User requests → DNS routes to nearest edge → *cache hit*: served instantly; *cache miss*: edge fetches from **origin**, stores per **TTL**, serves.
```
 User --> [Edge PoP nearest] --hit--> content
                |miss
                v
           [Origin server]
```
**Benefits:** low latency, reduced origin load/bandwidth, DDoS absorption, high availability, TLS offload. **Examples:** Amazon CloudFront, Akamai, Cloudflare, Azure CDN.

### Security & privacy of caching personalised content (Mid-2 Q6b)
**Risks:** private/personalised responses cached and served to other users (data leakage); cache poisoning; web cache deception; stale/sensitive data persisting; shared-cache exposure; insecure origin access; token leakage in URLs.
**Controls / policies:**
- Static public assets: `Cache-Control: public, max-age=…` (long TTL, versioned file names).
- Personalised/API responses: `Cache-Control: private` or `no-store`; `Vary: Cookie/Authorization` or bypass cache for authenticated paths.
- Access restrictions: signed URLs/signed cookies, origin access identity (origin accepts only CDN), WAF, rate-limiting, HTTPS/TLS everywhere, geo-restrictions.
- Strategy: split domains/paths (`static.example.com` cached, `/api/` uncached); edge-side includes or fetch personalised fragments via API; validate cache keys; purge/invalidate on logout or change.

**Keywords:** edge server, PoP, origin, TTL, cache-control, signed URL, cache poisoning, WAF, Vary header.

---

## 3.5 Security Reference Model

**Definition:** A layered architectural model describing cloud security domains, controls and who (provider vs consumer) is responsible for each (e.g., CSA Cloud Reference Model / NIST Cloud Reference Architecture, **Shared Responsibility Model**).

**Detail**
- Model stacks IaaS (base) → PaaS → SaaS; each higher layer inherits security of lower ones.
- Security domains: physical/facility, network, hardware, hypervisor/OS, application, data, identity & access management (IAM), governance/compliance.
- **Shared responsibility:** provider secures *"security of the cloud"*; customer secures *"security in the cloud"*.

| Layer | IaaS | PaaS | SaaS |
|---|---|---|---|
| Data & identities | Customer | Customer | Customer |
| Application | Customer | Customer | Provider |
| OS/middleware | Customer | Provider | Provider |
| Hypervisor, hardware, facility | Provider | Provider | Provider |

**Keywords:** CSA, NIST, shared responsibility, defence-in-depth, IAM, trust boundary.

---

## 3.6 Security Issues in Cloud

**Definition:** Threats and vulnerabilities that arise from shared, remote and multi-tenant cloud environments.

**Major threats (Mid-2 Q2 – public cloud migration of sensitive records):**
| Threat | Description |
|---|---|
| Data breach/leakage | Unauthorised access to sensitive records |
| Misconfiguration | Public buckets, open ports (top cause) |
| Insecure APIs/interfaces | Weak authentication in management APIs |
| Account/credential hijacking | Phishing, stolen keys |
| Insider threat | Malicious/negligent staff (customer or provider) |
| Multi-tenancy attacks | VM escape, side-channel, cross-tenant leakage |
| DoS/DDoS | Service unavailability |
| Data loss, weak deletion | Accidental deletion, no backups |
| Compliance violation, lock-in | Regulation breaches, provider dependence |

**Mitigation:**
- **IAM:** authentication (MFA, SSO), authorisation (RBAC/ABAC), federation, role separation, key rotation.
- **Least privilege:** grant only minimal permissions needed, time-bound access, review regularly → limits blast radius of stolen credential or insider.
- **Audit logging:** record who did what/when (CloudTrail, Azure Monitor); tamper-proof logs, SIEM alerts, forensic trail, compliance evidence.
- Plus: encryption (at rest/in transit, KMS), network segmentation/VPC, private endpoints, patching, DLP, backups, WAF.

**Keywords:** IAM, MFA, RBAC, least privilege, audit trail, CloudTrail, SIEM, KMS, encryption, misconfiguration, VM escape.

---

## 3.7 Privacy and Compliance Issues

**Definition:** **Privacy** = protecting personal data and its lawful use; **compliance** = adhering to laws, regulations and standards governing data and services.

**Detail**
- Issues: data location/residency & sovereignty, cross-border transfer, lack of visibility, third-party/sub-processor access, e-discovery & legal holds, breach notification, right to erasure, auditability.
- Regulations/standards: **GDPR** (EU), **HIPAA** (US health), **PCI-DSS** (card data), **SOC 2 / ISO 27001** (security attestations), **India's Digital Personal Data Protection (DPDP) Act, 2023**.
- Measures: data classification, encryption, contracts/SLAs & DPAs, choosing compliant regions, regular audits, privacy by design, anonymisation/pseudonymisation.

**Keywords:** GDPR, HIPAA, PCI-DSS, DPDP, data residency, sovereignty, DPA, audit, anonymisation, breach notification.
