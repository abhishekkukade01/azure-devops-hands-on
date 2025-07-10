✅ Task 1: Create a Basic Virtual Network and Subnet
Goal: Understand VNets, address spaces, and subnets.

Create a VNet with CIDR block 10.0.0.0/16

Inside it, create a subnet 10.0.1.0/24

Deploy a Linux VM in that subnet.

Verify IP addressing and connectivity (ping within subnet)

🧠  Goal: You'll be able to understand IP ranges, subnetting, and internal communication.

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

![image](https://github.com/user-attachments/assets/564d4cfa-e7be-426e-9784-0a5ca3c63ae0)
![image](https://github.com/user-attachments/assets/2a995b98-2211-4cb4-bc81-6e7ece4bda93)

_______________________________________________________________________________________________________________________
✅ Task 2: Secure the VM with NSG Rules
Goal: Learn traffic control using Network Security Groups.
Attach an NSG to your VM NIC or Subnet.
Allow only:
SSH (22) from your IP
HTTP (80) from any
Deny all other inbound traffic
Test using SSH and curl

🧠 Goal : You'll understand NSG rule structure, direction, priority, and real use cases.

          Step 1: Create an NSG
          Go to Azure Portal → Search for "Network Security Groups" → Create
          Fill in:
          Name: DevOps-NSG
          Region: Same as VM
          Resource Group: DevOps-RG
          Click Review + Create, then Create

          Step 2: Attach NSG to VM NIC
          Go to Virtual Machines → Click your DevOps-LinuxVM
          Under Networking, click the Network Interface (NIC) name
          In NIC blade, go to Network Security Group → Associate
          Select:
          Network Security Group: DevOps-NSG
          Click Save

          Step 3: Add Inbound Security Rules
           we add 3 inbound rules:
              1. Allow SSH (22) from your IP
                 Go to DevOps-NSG → Inbound Security Rules → Add
                 Fill in:
                   Source: IP Addresses
                   Source IP address: your public IP
                   Destination port ranges: 22
                   Protocol: TCP
                   Action: Allow
                   Priority: 100
                   Name: Allow-SSH-MyIP
                   Click Add
          
                  2. Allow HTTP (80) from anywhere
                     Add new rule again:
                     Source: Any
                     Destination port ranges: 80
                     Protocol: TCP
                     Action: Allow
                     Priority: 200
                     Name: Allow-HTTP-All
                     Click Add
          
                  3. Deny All Other Inbound Traffic
                     Azure adds this by default at priority 65500. So, no need to add manually.

![image](https://github.com/user-attachments/assets/fb13792e-a3fb-4e2b-84f2-f83c1196844a)
![image](https://github.com/user-attachments/assets/a7d5f043-8883-4823-8fd2-fefd1757403a)

