# Set variables
RESOURCE_GROUP="security-tools-rg"
LOCATION="eastus"
VNET_NAME="security-vnet"
SUBNET_NAME="security-subnet"
NSG_NAME="security-tools-nsg"
PUBLIC_IP_NAME="security-monitor-ip"
LB_NAME="security-monitor-lb"
VMSS_NAME="security-monitor-vmss"
VM_IMAGE="Ubuntu2204"  # Corrected image
ADMIN_USER="azureuser"
INSTANCE_COUNT=2  # Initial VM instances
MAX_INSTANCES=5   # Maximum VMs in autoscale
MIN_INSTANCES=1   # Minimum VMs in autoscale
SCALE_CPU_THRESHOLD=75  # CPU utilization percentage to scale out

# Create a resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP \
    --name $VNET_NAME \
    --address-prefix 10.0.0.0/16 \
    --subnet-name $SUBNET_NAME \
    --subnet-prefix 10.0.1.0/24

# Create a network security group
az network nsg create \
    --resource-group $RESOURCE_GROUP \
    --name $NSG_NAME

# Add NSG rule to allow SSH (Port 22)
az network nsg rule create \
    --resource-group $RESOURCE_GROUP \
    --nsg-name $NSG_NAME \
    --name AllowSSH \
    --protocol Tcp \
    --direction Inbound \
    --priority 1000 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 22 \
    --access Allow

# Create a public IP address
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --name $PUBLIC_IP_NAME \
    --sku Standard \
    --allocation-method Static

# Create a load balancer (required for VMSS)
az network lb create \
    --resource-group $RESOURCE_GROUP \
    --name $LB_NAME \
    --sku Standard \
    --public-ip-address $PUBLIC_IP_NAME \
    --backend-pool-name $VMSS_NAME-backend-pool

# Create a Virtual Machine Scale Set (VMSS) with autoscaling enabled
az vmss create \
    --resource-group $RESOURCE_GROUP \
    --name $VMSS_NAME \
    --image $VM_IMAGE \
    --admin-username $ADMIN_USER \
    --vnet-name $VNET_NAME \
    --subnet $SUBNET_NAME \
    --instance-count $INSTANCE_COUNT \
    --load-balancer $LB_NAME \
    --nsg $NSG_NAME \
    --upgrade-policy-mode Automatic \
    --generate-ssh-keys

# Enable autoscaling rules
az monitor autoscale create \
    --resource-group $RESOURCE_GROUP \
    --name "$VMSS_NAME-autoscale" \
    --target $VMSS_NAME \
    --min-count $MIN_INSTANCES \
    --max-count $MAX_INSTANCES \
    --count $INSTANCE_COUNT

# Add an autoscale rule to scale out when CPU usage is above the threshold
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "$VMSS_NAME-autoscale" \
    --condition "Percentage CPU > $SCALE_CPU_THRESHOLD avg 5m" \
    --scale out 1

# Add an autoscale rule to scale in when CPU usage is below a threshold
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "$VMSS_NAME-autoscale" \
    --condition "Percentage CPU < 30 avg 5m" \
    --scale in 1
