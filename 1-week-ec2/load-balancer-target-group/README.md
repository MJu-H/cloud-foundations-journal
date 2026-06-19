# Application Load Balancer + Target Group – Week 1 

## What I built (my real workflow)
1. Used my existing Launch Template to instantly create two identical EC2 instances.
2. Installed a web server on each instance:
   - Apache2 on Ubuntu
   - httpd on Amazon Linux
3. Downloaded a real HTML template from tooplate.com and deployed it to /var/www/html.
4. Verified that both instances served the website correctly on port 80.
5. Created a Target Group and registered both instances.
6. Created an Application Load Balancer (ALB) and connected it to the Target Group.
7. Tested the ALB DNS name and initially received a **504 Gateway Timeout**.
8. Checked Target Group health and saw both instances marked **unhealthy**.
9. Diagnosed the issue: the EC2 security group did NOT allow inbound port 80 from the ALB security group.
10. Updated the EC2 instance security group to allow:
    - HTTP (80) **from the ALB security group only**
11. Health checks turned **healthy**, and the ALB started serving traffic correctly.
12. Verified load balancing by refreshing the ALB DNS name and seeing alternating responses.

## Why this matters (DevOps + Architect perspective)
- ALB → Target Group → Instances is the core architecture for production workloads.
- Health checks ensure traffic only goes to healthy instances.
- Security groups must allow **ALB → EC2** communication, not public → EC2.
- Using Launch Templates ensures consistent, repeatable deployments.
- Deploying a real HTML template simulates real web hosting.
- Fixing a 504 error shows real troubleshooting ability.

This is real production-grade DevOps work.

## What confused me initially
- Why the ALB showed **504 Gateway Timeout**.
- Why the Target Group marked instances as **unhealthy**.
- Why the EC2 instances worked directly but not through the ALB.

## How I fixed my confusion
- Learned that ALB health checks must reach port 80 on the instances.
- Realized that EC2 security groups must allow inbound traffic **from the ALB security group**, not from the internet.
- Updated the security group rules and instantly saw the health checks turn green.
- Understood that 504 errors usually mean:
  - health check failure
  - security group misconfiguration
  - backend timeout

## Key takeaways
- ALB never talks directly to the internet → it talks to EC2 through security groups.
- EC2 must allow inbound port 80 **from the ALB security group**, not 0.0.0.0/0.
- 504 Gateway Timeout = backend unreachable.
- Launch Templates make multi-instance deployments fast and consistent.
- Real HTML templates help validate load balancing visually.

