# CloudWatch CPU Alarm + SNS Email Alert – Week 1 

## What I built (my real workflow)
1. Selected my EC2 instance (t3.micro) in us-east-1c.
2. Created a CloudWatch alarm to monitor CPUUtilization.
3. Set the threshold to trigger when CPU > 60%.
4. Created a simple bash script that runs `stress --cpu 38 --timeout 60` to push CPU above 60%.
5. Made the script executable and ran it using `nohup` so it continued running in the background.
6. Reused my existing SNS email subscription (the same one I use for my AWS billing alarm).
7. Waited for CloudWatch to evaluate the metric and received the CPU alarm notification.
8. Waited for the alarm to enter ALARM state.
9. Received the email alert successfully.
10. Observed the alarm return to OK state after CPU dropped.

## Why this matters (DevOps + Architect perspective)
- Monitoring is essential for:
  - Auto Scaling
  - Incident response
  - Performance tuning
  - Cost optimization
  - Production reliability
- CloudWatch alarms + SNS notifications are the foundation of:
  - Automated alerts
  - On-call systems
  - Auto-healing infrastructure
  - Scaling policies

This is real DevOps monitoring, not beginner AWS.

## What confused me initially
- Why the alarm didn’t trigger immediately.
- Why CloudWatch metrics have a delay.
- How SNS subscriptions work.
- How to generate CPU load for testing.

## How I fixed my confusion
- Learned that CloudWatch metrics update every 1 minute (for EC2).
- Understood that alarms evaluate over a period (e.g., 2 datapoints).
- Verified SNS subscription through email.
- Used CPU stress commands to force the alarm to trigger.

## Key takeaways
- CloudWatch alarms monitor metrics and trigger actions.
- SNS sends notifications to email, SMS, Lambda, or other systems.
- CPU alarms are commonly used for Auto Scaling.
- Testing alarms is essential to confirm they work.

