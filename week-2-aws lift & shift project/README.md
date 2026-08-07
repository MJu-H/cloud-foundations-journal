## Key Outcomes

- Migrated a 3-tier Java app to AWS with zero downtime architecture
- Resolved ALB-to-EC2 security group misconfiguration blocking health checks
- Implemented horizontal auto-scaling (1-4 instances) using custom AMIs
- Designed tiered security model with least-privilege access between all layers

# Week 2: AWS Lift & Shift — 3-Tier Java Web App Migration

**Project Type:** Lift & Shift Migration  
**Cloud Provider:** AWS  
**Duration:** Week 2  
**Status:** Completed

## Overview

Migrated a traditional on-premise 3-tier Java web application to AWS
using a Lift and Shift strategy. The architecture consists of a
Presentation tier (ALB), Application tier (Tomcat), and Data tier
(MySQL, Memcached, RabbitMQ).

The objective was to replicate the on-premise environment in AWS with
internal service discovery, load balancing, and horizontal auto-scaling.

| Tier         | Technology                 | AWS Service |
|--------------|----------------------------|-------------|
| Presentation | Application Load Balancer  | ALB         |
| Application  | Tomcat + Java WAR          | EC2         |
| Data/Backend | MySQL, Memcached, RabbitMQ | EC2         |

## Architecture

    Internet
        |
        v
    ┌─────────────────────────────────────┐
    │  Application Load Balancer (ALB)    │
    │  SG: project01-ELB-sg               │
    │  Listener: HTTP:80 -> TG:8080       │
    └─────────────┬───────────────────────┘
                  |
                  v
    ┌─────────────────────────────────────┐
    │  Auto Scaling Group (Min:1, Max:4)  │
    │  Launch Template: Tomcat AMI        │
    │  SG: project01-app-sg               │
    └─────────────┬───────────────────────┘
                  |
                  v
    ┌─────────────────────────────────────┐
    │  Route 53 Private Hosted Zone       │
    │  Zone: vprofile.in                  │
    └──────┬────────┬─────────┬──────────┘
           |        |         |
           v        v         v
       ┌───────┐ ┌───────┐ ┌───────┐
       │ MySQL │ │RabbitMQ│ │Memcached│
       │(3306) │ │(5672)  │ │(11211) │
       └───────┘ └───────┘ └───────┘
       SG: project01-backend-sg

## Repository Structure

    week-2-aws-lift-and-shift/
    ├── README.md              # Project overview
    ├── architecture.md        # Security & networking design
    ├── project-notes.md       # Step-by-step implementation
    ├── troubleshooting.md     # Issues encountered and resolutions
    └── lessons-learned.md     # Technical takeaways

## What Was Built

1.  **VPC Networking & Security Groups** — Implemented a tiered security
    model with three distinct security groups enforcing least-privilege
    access between layers.

2.  **Route 53 Private Hosted Zone** — Configured internal DNS
    (`vprofile.in`) to match the application’s `application.properties`
    file, enabling service discovery without code changes.

3.  **Application Deployment** — Built the Java WAR locally using
    SDKMAN-managed Java 17 and Maven 3.9.9, uploaded the artifact to S3,
    and deployed it to Tomcat via AWS CLI.

4.  **Application Load Balancer** — Configured an ALB with a target
    group, health checks, and listeners. Resolved a security group
    misconfiguration that blocked health checks.

5.  **Auto Scaling Group** — Created an AMI from the configured Tomcat
    instance, built a launch template, and deployed an ASG (Min:1,
    Max:4) attached to the ALB target group.

## Tech Stack

- **AWS:** EC2, ALB, Auto Scaling, Route 53, S3, IAM
- **Application:** Java, Maven, Tomcat 10, MySQL, RabbitMQ, Memcached
- **Tools:** AWS CLI, SDKMAN, SSH
