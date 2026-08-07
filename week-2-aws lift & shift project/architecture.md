# Architecture & Security Design

## Security Groups

Three-tiered security model with least-privilege access between layers.

| Security Group         | Purpose                    | Inbound Rules                                                                                                 |
|------------------------|----------------------------|---------------------------------------------------------------------------------------------------------------|
| `project01-ELB-sg`     | Load Balancer              | HTTP (80), HTTPS (443), Custom TCP (8080) from `0.0.0.0/0`                                                    |
| `project01-app-sg`     | Tomcat App Server          | SSH (22) from admin IP, HTTP (80), Custom TCP (8080) from `project01-ELB-sg`` `                               |
| `project01-backend-sg` | MySQL, Memcached, RabbitMQ | MySQL/Aurora (3306), Custom TCP (5672), Custom TCP (11211), from `project01-app-sg``. `SSH (22) from admin IP |

**Design rationale:** The application tier only accepts port 8080
traffic from the ALB security group. Direct access to the app server
from the internet is blocked. The backend tier only accepts traffic from
the application tier and for service checking SSH from my IP.

## EC2 Instances

| Instance Name     | Role                      | Security Group         | Private DNS         |
|-------------------|---------------------------|------------------------|---------------------|
| `project01-app01` | Tomcat Application Server | `project01-app-sg`     | `app01.vprofile.in` |
| `project01-mc01`  | Memcached Caching Layer   | `project01-backend-sg` | `mc01.vprofile.in`  |
| `project01-rmq01` | RabbitMQ Message Broker   | `project01-backend-sg` | `rmq01.vprofile.in` |
| `project01-db01`  | MySQL Database Server     | `project01-backend-sg` | `db01.vprofile.in`  |

## Route 53 Private Hosted Zone

- **Zone Name:** `vprofile.in`
- **Rationale:** Named to match the application’s
  `application.properties` file. No application code changes were
  required.
- **Records:**
  - `app01.vprofile.in` → Tomcat instance private IP
  - `db01.vprofile.in` → MySQL instance private IP
  - `mc01.vprofile.in` → Memcached instance private IP
  - `rmq01.vprofile.in` → RabbitMQ instance private IP

Using DNS names instead of hardcoded IPs ensures backend endpoints
remain stable if instance IPs change, and new Auto Scaling instances
resolve the correct endpoints automatically.

## Load Balancer

### Target Group

| Setting             | Value                   |
|---------------------|-------------------------|
| Protocol            | HTTP                    |
| Port                | 8080                    |
| Health Check Path   | `/`                     |
| Healthy Threshold   | 2 consecutive successes |
| Unhealthy Threshold | 2 consecutive failures  |

### Application Load Balancer

| Setting        | Value                       |
|----------------|-----------------------------|
| Type           | Internet-facing             |
| Listener       | HTTP:80 → Target Group:8080 |
| Security Group | `project01-ELB-sg`          |

## Auto Scaling

| Setting          | Value              |
|------------------|--------------------|
| Minimum Capacity | 1                  |
| Maximum Capacity | 4                  |
| Launch Template  | Tomcat AMI         |
| Security Group   | `project01-app-sg` |
| Target Group     | Attached to ALB    |

### AMI Creation

1.  Configured the Tomcat instance with the deployed WAR file
2.  Verified all service connections were operational
3.  Created an AMI from the running instance
4.  The AMI captures the full environment: OS, Tomcat, WAR file, and
    configuration

New instances launched from this AMI are ready to serve traffic
immediately without manual configuration.
