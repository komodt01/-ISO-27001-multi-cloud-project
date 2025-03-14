# Create a resource group for security tools
az group create --name security-tools-rg --location eastus

# Create a virtual network
az network vnet create \
    --resource-group security-tools-rg \
    --name security-vnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name security-subnet \
    --subnet-prefix 10.0.1.0/24

# Create a network security group
az network nsg create \
    --resource-group security-tools-rg \
    --name security-tools-nsg

# Add SSH rule
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name security-tools-nsg \
    --name AllowSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow

# Create public IP
az network public-ip create \
    --resource-group security-tools-rg \
    --name security-monitor-ip \
    --allocation-method Static

# Create network interface
az network nic create \
    --resource-group security-tools-rg \
    --name security-monitor-nic \
    --vnet-name security-vnet \
    --subnet security-subnet \
    --network-security-group security-tools-nsg \
    --public-ip-address security-monitor-ip

# Create monitoring VM
az vm create \
    --resource-group security-tools-rg \
    --name security-monitor-vm \
    --nics security-monitor-nic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --size Standard_D2s_v3 \
    --os-disk-size-gb 100 \
    --generate-ssh-keys