# Snapshot Recovery + AMI + Launch Template – Week 1

## What I built (my real workflow)
1. Launched a new EC2 instance (t3.micro) in us-east-1c.
2. Attached a new EBS volume to the instance.
3. Mounted the volume inside the EC2 instance.
4. Created a snapshot of the attached EBS volume.
5. Broke the volume on purpose by deleting files inside the mounted directory.
6. Restored the volume by creating a new volume from the snapshot and attaching it back.
7. Verified that the deleted files were recovered successfully.
8. Launched a second EC2 instance.
9. Created an AMI from that second instance.
10. Created a Launch Template using the AMI.
11. Launched a new EC2 instance from the Launch Template to confirm it works.

## Why this matters (DevOps + Architect perspective)
- Snapshots allow fast backup and disaster recovery.
- Restoring a corrupted volume from a snapshot is a real-world break/fix scenario.
- AMIs allow you to clone servers and create identical environments.
- Launch Templates are required for:
  - Auto Scaling Groups
  - Spot fleets
  - Immutable infrastructure
  - Zero-downtime deployments

This workflow is core DevOps and AWS Solutions Architect knowledge.

## What confused me initially
- I wasn’t sure how snapshots restore corrupted data.
- I didn’t understand the difference between:
  - Snapshot → Volume
  - AMI → Instance
- I didn’t know why Launch Templates are needed if I can launch EC2 manually.

## How I fixed my confusion
- Learned that snapshots store the exact block-level state of a volume.
- Understood that restoring from a snapshot creates a *new* volume, not overwriting the old one.
- Realized that AMIs are for full server cloning, while snapshots are for storage recovery.
- Understood that Launch Templates automate EC2 creation and ensure consistency.

## Key takeaways
- Snapshot → Volume is for storage recovery.
- AMI → Instance is for server cloning.
- Launch Templates are required for Auto Scaling and production-grade deployments.
- This workflow is a real DevOps break/fix scenario and shows practical cloud skills.

