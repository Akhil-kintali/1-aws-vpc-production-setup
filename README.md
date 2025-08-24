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

# AWS VPC Production Environment Setup

## Overview
This project demonstrates a highly available and secure AWS VPC architecture suitable for production environments. It includes public/private subnets, Application Load Balancer (ALB), Auto Scaling Group (ASG), NAT Gateways, and a Bastion Host.

## Architecture Diagram
Text-based diagram (for quick reference):

```mermaid
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
```

For a visual diagram, see `vpc-example-private-subnets.png`.

## Components

- **VPC (Virtual Private Cloud):** Isolated network, full control over IPs, routing, and security.
- **Subnets:**
  - *Public Subnets (2):* For ALB, Bastion Host, NAT Gateways. Internet access enabled.
  - *Private Subnets (2):* For EC2 instances. No direct internet; outbound via NAT.
- **Internet Gateway (IGW):** Enables internet access for public subnets.
- **NAT Gateways (2):** Outbound internet for private subnets, one per AZ for HA.
- **Bastion Host:** Secure SSH access to private EC2s, restricted by Security Groups.
- **Application Load Balancer (ALB):** Distributes traffic, provides HA and health checks.
- **Auto Scaling Group (ASG):** Scales EC2s based on demand, integrates with ALB.
- **EC2 Instances:** Application servers in private subnets, no public IPs.
- **Route Tables:**
  - *Public:* Routes to IGW.
  - *Private:* Routes outbound to NAT Gateway.

## Security Considerations

- Security Groups follow least privilege.
- Bastion Host restricted to trusted IPs.
- ALB is public; EC2s remain private.
- NAT Gateways allow only outbound access.

## Key Benefits

- **High Availability:** Resources across 2 AZs.
- **Scalability:** Auto Scaling with ALB.
- **Security:** Private subnets, Bastion Host, NAT Gateways.
- **Cost Optimization:** Elastic scaling.

## Repository Structure Example

```
aws-vpc-production-setup/
│── README.md          # Project Documentation
│── vpc-example-private-subnets.png   # Architecture Diagram
│── terraform/         # (If using Terraform/IaC)
│   └── main.tf
│── cloudformation/    # (If using CloudFormation)
│   └── template.yaml
```
7️⃣ Auto Scaling Group (ASG)
