Azure Tier 2 Walkthrough

This guide builds upon the Tier 1 Azure environment by adding a private subnet, a second VM, and secure access patterns.
The goal is to simulate a two-tier cloud network similar to an application and database setup.

Prerequisites

    - Completed Tier 1 environment (ResourceGroup1, Tier1-VNet, Tier1-Public-Subnet, Tier1-VM)
    - SSH key pair from Tier 1 VM creation (already copied into Tier1-VM)
    - Basic Linux CLI skills

Step 1 - Create a Private Subnet

    - Azure Portal → Virtual Networks → Tier1-VNet → Subnets → +Subnet
    - Name: Tier2-Private-Subnet
    - Address range: 10.0.2.0/24
    - Leave NSG blank (attach later)
    - Save

Step 2 - Create a Private NSG

    - Search "Network security groups" → Create
    - Name: Tier2-Private-NSG
    - Resource group: ResourceGroup1
    - Region: same as VNet
    - After creation → Settings → Inbound security rules → Add:
        - Name: Allow-AppSSH
        - Source: 10.0.1.0/24
        - Destination port: 22
        - Protocol: TCP
        - Action: Allow
        - Priority: 1000
    - Save

Step 3 - Attach the NSG to the Private Subnet

    - Go to Virtual Networks → Tier1-VNet → Subnets
    - Select Tier2-Private-Subnet
    - Under Network security group, choose Tier2-Private-NSG
    - Save

Step 4 - Create the Private VM (Database VM)

    - Azure Portal → Virtual Machines → Create
    - Basics:
        - Resource group: ResourceGroup1
        - VM name: Tier2-DB-VM
        - Region: same as Tier1-VM
        - Image: Ubuntu 22.04 LTS
        - Size: B1s
        - Authentication: SSH public key → Use existing key stored in Azure
    - Networking:
        - Virtual network: Tier1-VNet
        - Subnet: Tier2-Private-Subnet
        - Public IP: None
        - NIC NSG: Select existing → Tier2-Private-NSG
    - Review + Create → Create

Step 5 - Connect to the Private VM

    - From your local machine:
        ssh -i Tier1-VM_key.pem azureuser@<Tier1-Public-IP>
    - From inside Tier1-VM:
        chmod 400 ~/Tier1-VM_key.pem
        ssh -i ~/Tier1-VM_key.pem azureuser@10.0.2.x
    - You should now be inside the private VM.

Step 6 - Verify Connectivity

    - From Tier1-VM:
        ping -c 3 10.0.2.x   # should succeed
    - From your local machine:
        ping 10.0.2.x        # should fail (blocked by design)

Step 7 - Remove Public IP if Accidentally Assigned

    - Azure Portal → Virtual Machines → Tier2-DB-VM → Networking
    - Click the NIC name (e.g., tier2-db-vm-nic)
    - Go to IP configurations → ipconfig1
    - Under "Public IP address", select None → Save
    - Optional: Azure Portal → Public IP addresses → Delete the detached address
    - Confirm the VM only shows a private IP (10.0.2.x) and no public IP.

End State

    - Virtual network: Tier1-VNet (10.0.0.0/16)
    - Subnets:
        - Tier1-Public-Subnet (10.0.1.0/24)
        - Tier2-Private-Subnet (10.0.2.0/24)
    - NSGs:
        - Tier1-NSG: allows SSH from internet
        - Tier2-Private-NSG: allows SSH only from 10.0.1.0/24
    - VMs:
        - Tier1-VM: bastion/app layer (public)
        - Tier2-DB-VM: private/database layer (no public IP)
