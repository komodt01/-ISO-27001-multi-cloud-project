#managed disks provide better reliability and security with encryption options
# 1. Create a managed disk
az disk create \
    --resource-group SECURITY-TOOLS-RG \
    --name security-data-disk \
    --size-gb 1024 \
    --sku Premium_LRS \
    --encryption-type EncryptionAtRestWithPlatformKey \
    --location eastus

# 2. Attach the disk to your VM (if you already have a VM created)
az vm disk attach \
    --resource-group SECURITY-TOOLS-RG \
    --vm-name security-monitor-vm \
    --name security-data-disk

# 3. If creating a new VM with a managed OS disk and data disk
az vm create \
    --resource-group SECURITY-TOOLS-RG \
    --name security-vm \
    --image Canonical:0001-com-ubuntu-server-jammy:22_04-lts-gen2:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --size Standard_D2s_v3 \
    --os-disk-name security-os-disk \
    --os-disk-size-gb 128 \
    --data-disk-sizes-gb 1024 \
    --storage-sku Premium_LRS \
    --encryption-at-host true