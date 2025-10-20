Azure Multi-Tier Network Architecture Project – Tier 3

Article Complexity: [X] Novice [X] Apprentice [X] Adept [] Expert [] Master [] Legendary

🧭 Overview

The purpose of this project is to build upon your existing Tier 1 and Tier 2 Azure environments to demonstrate a multi-tier production-style network architecture.

This includes:

Implementing a NAT Gateway for secure outbound internet on private subnets

Deploying a Public Load Balancer for inbound access to front-end VMs

Reinforcing knowledge of VNets, NSGs, and subnet associations

🎯 Learning Criteria
Tier 3 (NAT Gateway + Load Balancer Integration)

Add a NAT Gateway to the private subnet for outbound connectivity

Add a Public Load Balancer (Standard SKU) for inbound SSH/web access

Verify secure network segmentation between Tier 1 (Public) and Tier 2 (Private)

Validate correct routing, NAT translation, and load balancing

🧩 Prerequisites

Completed Tier 1 and Tier 2 environments

Resource group: ResourceGroup1

Tier1-VNet with public and private subnets

Tier1-VM (public) and Tier2-VM (private) already created

SSH key pair copied into Tier1-VM

Basic Linux CLI skills

🧱 Step 1 – Create a NAT Gateway

Azure Portal → NAT gateways → Create

Basics

Resource group: ResourceGroup1

Name: Tier3-NAT-Gateway

Region: same as your VNet

Outbound IP

Create new → name Tier3-NAT-IP

SKU: Standard

Routing preference: Microsoft network

Subnet association

Select Tier2-Private-Subnet (10.0.2.0/24)

Review + Create → Create

✅ The NAT Gateway enables outbound-only internet access for private VMs.

🔎 Step 2 – Verify NAT Gateway Association

Virtual Networks → Tier1-VNet → Subnets

Select Tier2-Private-Subnet

Confirm that Tier3-NAT-Gateway is listed under NAT Gateway

From your private VM (Tier 2):
