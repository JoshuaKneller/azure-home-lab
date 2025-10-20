☁️ Azure Tier 3 Walkthrough

This guide builds upon the Tier 1 and Tier 2 Azure environments by introducing:

🛰️ Outbound internet for private resources (via NAT Gateway)

⚖️ Inbound load balancing for public-facing VMs

The goal is to simulate a production-style three-tier architecture with secure routing, a NAT Gateway, and a Load Balancer.

🧩 Prerequisites

Completed Tier 1 and Tier 2 environments

ResourceGroup1, Tier1-VNet, and existing subnets

Tier1-VM (public) and Tier2-VM (private)

SSH key pair copied into Tier1-VM

Basic Linux CLI skills

🧱 Step 1 – Create a NAT Gateway

The NAT Gateway enables outbound-only internet access for private subnet VMs.

Azure Portal → NAT gateways → Create

Basics

Resource group → ResourceGroup1

Name → Tier3-NAT-Gateway

Region → same as your VNet

Outbound IP

Create new → name Tier3-NAT-IP

SKU → Standard

Routing preference → Microsoft network

Subnet association

Select Tier2-Private-Subnet (10.0.2.0/24)

Review + Create → Create

✅ Provides secure outbound internet access for private VMs.

🔎 Step 2 – Verify NAT Gateway Association

Virtual Networks → Tier1-VNet → Subnets

Select Tier2-Private-Subnet

Confirm Tier3-NAT-Gateway is listed under NAT Gateway

From your private VM:
