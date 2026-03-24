# AWS Well-Architected Framework & Cloud Adoption Framework Assessment
**Lab: Design and Evaluate an AWS Solution Using WAF and CAF**

---

## Task 1 – Review the Existing Architecture

### 1. Components of the Workload (Current AWS Deployment After Lift-and-Shift)

| AWS Resource | Details |
|---|---|
| VPC | Single VPC with public and private subnets across one Availability Zone |
| EC2 Instances | 1–2 t3.medium or m5.large instances running the web/application (frontend) tier in a public subnet |
| Amazon RDS | Single-AZ MySQL or PostgreSQL instance in a private subnet |
| Security Groups | Web SG: ports 80, 443, and 22 open to 0.0.0.0/0 (entire internet) |
| Internet Gateway | Attached to the VPC; EC2 instances are directly reachable from the internet |
| Elastic IP / Public DNS | Directly pointing to the EC2 instance — no load balancer in front |
| EBS Volumes | Root and data volumes attached to EC2; no snapshot schedule configured |
| RDS Automated Backups | Enabled with the default 7-day retention period; no cross-region backup |
| IAM | Root account used for administration; no role-based access configured |
| Monitoring | Basic CloudWatch metrics only; no dashboards, alarms, or log aggregation |

---

### 2. Potential Risks and Weaknesses

1. **Single-AZ deployment:** Both the EC2 instances and the RDS database reside in a single Availability Zone. If that AZ experiences an outage, the entire application goes offline with no automatic failover.

2. **No load balancer:** Traffic is routed directly to one or two EC2 instances. There is no health checking, no failover between instances, and no SSL termination at the edge.

3. **No Auto Scaling Group:** The application cannot scale out to handle traffic spikes and cannot scale in during low-traffic periods to reduce cost. Capacity is entirely manual.

4. **Overly permissive security groups:** Port 22 (SSH), 80 (HTTP), and 443 (HTTPS) are open to the entire internet (0.0.0.0/0). This dramatically increases the attack surface and the risk of brute-force or credential-stuffing attacks on SSH.

5. **SSH key-based admin access:** Administrators connect directly via SSH using key pairs. There is no centralised audit trail, no session recording, and no compliance-friendly access method. AWS best practice recommends AWS Systems Manager Session Manager instead.

6. **No Multi-AZ RDS:** A single-AZ RDS instance means a database failure or AZ outage causes complete application downtime and potential data loss with no automatic failover.

7. **Weak backup and recovery strategy:** RDS has 7-day automated backups but no manual snapshots, no cross-region replication, and no tested restore procedure. EC2 EBS volumes have no snapshot policy at all. RPO and RTO targets are undefined.

8. **No Web Application Firewall (WAF):** The application has no protection against OWASP Top 10 threats such as SQL injection, cross-site scripting (XSS), or HTTP flood attacks.

9. **No CDN:** Static assets (images, CSS, JavaScript) are served directly from EC2. Users located far from the AWS region experience high latency, and EC2 instances bear unnecessary load.

10. **Insufficient monitoring and alerting:** The setup relies only on default CloudWatch metrics. There are no custom dashboards, no metric alarms, no log aggregation, and no automated incident notification. Issues are typically discovered by end-user complaints rather than proactive detection.

11. **No encryption in transit or at rest:** The RDS instance and EBS volumes are likely unencrypted. Sensitive data is therefore exposed if storage media or snapshots are compromised.

12. **Fixed, always-on compute:** Instances run 24/7 regardless of traffic patterns, leading to unnecessary cost during off-peak hours (nights, weekends).

---

## Task 2 – AWS Well-Architected Framework Evaluation

| Pillar | Strength (Observation) | Area for Improvement | Recommendation | Supporting AWS Services |
|---|---|---|---|---|
| **Operational Excellence** | Moving to AWS enables infrastructure automation and repeatable deployments via code | No CI/CD pipeline, no IaC, and no centralised observability; deployments are manual and error-prone | Adopt Infrastructure as Code (IaC) for all resources; implement centralised logging and structured runbooks | AWS CloudFormation, AWS CodePipeline, AWS CodeDeploy, Amazon CloudWatch (dashboards + alarms), AWS CloudTrail |
| **Security** | Workloads are now isolated from the on-premises network within a VPC; some network boundary exists | Security groups are overly permissive; SSH is exposed to the internet; data is unencrypted; no threat detection | Apply least-privilege IAM roles; restrict security groups; encrypt data at rest and in transit; enable threat detection | AWS IAM, AWS KMS, AWS Secrets Manager, AWS WAF, Amazon GuardDuty, AWS Security Hub, AWS Shield Standard |
| **Reliability** | AWS cloud infrastructure supports high availability features that can be enabled at any time | Single-AZ EC2 and RDS mean any AZ-level or instance-level failure causes total downtime | Deploy across two or more AZs; use ALB for health-checked load distribution; enable RDS Multi-AZ for automatic database failover | Application Load Balancer (ALB), Auto Scaling Group (ASG), Amazon RDS Multi-AZ, AWS Backup |
| **Performance Efficiency** | Managed AWS services (RDS, EC2) reduce the overhead of hardware provisioning | There is no dynamic scaling; all traffic hits EC2 directly with no caching or CDN offloading | Enable Auto Scaling; serve static content via CloudFront CDN; use ElastiCache to reduce database read load | Amazon CloudFront, Auto Scaling Group, Amazon ElastiCache (Redis/Memcached), Amazon RDS read replicas |
| **Cost Optimization** | The pay-as-you-go model eliminates large upfront capital expenditure compared to on-premises | On-demand EC2 and RDS instances run 24/7 and may be over-provisioned for actual usage; no cost visibility tooling | Right-size instances using Cost Explorer recommendations; purchase Compute Savings Plans for predictable workloads; use S3 Lifecycle policies for backup storage | AWS Cost Explorer, AWS Trusted Advisor, Compute Savings Plans, Amazon S3 Intelligent-Tiering, AWS Budgets |

---

## Task 3 – AWS Cloud Adoption Framework (CAF) Readiness Assessment

### Business Perspective

The organisation's primary business motivation for migrating to AWS is to achieve higher availability, reduce infrastructure maintenance costs, and accelerate the delivery of new product features. However, cloud value realization has not yet been formally defined. There is no documented Total Cost of Ownership (TCO) analysis, no Return on Investment (ROI) model, and no set of cloud-specific Key Performance Indicators (KPIs) to measure whether the migration is delivering expected outcomes.

To improve readiness from a business perspective, leadership must establish clear, measurable business outcomes tied to the cloud migration. These should include targets such as improved uptime SLAs (e.g., 99.9% availability), reduction in infrastructure operational costs by a defined percentage, faster time-to-market for new features, and improved customer satisfaction scores. A formal cloud financial model — including a cost-benefit analysis comparing on-premises versus AWS operational expenditure — should be developed and approved before further cloud investment is made. Executive sponsorship must be formalised, and cloud goals must be explicitly linked to the organisation's broader strategic objectives such as market competitiveness, revenue growth, and customer experience improvement. Without these business guardrails, the technical migration risks being undertaken without sufficient organisational alignment or accountability.

---

### People Perspective

The organisation has an existing IT team experienced with on-premises infrastructure — physical servers, traditional networking, and manual deployment processes. However, cloud-native skills are largely absent. Team members are unfamiliar with managed services, automation, Infrastructure as Code, and DevOps practices, which represents a significant risk to the success of the migration.

To address this gap, the organisation must invest in a structured upskilling programme. This should include AWS Academy courses, AWS certification pathways (beginning with Cloud Practitioner, then Solutions Architect Associate), hands-on labs, and sandbox environments for experimentation. Beyond individual training, roles and responsibilities must be redefined to support cloud operations. The separation between development, operations, and security teams needs to evolve toward a shared-responsibility DevOps model where cross-functional collaboration is the norm. A Cloud Centre of Excellence (CCoE) should be established to own cloud standards, share best practices, and mentor teams during the transition. Change management is equally critical — staff must understand not just how the new systems work, but why the organisation is adopting them and how their own roles will evolve. With the right training investment, role clarity, and cultural enablement, the team is capable of successfully operating AWS at scale.

---

### Governance Perspective

Currently, the organisation lacks formal cloud governance policies. Without guardrails, there is a significant risk of cost overruns from unmanaged resource sprawl, security misconfigurations due to inconsistent practices, and regulatory non-compliance if data handling requirements (such as GDPR or PCI-DSS) are not addressed from the outset.

To build governance readiness, the organisation should define and enforce a set of cloud governance controls before expanding the AWS environment. These should include: a mandatory tagging strategy for all resources (identifying environment, cost centre, and owner); AWS Budgets configured with threshold alerts to prevent surprise bills; Service Control Policies (SCPs) via AWS Organizations to enforce guardrails across accounts; and AWS Config rules to detect and flag non-compliant resource configurations automatically. AWS Control Tower can be used to establish a well-architected multi-account landing zone with governance built in. Compliance requirements — whether GDPR, ISO 27001, or PCI-DSS — should be mapped to specific AWS controls during the migration design phase, not retrofitted afterward. AWS Well-Architected Framework reviews should be scheduled on a regular cadence (quarterly or after major changes) to ensure workloads remain aligned with best practices. With these governance controls in place, the organisation can scale cloud adoption confidently without losing financial, legal, or security control.

---

### Platform Perspective

The current AWS platform is a basic lift-and-shift: the technical infrastructure exists in AWS but it is not yet architected for elasticity, high availability, or automation. It closely mirrors the old on-premises setup, just running on virtual machines in the cloud rather than physical servers in a data centre.

To advance platform readiness, the organisation must adopt cloud-native architectural patterns. This means deploying across multiple Availability Zones using an Application Load Balancer, Auto Scaling Group, and RDS Multi-AZ — making the platform resilient by design. All infrastructure should be defined and version-controlled using Infrastructure as Code, specifically AWS CloudFormation or AWS CDK, so that environments can be reproduced reliably and changes are auditable. Managed services should be preferred over self-managed alternatives wherever practical — for example, Amazon RDS instead of a self-managed MySQL on EC2, and AWS Backup for centralised backup orchestration. A CI/CD deployment pipeline using AWS CodePipeline, CodeBuild, and CodeDeploy will enable faster, safer, and more frequent application releases with automated testing gates. Network architecture should be hardened with properly segmented public and private subnets, NAT Gateways for outbound internet access from private resources, and least-privilege security group rules. Once these patterns are in place, the platform will be resilient, scalable, secure, and significantly easier to operate.

---

### Security Perspective

The organisation's current security posture is low. The existing approach is perimeter-based — inherited from on-premises thinking — where a firewall at the network edge is treated as the primary defence. In a cloud environment, this model is insufficient. There are no IAM role-based access controls, no encryption policies, no secrets management, and no threat detection or logging.

To reach an acceptable cloud security baseline, the organisation must adopt the AWS Shared Responsibility Model and implement the following controls. First, replace all SSH key-based access with AWS Systems Manager Session Manager, which provides audited, keyless access with no need to open port 22. Second, implement least-privilege IAM roles for all services and users, eliminating any use of the AWS root account for day-to-day operations. Third, encrypt all data at rest using AWS KMS-managed keys applied to RDS, EBS volumes, and S3 buckets; enforce HTTPS for all data in transit. Fourth, move all secrets (database credentials, API keys) to AWS Secrets Manager or AWS Systems Manager Parameter Store — never store credentials in code or environment variables. Fifth, deploy AWS WAF in front of the Application Load Balancer to filter OWASP Top 10 threats including SQL injection and XSS. Finally, enable Amazon GuardDuty for continuous threat detection, AWS CloudTrail for a full audit log of all API activity, and AWS Security Hub to aggregate and prioritise security findings. Compliance requirements should be integrated into the architecture design from day one — security is far cheaper to build in than to retrofit.

---

### Operations Perspective

Operational readiness is currently below the required standard. The team manages the AWS environment reactively, relying on manual processes: servers are manually patched, infrastructure is manually deployed, and issues are typically discovered by end users before the operations team is aware. There are no runbooks, no incident response playbooks, and no defined recovery objectives.

To achieve operational excellence in the cloud, the organisation must shift from reactive to proactive operations. CloudWatch should be configured with metric alarms, custom dashboards, and log groups for both EC2 application logs and RDS logs, enabling teams to detect anomalies before they impact customers. AWS Systems Manager Patch Manager should handle automated OS patching on a defined schedule, replacing manual patching entirely. Incident response runbooks should be documented for the most common failure scenarios (database failover, EC2 instance failure, high CPU/memory alerts), and post-incident reviews should be conducted after every significant event. Disaster recovery objectives — Recovery Point Objective (RPO) and Recovery Time Objective (RTO) — must be formally defined, documented, and validated through regular backup-and-restore drills using AWS Backup. AWS Systems Manager Automation can be used to codify common operational tasks as runbooks that can be triggered automatically or on demand. As automation matures, the team should measure mean time to detection (MTTD) and mean time to recovery (MTTR) as ongoing operational KPIs. With these changes, the environment will become significantly more reliable, with reduced downtime and faster, more confident incident response.

---

## Task 4 – Improved Architecture Design

### Architecture Overview

The improved architecture is a two-tier (web + database) AWS deployment, redesigned to be highly available, secure, scalable, and cost-efficient. It replaces the single-AZ, directly-exposed EC2 setup with a multi-layered architecture spanning two Availability Zones.

**Traffic flow:**
1. End users access the application via **Amazon Route 53** (DNS with health-check-based routing).
2. Static assets (images, CSS, JS) are served globally via **Amazon CloudFront** from an **Amazon S3** bucket — reducing EC2 load and improving latency worldwide.
3. Dynamic requests flow through **AWS WAF** (attached to the ALB) and then to an **Application Load Balancer (ALB)** in the public subnets.
4. The ALB routes traffic to **EC2 instances** in an **Auto Scaling Group** deployed across private subnets in **two Availability Zones**.
5. EC2 instances communicate with an **Amazon RDS Multi-AZ** instance (primary in AZ-1, standby in AZ-2) via a dedicated database security group on port 3306 only.
6. All outbound internet traffic from private subnet resources (e.g., OS updates) exits via **NAT Gateways** in the public subnets.

**Security controls:**
- ALB Security Group: accepts inbound 80/443 from the internet only.
- EC2 Security Group: accepts inbound 80/443 from the ALB security group only. Port 22 is closed entirely.
- RDS Security Group: accepts inbound 3306 from the EC2 security group only.
- Admin access via **AWS Systems Manager Session Manager** — no SSH, no open port 22.
- All data at rest encrypted with **AWS KMS**; all data in transit via HTTPS/TLS.
- Secrets stored in **AWS Secrets Manager**.
- **Amazon GuardDuty** enabled for threat detection; **AWS CloudTrail** for full API audit logging.
- **AWS Config** rules enforce tagging, encryption, and security group compliance continuously.

**Monitoring and operations:**
- **Amazon CloudWatch** with dashboards, metric alarms, and log groups for EC2 and RDS.
- **AWS Backup** with a defined schedule for RDS snapshots (daily, 30-day retention) and EBS snapshots.
- **AWS Systems Manager Patch Manager** for automated EC2 patching.

---

### How the Improved Design Addresses Each WAF Pillar

| Pillar | How the New Design Addresses It |
|---|---|
| **Operational Excellence** | All infrastructure defined in CloudFormation (IaC). CI/CD pipeline via CodePipeline/CodeDeploy. CloudWatch dashboards and alarms for proactive monitoring. No manual server access — SSM Session Manager used instead of SSH. |
| **Security** | ALB, EC2, and RDS each have dedicated, least-privilege security groups with no cross-exposure. Port 22 closed. WAF protects against OWASP Top 10. KMS encryption for RDS and EBS. Secrets Manager for credentials. GuardDuty and CloudTrail provide threat detection and audit logging. |
| **Reliability** | Multi-AZ deployment for both EC2 (via ASG across AZ-1 and AZ-2) and RDS (Multi-AZ with automatic failover). ALB performs health checks and routes away from unhealthy instances. AWS Backup provides tested, scheduled recovery capability. |
| **Performance Efficiency** | CloudFront CDN serves static assets globally, reducing EC2 load and latency. Auto Scaling Group dynamically adjusts EC2 capacity based on demand. ElastiCache (Redis) can be added to reduce repetitive database reads. |
| **Cost Optimization** | Auto Scaling Group scales down to minimum instances during off-peak hours. Static assets offloaded to S3 + CloudFront reduces EC2 instance requirements. Compute Savings Plans applied to baseline EC2 usage. AWS Budgets and Cost Explorer provide ongoing cost visibility and anomaly detection. |

---

### CAF Alignment of the Improved Architecture

- **Business:** The architecture is designed for 99.9%+ availability, directly supporting business SLA commitments and customer satisfaction goals.
- **People:** Use of managed services (ALB, RDS Multi-AZ, CloudFront) reduces operational burden, allowing the team to focus on higher-value work while building cloud skills.
- **Governance:** All resources are tagged; AWS Config enforces compliance rules; CloudTrail provides full audit history for governance and compliance reporting.
- **Platform:** CloudFormation templates make the architecture fully reproducible. The landing zone pattern (public/private subnets, NAT Gateway, IGW, security group hierarchy) becomes a reusable standard.
- **Security:** Security is embedded by design — encryption, least-privilege access, WAF, GuardDuty, and audit logging are all built into the architecture from day one.
- **Operations:** CloudWatch alarms, automated patching, and AWS Backup replace all manual operational tasks with automated, auditable processes.

---

## Brief Reflection

Working on this assessment helped me move from theoretical knowledge of the AWS Well-Architected Framework to practical, applied thinking. What became clear through the process is that a lift-and-shift migration is only the beginning — moving servers to EC2 and a database to RDS does not automatically make a workload well-architected. Real cloud readiness requires intentional decisions about high availability, security by design, automation, and cost governance from the very first architecture review.

The Cloud Adoption Framework was equally illuminating. It made me realise that cloud migration fails far more often due to people, governance, and process gaps than due to technical ones. Upskilling teams, defining governance guardrails, and aligning cloud goals to measurable business outcomes are not optional — they are prerequisites for sustainable cloud success.

Designing the improved architecture forced me to think like a cloud architect: balancing trade-offs between cost and reliability, between operational simplicity and feature richness. Every service added to the architecture has a reason — nothing is included arbitrarily. This exercise has strengthened my ability to reason about cloud systems holistically, which is exactly the skill that AWS certifications and real-world cloud roles demand.
