# EC2 Instance Types – Week 1 (16 June 2026)

## What I learned
- EC2 instance types define CPU, RAM, network performance, and storage behavior.
- I compared t3.micro and t3.small to understand differences in vCPU, RAM, and baseline performance.
- Learned how instance families are grouped: General Purpose (t, m), Compute Optimized (c), Memory Optimized (r), Storage Optimized (i, d).

## What I built
- Launched EC2 instances using t3.micro (free-tier eligible for my account).
- Observed CPU performance and network behavior in CloudWatch.
- Compared burstable performance between t3.micro and t3.small.

## Why I used t3.micro instead of t2.micro
- My AWS account does not show t2.micro as free-tier eligible.
- AWS has changed the free-tier offering for newer accounts.
- t3.micro is the default free-tier instance for new AWS accounts.
- t2.micro still exists in some regions, but is not free-tier eligible for my account.

## Why instance types matter (architect perspective)
- Choosing the wrong instance type increases cost or reduces performance.
- General Purpose = balanced workloads  
- Compute Optimized = CPU-heavy tasks  
- Memory Optimized = databases, caching  
- Storage Optimized = high IOPS workloads  
- Burstable instances (t-series) are ideal for dev/test and low-traffic apps.

## What confused me initially
- Why t3.micro performs better even though it looks similar to t2.micro in tutorials.

## How I fixed my confusion
- Learned that t3 instances use a newer CPU generation and support “unlimited mode” by default.
- Understood that AWS free-tier offerings differ between old and new accounts.

## Key takeaways
- Always match instance type to workload.
- Burstable instances are cheap but can throttle if CPU is sustained.
- Understanding instance families is essential for AWS Solutions Architect and DevOps roles.

