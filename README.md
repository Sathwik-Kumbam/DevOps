# DevOps Project

## Overview
This project demonstrates deploying an application on AWS EC2 using docker and provisoing infrastructure using terraform.

## Tech Stack Used
  - AWS EC2
  - Docker
  - Terraform

This project demonstrates a complete DevOps workflow including:

* Infrastructure provisioning using Terraform
* Application deployment using Docker
* Security best practices
* Monitoring and logging using Prometheus, Grafana, and Loki

The goal is to simulate a **production-like cloud environment**.

---

#  Infrastructure Setup (Terraform)

Infrastructure is provisioned using **Terraform** on AWS.

## Components Created

* VPC (Virtual Private Cloud)
* Public Subnet
* Private Subnet (for future use)
* Internet Gateway
* Route Table
* Security Group
* EC2 Instance

## How it Works

1. **VPC Creation**

   * A VPC with CIDR block `10.0.0.0/16` is created to isolate the network.

2. **Subnets**

   * Public Subnet (`10.0.1.0/24`) → hosts EC2
   * Private Subnet (`10.0.2.0/24`) → reserved for database

3. **Internet Gateway**

   * Enables internet access for resources inside the VPC

4. **Route Table**

   * Routes `0.0.0.0/0` traffic to the Internet Gateway

5. **Security Group**

   * Allows:

     * SSH (22) → restricted to my IP
     * HTTP (80) → open for users

6. **EC2 Instance**

   * Ubuntu-based instance
   * Deployed inside the public subnet
   * Associated with security group

---

#  EC2 & Elastic IP

* EC2 instance is launched using Terraform
* An Elastic IP is attached to ensure a **static public IP**

---

#  Application Deployment (Docker)

The application is deployed using Docker and Docker Compose.

## Services Used

* **App** → `twentycrm/twenty`
* **Database** → PostgreSQL
* **Cache** → Redis

## Source Code

The application is cloned from:

  https://github.com/twentyhq/twenty

---

## Dockerization

Instead of building manually, we use a pre-built Docker image:

```yaml
image: twentycrm/twenty:latest
```

---

## Docker Compose Setup

All services are defined in `docker-compose.yml`:

* App connects to PostgreSQL and Redis
* Services communicate via Docker network
* Only port 80 is exposed externally

---

## Deployment Steps

1. Install Docker on EC2
2. Clone repository or create docker-compose.yml
3. Run:

```bash
docker-compose up -d
```

4. Application becomes available at:

```text
http://<Elastic-IP>
```

---

#  Security Implementation

##  Secrets Management

* Sensitive data stored using environment variables
* Example:

```env
DB_USER=twenty
DB_PASSWORD=securepassword
APP_SECRET=supersecret
```

* Avoid hardcoding credentials

---

##  Least Privilege Access

* SSH access restricted to personal IP
* Only required ports exposed

---

##  Network Restrictions

* Redis and PostgreSQL are NOT publicly exposed
* Only application port (80) is open

---

#  Observability (Monitoring & Logging)

---

##  Prometheus (Metrics Collection)

* Scrapes metrics every 15 seconds
* Collects data from Node Exporter

---

##  Node Exporter

* Provides system metrics:

  * CPU usage
  * Memory usage
  * Disk usage
  * Network stats

---

##  Grafana (Visualization)

* Connected to Prometheus as data source
* Imported dashboard ID **1860**
* Displays:

  * CPU usage
  * Memory usage
  * Disk usage
  * System health

---

##  Loki (Logging)

* Collects logs from containers
* Centralized logging system

---

##  Monitoring Flow

```text
Node Exporter → Prometheus → Grafana
Logs → Loki → Grafana
```

---

#  Issues & Fixes

## Issue: Grafana showing no data

### Cause:

Mismatch between Prometheus job label and Grafana dashboard

```text
Prometheus: job="node"
Grafana expects: job="node_exporter"
```

### Fix:

```yaml
job_name: "node_exporter"
```

---

## Issue: Docker no space left

* Cleaned unused images and volumes

---

## Issue: App container restarting

* Fixed database connection configuration

---

#  Grafana Dashboard

Grafana dashboard provides:

* CPU utilization
* Memory consumption
* Disk usage
* Network activity
* System uptime
<img width="720" height="332" alt="image" src="https://github.com/user-attachments/assets/f0bed68e-bee1-4f11-83fd-9be80365c3e7" />

This helps in **real-time monitoring of system health**


---

#  Key Learnings

* Infrastructure as Code using Terraform
* Containerization using Docker
* AWS networking (VPC, subnets, security groups)
* Monitoring using Prometheus & Grafana
* Debugging real-world production issues

---

#  Conclusion

This project demonstrates a **complete DevOps pipeline** including:

* Automated infrastructure
* Secure deployment
* Containerized application
* Full observability

It simulates a real-world production environment and showcases practical DevOps skills.
