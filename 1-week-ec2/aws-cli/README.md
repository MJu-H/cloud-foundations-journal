# AWS CLI + CloudShell + SSM – Week 1 

## What I built (my real workflow)
1. Used AWS CloudShell to run AWS CLI commands without installing anything locally.
2. Used SSM Session Manager to access EC2 instances without SSH keys.
3. Verified AWS CLI version and tested basic commands.
4. Used AWS CLI to:
   - Describe EC2 instances
   - Describe volumes and snapshots
   - Create snapshots
   - Create AMIs
   - Create Launch Templates
   - Check Target Group health
   - Check CloudWatch alarms
5. Used AWS CLI inside CloudShell to avoid managing credentials on my laptop.
6. Used AWS CLI inside SSM sessions to run commands directly on the instance.
7. Combined CLI + SSM + CloudShell to manage my entire EC2 workflow securely.

## Why this matters (DevOps + Architect perspective)
- AWS CLI is required for:
  - Automation
  - Scripting
  - CI/CD pipelines
  - Infrastructure as Code
  - Debugging
  - CloudShell workflows
- CloudShell removes the need to install AWS CLI locally.
- SSM removes the need for SSH keys and open ports.
- CLI commands are the foundation for:
  - Terraform
  - Ansible
  - Jenkins pipelines
  - GitHub Actions
  - Bash automation

This is real DevOps engineering.

## What confused me initially
- Why CloudShell already had AWS CLI installed.
- Why SSM sessions can also run AWS CLI commands.
- How AWS CLI knows which region and credentials to use.

## How I fixed my confusion
- Learned that CloudShell uses my IAM identity automatically.
- Understood that SSM sessions run commands *inside* the instance.
- Realized that AWS CLI uses the instance’s IAM role when running inside SSM.
- Saw that CloudShell uses my IAM user/role permissions.

## Key takeaways
- CloudShell = AWS CLI in the browser.
- SSM = secure shell without SSH keys.
- AWS CLI = automation + scripting + DevOps workflows.
- IAM roles determine what CLI commands can do.

