# EBS Volume Attachment, Formatting, Partitioning & Persistent Mount – Week 1 

## What I built (my real workflow)
1. Launched an EC2 instance (t3.micro) in us-east-1c.
2. Created a new EBS volume and attached it to the instance.
3. Verified the new block device using `fdisk -l`.
4. Created a new partition on the volume using `fdisk`.
5. Formatted the partition with the ext4 filesystem.
6. Mounted the partition to a directory inside the instance.
7. Wrote test files to confirm the volume was working.
8. Edited `/etc/fstab` to make the mount persistent across reboots.
9. Rebooted the instance to verify that the volume auto-mounted correctly.
10. Confirmed that the test files were still accessible after reboot.

## Why this matters (DevOps + Architect perspective)
- AWS gives you a raw block device with no filesystem.
- You must manually:
  - Partition the disk
  - Format it
  - Mount it
  - Persist it in `/etc/fstab`
- Persistent mounts are required for:
  - Production servers
  - Databases
  - EKS worker nodes
  - Auto-scaling environments
  - Any workload that depends on attached storage

This is real Linux system administration and a core DevOps skill.

## What confused me initially
- Why the new volume didn’t show up automatically.
- Why I had to create a partition before formatting.
- How `/etc/fstab` works and why it’s needed.

## How I fixed my confusion
- Learned that new EBS volumes are raw disks with no filesystem.
- Understood that `fdisk` creates the partition table.
- Understood that `mkfs` formats the partition.
- Realized that mounting is temporary unless added to `/etc/fstab`.
- Verified persistence by rebooting the instance.

## Key takeaways
- `fdisk -l` shows block devices.
- `fdisk` creates partitions.
- `mkfs` formats the partition.
- `mount` attaches the filesystem.
- `/etc/fstab` makes mounts persistent.
- Reboot testing is essential to confirm correct configuration.

