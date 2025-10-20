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

From your private VM (Tier 2), run:
curl -s ifconfig.me

✅ It should return your NAT Gateway’s public IP (Tier3-NAT-IP).

🔐 Step 3 – Configure NSG Outbound Rules (Private Subnet)

If the private VM cannot reach the internet:

Go to Tier2-Private-NSG → Outbound security rules → Add

Configure:

Source → Any

Destination → Service Tag → Internet

Port → *

Protocol → Any

Action → Allow

Priority → 200

Name → Allow-Internet-Outbound

Save and test again:
curl -s ifconfig.me

✅ If the IP matches your NAT Gateway, outbound routing is successful.

⚖️ Step 4 – Create a Public Load Balancer

The Public Load Balancer distributes inbound connections (SSH or HTTP) to your Tier1-VMs.

Azure Portal → Load Balancers → Create

Basics

Resource group: ResourceGroup1

Name: Tier3-Public-LB

SKU: Standard

Type: Public

Region: same as VNet

Frontend IP Configuration

Name: Tier3-LB-Frontend

Public IP: Create new → Tier3-LB-IP

SKU: Standard | Assignment: Static | Routing preference: Microsoft network

Backend Pool

Name: Tier3-BackendPool

Add: Tier1-VM NIC → ipconfig1

Health Probe

Name: Tier3-Probe-SSH

Protocol: TCP | Port: 22 | Interval: 5 | Unhealthy threshold: 2

Load-Balancing Rule

Name: Tier3-LB-Rule-SSH

Frontend IP: Tier3-LB-Frontend

Backend Pool: Tier3-BackendPool

Health Probe: Tier3-Probe-SSH

Protocol: TCP | Port: 22→22 | Session Persistence: None | Idle Timeout: 4 | Floating IP: Disabled

Review + Create → Create

✅ The Load Balancer is now deployed and ready to handle inbound traffic.

🧱 Step 5 – Configure NSG for Load Balancer Access

In your Tier1-NSG (Public Subnet), add inbound rules:

Direction	Source Type	Source	Port	Action	Priority	Name
Inbound	Service Tag	AzureLoadBalancer	22	Allow	100	Allow-LB-Probe
Inbound	Any	Any	22	Allow	200	Allow-SSH

🧪 Step 6 – Validate Connectivity
Inbound (Load Balancer)

ssh -i Tier1-VM_key.pem azureuser@<Tier3-LB-IP>

✅ If connected, the Load Balancer is functioning correctly.

Outbound (Private VM via NAT)

curl -s ifconfig.me

✅ Confirms NAT Gateway egress is working.

Azure Portal Checks

Load Balancer → Backend pools → Health probe status: Healthy

NAT Gateway → Metrics → SNAT connections: Increasing when outbound traffic occurs

         Internet
            │
    ┌────────────────────┐
    │ Public Load Balancer │
    └────────────────────┘
            │
   [ Tier 1 Public Subnet ]
            │
      Tier1-VM (front-end)
            │
   [ Tier 2 Private Subnet ]
            │
      Tier2-VM (database)
            │
    ┌────────────────────┐
    │     NAT Gateway     │
    └────────────────────┘

    ✅ Public requests → Load Balancer → Tier 1 VM
✅ Private VMs → NAT Gateway → Internet (outbound only)

🏁 Completion

You now have a fully functional three-tier Azure environment with:

Secure private outbound connectivity via NAT Gateway

Scalable inbound access via Public Load Balancer

Segmented NSGs enforcing clear traffic control

Next Tier (Optional): Add an Internal Load Balancer between Tier 1 and Tier 2 for private API or application-layer traffic.
