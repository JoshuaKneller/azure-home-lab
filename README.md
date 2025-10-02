# Azure Multi-Tier Network Architecture Project

Article Complexity: [X] Novice [] Apprentice [] Adept [] Expert [] Master [] Legendary

---

## Overview

The purpose of this project is to demonstrate multi-tier **Azure network architecture**.  
This includes:

- Practicing Azure networking concepts (VNet, Subnets, NSGs)  
- Understanding routing and Public IP vs NAT Gateway use cases  
- Gaining experience with secure access patterns in Azure environments  

---

## Learning Criteria

### Tier 1 (Basic Public VM)
- Resource Group (`ResourceGroup1`)
- Virtual Network (VNet) with /16 CIDR block (10.0.0.0/16)
- Public Subnet (/24 CIDR block: 10.0.1.0/24)
- Static Public IP assigned to VM
- Network Security Group (NSG) with inbound SSH rule
- Linux VM (Ubuntu) in public subnet with SSH access  

### Tier 2 (Public + Private Tiers)
- Add a Private Subnet (/24 CIDR block: 10.0.2.0/24)
- Deploy second VM into Private Subnet (DB-VM)
- Configure NSG rules for intra-tier communication (App VM ↔ DB VM)
- Use Public VM (App-VM) as a **bastion host** to SSH into DB-VM
- Route table associations to control subnet traffic

### Tier 3 (Secure Multi-Tier Architecture)
- Deploy a **NAT Gateway** for private subnet outbound internet access
- Enforce **NSG rules** so private VM has no direct inbound access
- Add Application Security Groups (ASGs) to simplify tier-based rules
- Introduce Load Balancer (Public LB) for App VM scaling
- Configure diagnostic logs and monitoring (Azure Monitor / Log Analytics)
- Implement storage account or database service in the private subnet
- Secure communication between tiers with least privilege

---

## Usage

- SSH into App VM (Public Subnet, Static IP)  
  ```bash
  ssh -i [KEYNAME].pem azureuser@<Public-IP>
