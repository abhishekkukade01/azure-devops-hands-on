# Azure CLI Practice - All Commands

## Login to Azure
az login

## Create Resource Group
az group create --name MyResourceGroup --location eastus

## Create Virtual Machine
az vm create \
  --resource-group MyResourceGroup \
  --name myVM \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --size Standard_B1s \
  --location eastus

## Get Public IP of VM
az vm list-ip-addresses \
  --resource-group MyResourceGroup \
  --name myVM \
  --query "[0].virtualMachine.network.publicIpAddresses[0].ipAddress" \
  --output tsv

## SSH into VM
ssh azureuser@<public-ip>

## Create Virtual Network and Subnet
az network vnet create \
  --name MyVNet \
  --resource-group MyResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name MySubnet \
  --subnet-prefix 10.0.1.0/24 \
  --location eastus

## Assign NSG to Subnet
az network vnet subnet update \
  --name MySubnet \
  --vnet-name MyVNet \
  --resource-group MyResourceGroup \
  --network-security-group MyNSG

## Create Storage Account
az storage account create \
  --name mystorage123 \
  --resource-group MyResourceGroup \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
