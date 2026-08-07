# Project Notes — Step-by-Step Implementation

**Date:** Week 2  
**Project:** AWS Lift & Shift — 3-Tier Java Web App

## Phase 1: Foundation & Networking

### Security Groups

Created three security groups via AWS CLI:

    aws ec2 create-security-group   --group-name project01-ELB-sg   --description "Load Balancer SG"   --vpc-id vpc-xxxxx

    aws ec2 create-security-group   --group-name project01-app-sg   --description "Tomcat App Server SG"   --vpc-id vpc-xxxxx

    aws ec2 create-security-group   --group-name project01-backend-sg   --description "MySQL, Memcached, RabbitMQ SG"   --vpc-id vpc-xxxxx

### EC2 Instances

Launched 3 Amazon Linux and 1 Ubuntu-based EC2 instances:

1.  `project01-app01` — Tomcat 10 ( Ubuntu )
2.  `project01-mc01` — Memcached ( Amazon )
3.  `project01-rmq01` — RabbitMQ ( Amazon )
4.  `project01-db01` — MySQL 8.0 ( Amazon )

Security group assignments: - App instance → `project01-app-sg` -
Backend instances → `project01-backend-sg`

### Route 53 Private Hosted Zone

    aws route53 create-hosted-zone   --name vprofile.in   --vpc VPCRegion=us-east-1,VPCId=vpc-xxxxx   --caller-reference $(date +%s)

Created A records: - `app01.vprofile.in` → app01 private IP -
`db01.vprofile.in` → db01 private IP - `mc01.vprofile.in` → mc01 private
IP - `rmq01.vprofile.in` → rmq01 private IP

Verified resolution:

    nc -zv db01.vprofile.in 3306
    nc –zv mc01.vprofile.in 11211
    nc –zv rmq01.vprofile.in 5672

## Phase 2: Application Deployment

### IAM Setup

Created: - **IAM Role:** `s3-admin-role` (attached to Tomcat EC2
instance) - **IAM User:** `s3-admin-user` (for local AWS CLI access)

    aws configure

The IAM Role was attached to the EC2 instance for S3 access. Using a
role is preferred over hardcoded credentials because AWS manages
temporary credential rotation automatically.

### Java & Maven Version Management

**Problem:** Local environment had Maven 3.9.16 and Java 21. The project
required Maven 3.9.9 and OpenJDK 17.

**Solution:** Installed SDKMAN for version switching.

    curl -s "https://get.sdkman.io" | bash
    source "$HOME/.sdkman/bin/sdkman.sh"

    sdk install java 17.0.9-amzn
    sdk install maven 3.9.9

    sdk use java 17.0.9-amzn
    sdk use maven 3.9.9

    java -version
    mvn -version

### Build & Upload WAR

    mvn clean install

    aws s3 mb s3://project01-artifacts-bucket

    aws s3 cp target/vprofile-v2.war s3://project01-artifacts-bucket/

### Deploy to Tomcat

    ssh -i project01-key.pem ubuntu@<tomcat-public-ip>

    aws s3 cp s3://project01-artifacts-bucket/vprofile-v2.war /tmp/

    sudo systemctl stop tomcat10

    sudo rm -rf /var/lib/tomcat10/webapps/ROOT


    sudo cp /tmp/vprofile-v2.war /var/lib/tomcat10/webapps/ROOT.war

    sudo systemctl start tomcat10

    Tomcat deploys the WAR as the root context when named ROOT.war. The old ROOT application was removed to prevent conflicts.

### Verification

- Application accessible at `http://<tomcat-public-ip>:8080`
- Database connectivity confirmed
- Caching and message queue functionality verified

## Phase 3: Application Load Balancer

### Target Group

    aws elbv2 create-target-group   --name project01-tg   --protocol HTTP   --port 8080   --vpc-id vpc-xxxxx   --health-check-protocol HTTP   --health-check-path /

### Register Target

    aws elbv2 register-targets   --target-group-arn arn:aws:elasticloadbalancing:...   --targets Id=i-xxxxxxxxx

### Create ALB

    aws elbv2 create-load-balancer   --name project01-alb   --subnets subnet-xxxxx subnet-yyyyy   --security-groups sg-xxxxxxxxx   --scheme internet-facing   --type application

### Create Listener

    aws elbv2 create-listener   --load-balancer-arn arn:aws:elasticloadbalancing:...   --protocol HTTP   --port 80   --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:...

### Security Group Issue

ALB targets showed **Unhealthy**. Root cause and resolution documented
in `troubleshooting.md`.

## Phase 4: Auto Scaling

### Create AMI

    aws ec2 create-image   --instance-id i-xxxxxxxxx   --name "project01-tomcat-ami"   --description "Tomcat with deployed vprofile app"

Waited for AMI status: `available`.

### Create Launch Template

    aws ec2 create-launch-template   --launch-template-name project01-tomcat-template   --version-description "v1"   --launch-template-data     '{
          "ImageId": "ami-xxxxxxxxx",
          "InstanceType": "t3.micro",
          "KeyName": "project01-key",
          "SecurityGroupIds": ["sg-app-sg-xxxxx"],
          "TagSpecifications": [{
            "ResourceType": "instance",
            "Tags": [{"Key": "Name", "Value": "project01-app"}]
          }]
        }'

### Create Auto Scaling Group

    aws autoscaling create-auto-scaling-group   --auto-scaling-group-name project01-asg   --launch-template LaunchTemplateName=project01-tomcat-template,Version='$Latest'   --min-size 1   --max-size 4   --desired-capacity 1   --vpc-zone-identifier "subnet-xxxxx,subnet-yyyyy"   --target-group-arns arn:aws:elasticloadbalancing:...   --health-check-type ELB   --health-check-grace-period 300

### Verification

- ASG launched new instance from AMI
- Instance auto-registered with Target Group
- ALB health checks: **Healthy**
- Application accessible via ALB DNS: `http://<alb-dns-name>:8080`

## Final Verification

- [x] 4 EC2 instances running
- [x] Route 53 private DNS resolving
- [x] Application deployed on Tomcat
- [x] ALB serving traffic (80 → 8080)
- [x] Target group healthy
- [x] Auto Scaling Group operational
- [x] Security groups enforcing tiered access
- [x] Application accessible via ALB DNS
