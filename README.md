# Cloud-Engineering-Fundamentals-
# AWS Well-Architected & Cloud Adoption Framework Assessment

## Overview
This repository contains the design and evaluation of a two-tier web application migrated from an on-premises environment to AWS. The assessment applies the **AWS Well-Architected Framework (WAF)** and the **AWS Cloud Adoption Framework (CAF)** to evaluate the workload, identify risks, and propose architectural improvements aligned with AWS best practices.

The goal of this lab is to demonstrate the ability to think like a cloud architect by critically analyzing an existing system, applying industry-recognized frameworks, and clearly communicating architectural decisions.

---

## Scenario
An organization is migrating a two-tier web application (frontend and backend database) from on-premises infrastructure to AWS. Management requires the new cloud architecture to be secure, reliable, scalable, cost-effective, and operationally excellent from day one.

---

## Repository Structure
├── aws_waf_caf_assessment.md # Main report covering Tasks 1–4 and reflection
└── README.md # Project overview and explanation

---

## Contents
### 1. Well-Architected Framework Assessment
The workload is evaluated using the five AWS Well-Architected pillars:
- Operational Excellence  
- Security  
- Reliability  
- Performance Efficiency  
- Cost Optimization  

Each pillar includes:
- Observations
- Identified gaps
- Improvement recommendations
- Supporting AWS services

---

### 2. Cloud Adoption Framework (CAF) Readiness Analysis
The organization’s cloud readiness is analyzed across the six CAF perspectives:
- Business
- People
- Governance
- Platform
- Security
- Operations

Each perspective includes a short readiness assessment and recommended actions to enable a successful cloud migration.

---

### 3. Improved AWS Architecture Design
An enhanced AWS architecture is proposed to address the identified risks and gaps. The design:
- Uses multi-AZ deployments for high availability
- Applies least-privilege security principles
- Leverages managed AWS services
- Supports scalability, monitoring, and cost optimization

---

### 4. Reflection
A short reflection summarizes key learnings from applying AWS architectural frameworks and highlights the importance of aligning technical decisions with business and organizational goals.

---

## Tools & Services Referenced
- Amazon EC2 & Auto Scaling
- Elastic Load Balancer (ALB)
- Amazon RDS (Multi-AZ)
- Amazon VPC, Security Groups, NACLs
- AWS IAM
- Amazon CloudWatch
- AWS Cost Explorer
- AWS Backup

---

## Outcome
By completing this lab, this project demonstrates the ability to:
- Apply AWS Well-Architected principles to real-world workloads
- Assess organizational readiness using the Cloud Adoption Framework
- Design secure, scalable, and reliable cloud architectures
- Communicate architectural decisions clearly and professionally

---

## Author
**Spencer Williams**
