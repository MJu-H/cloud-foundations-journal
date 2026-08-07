# Troubleshooting Log

**Project:** AWS Lift & Shift — 3-Tier Java Web App  
**Environment:** AWS EC2, ALB, Route 53, Auto Scaling

## Issue 1: Java & Maven Version Mismatch

### Symptom

`mvn clean install` failed during the build process.

### Root Cause

- Local environment: Maven 3.9.16 + Java 21
- Project requirement: Maven 3.9.9 + OpenJDK 17

### Resolution

Installed SDKMAN for version management:

    curl -s "https://get.sdkman.io" | bash
    source "$HOME/.sdkman/bin/sdkman.sh"

    sdk install java 17.0.9-amzn
    sdk install maven 3.9.9

    sdk use java 17.0.9-amzn
    sdk use maven 3.9.9

SDKMAN allows switching between Java and Maven versions across different
projects without manual reconfiguration.

## Issue 2: ALB Targets Unhealthy

### Symptom

- ALB targets showed **Unhealthy**
- ALB DNS link was not serving traffic

### Root Cause

Traffic flow:

    User -> ALB DNS -> ELB-sg (allows 80/443) -> ALB -> project01-app-sg (port 8080)

The ALB was attempting health checks and forwarding traffic to the
Tomcat instance on port 8080, but `project01-app-sg` did not have an
inbound rule allowing traffic from `project01-ELB-sg`.

In AWS, traffic from an ALB to an instance originates from the ALB’s
security group identity. The target instance’s security group must
explicitly allow inbound traffic from the ALB’s security group.

### Resolution

Added inbound rule to `project01-app-sg`:

| Setting    | Value                                  |
|------------|----------------------------------------|
| Protocol   | TCP                                    |
| Port Range | 8080                                   |
| Source     | `project01-ELB-sg` (security group ID) |

    aws ec2 authorize-security-group-ingress   --group-id sg-app-sg-xxxxx   --protocol tcp   --port 8080   --source-group sg-elb-sg-xxxxx

Targets transitioned to **Healthy** immediately after the rule was
applied.

## Summary

| Issue                       | Symptom                            | Root Cause                         | Resolution                                |
|-----------------------------|------------------------------------|------------------------------------|-------------------------------------------|
| Java/Maven version mismatch | Build failure                      | Wrong versions installed           | SDKMAN version switching                  |
| ALB unhealthy targets       | DNS not working; targets unhealthy | App SG blocked ALB traffic on 8080 | Added inbound rule: port 8080 from ELB-sg |
