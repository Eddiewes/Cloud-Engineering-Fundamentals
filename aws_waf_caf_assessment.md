
Review of Existing Architecture
Workload components:
Web frontend (single web server)
Backend database server
Local storage for data and backups
Flat network, perimeter firewall
Key risks & weaknesses:
Single point of failure (single server / single AZ equivalent)
No automated backups or disaster recovery
Manual deployments and monitoring
Broad network access (no security group segmentation)
Limited scalability and capacity planning

AWS Well‑Architected Framework Assessment
Pillar
Observation
Improvement Recommendation
Supporting AWS Service
Operational Excellence
Basic monitoring exists via system logs
Introduce infrastructure as code and centralized monitoring
AWS CloudWatch, AWS CloudFormation
Security
Perimeter‑only security model
Apply least privilege, network segmentation, and encryption
IAM, Security Groups, AWS KMS
Reliability
Single‑instance deployment
Deploy across multiple AZs with automated recovery
Elastic Load Balancer, Auto Scaling
Performance Efficiency
Fixed compute capacity
Use right‑sized, scalable compute and managed database
EC2 Auto Scaling, Amazon RDS
Cost Optimization
Over‑provisioned servers
Use pay‑as‑you‑go services and cost visibility tools
AWS Cost Explorer, Compute Savings Plans


AWS Cloud Adoption Framework (CAF) Readiness Summary
1. Business Perspective
The need for higher availability, scalability, and reduced infrastructure maintenance costs primarily drives the organization’s business motivation for cloud migration. However, cloud value realization is not yet fully defined. To improve readiness, leadership should define clear business outcomes such as improved uptime SLAs, faster feature delivery, and reduced operational overhead. Financial models like cost‑benefit analysis and TCO comparisons should be established to justify cloud investments and track ROI throughout the migration.
2. People Perspective
Current teams are experienced with on‑premises infrastructure but have limited AWS expertise. This skills gap poses a risk to successful adoption. Key enablers include structured cloud training, AWS certification paths, and hands‑on labs. Roles and responsibilities should also be updated to support DevOps practices, encouraging collaboration between development, operations, and security teams.
3. Governance Perspective
The organization lacks formal cloud governance policies. Without guardrails, there is a risk of cost overruns and security misconfigurations. Governance readiness can be improved by defining account structures, cost controls, tagging strategies, and compliance requirements. AWS Organizations and Service Control Policies can help enforce standards while still allowing teams autonomy.
4. Platform Perspective
The current platform is not designed for elasticity or automation. To improve readiness, the organization should adopt a standardized landing zone with networking, identity, and shared services preconfigured. Using managed services such as Amazon RDS and ELB will reduce operational burden and improve consistency across environments.
5. Security Perspective
Security is currently reactive rather than proactive. Cloud readiness requires embedding security into architecture design and deployment pipelines. Key actions include enabling encryption by default, using IAM roles instead of static credentials, and continuous monitoring for threats. Services like AWS Shield, GuardDuty, and Security Hub can strengthen the security posture.
6. Operations Perspective
Operational processes are manual and reactive. To improve readiness, the organization should adopt automation, observability, and incident response playbooks. Implementing monitoring, logging, and automated scaling will improve system resilience and reduce mean time to recovery.

Improved AWS Architecture Design 
Users access the application through Amazon Route 53 and an Application Load Balancer.
The frontend runs on EC2 instances in an Auto Scaling Group across multiple Availability Zones.
The backend database is hosted on Amazon RDS (Multi‑AZ) with automated backups.
Security Groups and NACLs enforce network segmentation.
IAM roles manage access securely.
CloudWatch provides monitoring, logging, and alarms.
AWS Backup and Snapshots ensure data protection.
This design improves availability, security, scalability, and cost efficiency while aligning with all five WAF pillars.

Reflection
This lab reinforced the importance of structured frameworks when designing cloud architectures. Applying the AWS Well‑Architected Framework helped identify weaknesses that might otherwise be overlooked, such as single points of failure and lack of cost visibility. The Cloud Adoption Framework emphasized that cloud migration is not just a technical effort but also an organizational transformation involving people, processes, and governance. I learned how managed AWS services can significantly reduce operational complexity while improving reliability and security. Overall, the exercise improved my ability to think like a cloud architect—balancing technical design with business goals and operational readiness.

