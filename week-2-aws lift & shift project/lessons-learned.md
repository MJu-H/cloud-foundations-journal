# Lessons Learned

**Project:** AWS Lift & Shift — 3-Tier Java Web App  
**Week:** 2

## 1. Security Group Source References

When an ALB sends traffic to an EC2 instance, the traffic originates
from the ALB’s security group. The target instance’s security group must
explicitly allow inbound traffic from the ALB’s security group ID, not
just have the port open to `0.0.0.0/0`.

**Practice:** Reference security groups as sources instead of IP
addresses for intra-VPC communication. This is more secure and remains
valid when instance IPs change.

## 2. Health Checks as Diagnostic Tools

ALB health checks are the primary mechanism for determining target
health. If the security group blocks the health check path, the target
is marked unhealthy regardless of whether the application is running
correctly.

**Practice:** When troubleshooting ALB issues, verify in order: 1.
Security group rules (both ALB and target) 2. Target group health check
configuration 3. Application logs on the target instance

## 3. AMIs for Immutable Infrastructure

Creating an AMI from a fully configured instance eliminates manual
configuration for new instances. The AMI captures the entire
environment: OS, runtime, deployed artifacts, and settings.

**Practice:** Create an AMI after verifying instance configuration. Use
the AMI as the base image for launch templates in Auto Scaling Groups.

## 4. Private DNS for Service Discovery

Route 53 Private Hosted Zones enable backend services to be referenced
by DNS name rather than IP address. This ensures: - Endpoint stability
when instance IPs change - No configuration changes required for new
Auto Scaling instances - Decoupled service communication

**Practice:** Align the hosted zone name with application configuration
files to avoid code changes during migration.

## 5. Tiered Security Groups

Separating security groups by tier (ELB, Application, Backend) enforces
defense in depth:

- **ELB-sg:** Exposed to internet (ports 80, 443)
- **App-sg:** Accepts traffic only from ELB (port 8080) and admin IP
  (SSH)
- **Backend-sg:** Accepts traffic only from application tier (ports
  3306, 5672, 11211) and for service checks SSH from admin IP.

**Practice:** Layer security groups to limit blast radius. A compromise
at one tier does not automatically grant access to downstream tiers.

## 6. IAM Roles Over Hardcoded Credentials

Attaching an IAM Role to an EC2 instance is the correct method for
granting AWS service access. Roles provide temporary credentials that
AWS rotates automatically. No secrets are stored on the instance.

**Practice:** Use IAM Roles for EC2 instance permissions. Avoid
embedding access keys in scripts or storing them on instances.

## 7. Version Managers for Development Toolchains

SDKMAN eliminates manual Java/Maven/Gradle version management. In cloud
engineering, switching between tool versions across projects is a common
requirement.

**Practice:** Integrate version managers (SDKMAN, nvm, pyenv) into the
development workflow early.

## 8. Documentation as Knowledge Base

Maintaining a troubleshooting log with symptoms, root causes, and
resolutions builds a reference for future projects and interview
preparation.

**Practice:** Document each issue encountered, the debugging process,
and the final fix.

## 9. Lift & Shift as a Foundation

Lift and Shift migration involves: - Replicating on-premise architecture
in the cloud - Adding cloud-native capabilities (load balancing,
auto-scaling, service discovery) - Maintaining security boundaries -
Ensuring operational parity

The next evolution is re-architecting: moving to serverless, containers,
or managed services.
