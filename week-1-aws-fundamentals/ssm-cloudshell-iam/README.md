# SSM Session Manager + IAM Role + CloudShell – Week 1 

## What I built (my real workflow)
1. Launched an EC2 instance (t3.micro) in us-east-1c.
2. Created an IAM role with the AmazonSSMManagedInstanceCore policy.
3. Attached the IAM role to the EC2 instance.
4. Verified that the SSM Agent was already installed on the Amazon Linux 2 AMI.
5. Connected to the instance using SSM Session Manager (no SSH keys required).
6. Used CloudShell to run AWS CLI commands without installing anything locally.
7. Verified that SSM access works even when port 22 is closed.
8. Tested commands inside the SSM session to confirm full shell access.

## Why this matters (DevOps + Architect perspective)
- SSM Session Manager is more secure than SSH:
  - No key pairs to manage
  - No open port 22
  - No inbound rules required
  - Full audit logging
  - IAM-based access control
- Enterprises prefer SSM for:
  - Production EC2 access
  - Compliance and auditing
  - Zero-trust environments
  - Automated maintenance
- CloudShell allows CLI access without installing AWS CLI locally.

This is real enterprise-level DevOps practice.

## What confused me initially
- Why I could connect without SSH keys.
- How IAM roles replace key pairs.
- Why port 22 can stay closed.
- How SSM Agent communicates with AWS.

## How I fixed my confusion
- Learned that SSM Agent uses outbound HTTPS (port 443), not SSH.
- Understood that IAM roles give the instance permission to register with SSM.
- Realized that SSM Session Manager is a secure, keyless alternative to SSH.
- Saw that CloudShell uses my IAM identity to run AWS CLI commands.

## Key takeaways
- IAM role = permissions for the instance.
- SSM Agent = software that enables remote access.
- Session Manager = secure shell without SSH.
- CloudShell = browser-based AWS CLI.
- No need to expose port 22 in production environments.

