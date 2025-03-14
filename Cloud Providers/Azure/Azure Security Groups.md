# Create a Network Security Group
az network nsg create \
    --resource-group security-tools-rg \
    --name iso27001-security-nsg \
    --location eastus

# Add inbound rule for SSH access
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name iso27001-security-nsg \
    --name AllowSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow \
    --source-address-prefixes 10.0.0.0/16

# Add inbound rule for HTTPS access
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name iso27001-security-nsg \
    --name AllowHTTPS \
    --protocol tcp \
    --priority 1010 \
    --destination-port-range 443 \
    --access allow \
    --source-address-prefixes 10.0.0.0/16