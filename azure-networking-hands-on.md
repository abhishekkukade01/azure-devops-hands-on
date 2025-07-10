✅ Task 1: Create a Basic Virtual Network and Subnet
Goal: Understand VNets, address spaces, and subnets.

Create a VNet with CIDR block 10.0.0.0/16

Inside it, create a subnet 10.0.1.0/24

Deploy a Linux VM in that subnet.

Verify IP addressing and connectivity (ping within subnet)

🧠 Interview insight: You'll be able to explain IP ranges, subnetting, and internal communication.

solutions: 

          Step 1: Create a Resource Group 
          Go to Resource Groups → Create
          Name: Abhishek_devops
          Region: Choose your closest 
          Click Review + Create, then Create
          
          Step 2: Create Virtual Network
          Go to Virtual Networks → Create
          Resource Group: Abhishek_devops
          Name: Abhishek_vnet
          Region: Same as RG
          IPv4 address space: 10.0.0.0/16

          Step 4: Create a Subnet
          Subnet name: Subnet-1
          Subnet address range: 10.0.1.0/24
          Click Add
          Click Review + Create, then Create

          Step 5: Create a Linux Virtual Machine
          Go to Virtual Machines → Create
          Resource Group: Abhishek_devops
          VM Name: DevOps-LinuxVM3
          Region: Same
          Image: Ubuntu 22.04 LTS
          Size: Standard B1s
          Username: azureuser
          Authentication: Password or SSH
          Virtual Network: Abhishek_vnet
          Subnet: Subnet-1
          Public IP: Yes (for initial access)
          NIC Network Security Group: Basic, allow SSH (port 22)
          Click Review + Create, then Create

          Note: By default, all VMs in the same subnet can talk to each other privately using their private IPs.
          No NSG rules are needed unless we restrict them.

![image](https://github.com/user-attachments/assets/597ff241-89f4-4200-8274-e5895d16037f)



