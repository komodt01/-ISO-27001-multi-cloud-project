Azure VM Autoscaling Configuration
This document provides instructions for setting up autoscaling for Azure Virtual Machines within the iso27001 resource group.
Prerequisites

Azure CLI installed
Logged in to Azure account
Existing VMs in the iso27001 resource group

Setup Instructions
1. Login to Azure
az login

2. Check Existing VMs
az vm list --resource-group iso27001 --output table

3. Create a VM Scale Set
az vmss create \
  --resource-group iso27001 \
  --name iso27001-scaleset \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --instance-count 2

  5. Add Scale-Out Rule (Scale Up)
  az monitor autoscale rule create \
  --resource-group iso27001 \
  --autoscale-name iso27001-autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 2

  6. Add Scale-In Rule (Scale Down)
  az monitor autoscale rule create \
  --resource-group iso27001 \
  --autoscale-name iso27001-autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1

  Additional Configuration Options
Creating a Scale Set from an Existing VM
If you want to create a scale set based on an existing VM:
# Create an image from an existing VM
az vm deallocate --resource-group iso27001 --name yourExistingVM
az vm generalize --resource-group iso27001 --name yourExistingVM
az image create --resource-group iso27001 --name myVMImage --source yourExistingVM

# Then use that image in your scale set
az vmss create \
  --resource-group iso27001 \
  --name iso27001-scaleset \
  --image myVMImage \
  --instance-count 2

Deleting Resources
If you need to remove the autoscale configuration:
# Delete the autoscale settings
az monitor autoscale delete \
  --resource-group iso27001 \
  --name iso27001-autoscale

# Delete the VM scale set (if needed)
az vmss delete \
  --resource-group iso27001 \
  --name iso27001-scaleset