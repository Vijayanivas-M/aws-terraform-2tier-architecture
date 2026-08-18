# Highly Available 2-Tier AWS Architecture via Terraform

## 📌 Project Overview
This project transitions manual AWS Cloud infrastructure provisioning ("ClickOps") into a fully automated, version-controlled **Infrastructure as Code (IaC)** pipeline using **Terraform**. 

The code deploys a highly available, scalable 2-tier web application architecture distributed across multiple Availability Zones to ensure fault tolerance and secure traffic routing.

## 🏗️ Architecture Design
*   **Virtual Private Cloud (VPC):** Custom isolated network environment.
*   **High Availability:** 4 Subnets (2 Public, 2 Private) distributed across `us-east-1a` and `us-east-1b`.
*   **Internet Gateway & Route Tables:** Configured to manage inbound/outbound internet traffic routing.
*   **Security Group Chaining:** Strict firewall rules where the Web Server security group only accepts traffic originating directly from the Load Balancer, blocking all direct internet access.
*   **Application Load Balancer (ALB):** Distributes incoming HTTP web traffic across multiple instances in different Availability Zones.
*   **Auto Scaling Group (ASG) & Launch Template:** Automatically provisions and manages `t3.micro` EC2 instances (running a Python HTTP server) based on demand and health checks.

```mermaid
graph TD
    Internet((Internet)) --> IGW[Internet Gateway]
    
    subgraph VPC [AWS VPC: 10.0.0.0/16]
        IGW --> ALB{Application Load Balancer \n Port 80}
        
        subgraph AZ1 [us-east-1a]
            PUB1[Public Subnet 1 \n 10.0.1.0/24]
            PRIV1[Private Subnet 1 \n 10.0.10.0/24]
            PUB1 -.- PRIV1
        end
        
        subgraph AZ2 [us-east-1b]
            PUB2[Public Subnet 2 \n 10.0.2.0/24]
            PRIV2[Private Subnet 2 \n 10.0.20.0/24]
            PUB2 -.- PRIV2
        end

        ALB -->|Forwards to Port 8000| ASG
        
        subgraph ASG [Auto Scaling Group]
            EC2_1(EC2 Instance 1 \n t3.micro)
            EC2_2(EC2 Instance 2 \n t3.micro)
        end
        
        PRIV1 -.-> EC2_1
        PRIV2 -.-> EC2_2
    end

    classDef aws fill:#FF9900,stroke:#232F3E,stroke-width:2px,color:black;
    classDef vpc fill:#F9F9F9,stroke:#333,stroke-width:2px;
    classDef subnet fill:#E6F7FF,stroke:#0066CC,stroke-width:1px;
    
    class Internet,IGW,ALB,EC2_1,EC2_2 aws;
    class VPC vpc;
    class AZ1,AZ2,PUB1,PUB2,PRIV1,PRIV2 subnet;
```

*   **Virtual Private Cloud (VPC):** Custom isolated network environment.
*   **High Availability:** 4 Subnets (2 Public, 2 Private) distributed across `us-east-1a` and `us-east-1b`.

## 🛠️ Technologies Used
*   **Cloud Provider:** Amazon Web Services (AWS)
*   **Infrastructure as Code:** Terraform (v1.15.8)
*   **Compute & Networking:** EC2, VPC, ALB, ASG, Security Groups
*   **Command Line Interfaces:** AWS CLI, WSL (Ubuntu)

## 🚀 Key Learnings & Skills Demonstrated
*   **Infrastructure Automation:** Completely eliminated manual cloud configuration.
*   **Security Best Practices:** Implemented least-privilege access using private subnets and Security Group chaining.
*   **Disaster Recovery & Fault Tolerance:** Leveraged multiple Availability Zones so the application remains online even if a data center goes down.
*   **Debugging & Troubleshooting:** Resolved port mismatch issues between ALB listeners and target groups, and navigated AWS free-tier instance limitations (transitioning from `t2.micro` to `t3.micro`).

## ⚙️ How to Deploy
1. Clone the repository.
2. Ensure AWS CLI is configured with valid credentials (`aws configure`).
3. Initialize the working directory:
   ```bash
   terraform init
