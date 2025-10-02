Azure Tier 1 Walkthrough

This guide will walk through creating a basic Azure environment with a single Virtual Machine (VM) inside a public subnet. This is a tier 1 of 3, designed for learning the basics of cloud engineering.

Prerequisites

    - Azure account (free trial or pay-as-you-go is fine)
    - Basic IT background
    - Basic linux CLI skills
    - Text editor basic skills (vim, nano etc)

Step 1: Create a Resource Group

A Resource Group is a logical container for all resources.

    - In the Azure Portal, search for Resource groups>Create
    - Resource Group Name: ResourceGroup1
    - Region: Select a local region (e.g., Australia East)
    - Click Review + Create, then Create

Step 2 - Create a Virtual Network (VNet)

A Virtual Network (VNet) is like a VPC in AWS, it defines your private IP space.

    - In the Azure Portal, search for Virtual Networks>Create
    - Name: Tier1-VNet
    - Region: Same as your resource group
    - Resource Group: ResourceGroup1
    - IPv4 Address space: 10.0.0.0/16
    - Subnet:
        - Name: Tier1-Public-Subnet
        - Subnet address range: 10.0.1.0/24
    - Click Review + Create, then Create

Step 3 - Create a Public IP Address

This assigns your VM a public-facing IP so you can connect to it.

    - In Azure Portal, search for Public IP addresses>Create
    - Name: Tier1-Public-IP
    - Region: Same as above
    - SKU: Basic
    - Assignment: Static (some regions no longer support Dynamic)
    - Resource Group: ResourceGroup1
    - Click Review + Create, then Create

Step 4 - Create a Network Security Group (NSG)

An NSG in Azure is like a Security Group in AWS, it controls inbound/outbound traffic.

    - Search Network Security Groups>Create
    - Name: Tier1-NSG
    - Region: Same as resource group
    - Resource Group: ResourceGroup1
    - Once created, go into the NSG:
        - On the left-hand menu, go to Settings>Inbound security rules
        - Add a rule:
            - Name: Allow-SSH
            - Source: Any (or your IP)
            - Service: SSH
            - Port: 22
            - Action: Allow
            - Priority: 1000
    - Save the rule

Step 5 - Create a Virtual Machine

This launches a Linux VM in the VNet.

    - In Azure Portal, search for Virtual Machines>Create
    - Basics:
        - Resource Group: ResourceGroup1
        - VM Name: Tier1-VM
        - Region: Same as your VNet
        - Image: Ubuntu 22.04 LTS
        - Size: B1s (free tier eligible)
    - Authentication:
        - Select SSH Public Key
        - Generate new key pair (download and keep .pem or .ppk)
    - Networking:
        - Virtual Network: Tier1-VNet
        - Subnet: Tier1-Public-Subnet
        - Public IP: Tier1-Public-IP
    - Advanced:
        - Under NIC network security group, select Existing>Tier1-NSG
    - Click Review + Create, then Create

Step 6 - Connect to the VM via SSH

Once deployment completes, grab the Public IP of the VM.

    - chmod 400 [KEYNAME].pem
    - ssh -i [KEYNAME].pem azureuser@<Public-IP>

Default username is usually azureuser unless changed during setup.

Inside the VM, run:

    - sudo apt update && sudo apt upgrade -y
