AWS VPC Production Environment Setup
📌 Project Overview

This project demonstrates the creation of a highly available and secure VPC architecture in AWS that can be used in a production environment.

The setup includes:

2 Public and 2 Private Subnets across 2 Availability Zones for high availability.

Application Load Balancer (ALB) to distribute traffic across multiple EC2 instances.

Auto Scaling Group (ASG) to handle scaling of EC2 instances automatically.

2 NAT Gateways (one per AZ) for secure outbound internet access from private subnets.

Bastion Host for secure SSH access to private EC2 instances.

🏗️ Architecture Diagram
flowchart TB
    Internet -->|HTTPS/HTTP| ALB
    ALB -->|Traffic| ASG
    ASG --> EC2_1[Private EC2 - AZ1]
    ASG --> EC2_2[Private EC2 - AZ2]
    
    Bastion[Bastion Host (Public Subnet)] --> EC2_1
    Bastion --> EC2_2

    EC2_1 -->|Outbound| NAT1[NAT Gateway AZ1]
    EC2_2 -->|Outbound| NAT2[NAT Gateway AZ2]

    subgraph VPC[VPC]
      subgraph Public[Public Subnets]
        ALB
        Bastion
        NAT1
        NAT2
      end
      subgraph Private[Private Subnets]
        EC2_1
        EC2_2
      end
    end

⚙️ Components and Their Purpose
1️⃣ VPC (Virtual Private Cloud)

A logically isolated network within AWS.

Provides full control over IP addressing, routing, and security.

Used to securely host public-facing and internal applications.

2️⃣ Subnets

Public Subnets (2):

Used for ALB, Bastion Host, and NAT Gateways.

Have internet gateway route for inbound/outbound internet access.

Private Subnets (2):

Used for hosting EC2 instances behind ALB.

No direct internet access; outbound traffic goes via NAT Gateways.

3️⃣ Internet Gateway (IGW)

Allows communication between VPC resources and the internet.

Attached to the VPC and routes traffic from public subnets.

4️⃣ NAT Gateways (2, one per AZ)

Provide outbound internet access to EC2 instances in private subnets.

Ensures high availability by deploying one NAT Gateway in each AZ.

Prevents direct inbound traffic from the internet.

5️⃣ Bastion Host (Jump Box)

Secure entry point to access private EC2 instances.

Deployed in public subnet with restricted SSH access (via Security Groups).

Enhances security by avoiding direct SSH into private resources.

6️⃣ Application Load Balancer (ALB)

Distributes incoming traffic across multiple EC2 instances in private subnets.

Provides high availability, fault tolerance, and automatic health checks.

Operates at Layer 7 (HTTP/HTTPS), supporting advanced routing features.

7️⃣ Auto Scaling Group (ASG)

Automatically adjusts EC2 instance count based on demand.

Ensures application availability during traffic spikes.

Works with ALB to register/deregister instances dynamically.

8️⃣ EC2 Instances

Application servers hosted in private subnets.

Do not have public IPs for enhanced security.

Accessible via Bastion Host for administration.

9️⃣ Route Tables

Public Route Table: Routes traffic from public subnets to Internet Gateway.

Private Route Table: Routes outbound traffic from private subnets to NAT Gateway.

🔒 Security Considerations

Security Groups are configured with least privilege principle.

Bastion Host restricted to trusted IPs for SSH access.

ALB exposed to the internet, but EC2 instances remain private.

NAT Gateways allow secure outbound access only.

✅ Key Benefits of this Architecture

High Availability (resources spread across 2 AZs).

Scalability (Auto Scaling with ALB).

Security (private subnets, Bastion Host, NAT Gateways).

Cost Optimization (elastic scaling based on demand).

📂 Repository Structure Example
aws-vpc-production-setup/
│── README.md          # Project Documentation
│── architecture.png   # Optional - Exported Architecture Diagram
│── terraform/         # (If you use Terraform/IaC)
│   └── main.tf
│── cloudformation/    # (If you use CFN templates)
│   └── template.yaml