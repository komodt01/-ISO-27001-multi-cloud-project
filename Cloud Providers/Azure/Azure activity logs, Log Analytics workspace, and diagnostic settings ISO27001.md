# 1. Create a Log Analytics workspace
az monitor log-analytics workspace create \
    --resource-group SECURITY-TOOLS-RG \
    --workspace-name iso27001-analytics \
    --location eastus \
    --sku PerGB2018 \
    --retention-time 90

# 2. Configure Azure Activity Log to send to Log Analytics
# Get the resource ID of the Log Analytics workspace
WORKSPACE_ID=$(az monitor log-analytics workspace show --resource-group SECURITY-TOOLS-RG --workspace-name iso27001-analytics --query id -o tsv)

# Enable Activity Log collection to Log Analytics at subscription level
az monitor diagnostic-settings create \
    --name iso27001-activity-logs \
    --resource $(az account show --query id -o tsv) \
    --logs '[{"category":"Administrative","enabled":true},{"category":"Security","enabled":true},{"category":"ServiceHealth","enabled":true},{"category":"Alert","enabled":true},{"category":"Recommendation","enabled":true},{"category":"Policy","enabled":true},{"category":"Autoscale","enabled":true},{"category":"ResourceHealth","enabled":true}]' \
    --workspace $WORKSPACE_ID

# 3. Configure diagnostic settings for specific resources
# For Network Security Group
NSG_ID=$(az network nsg show --resource-group SECURITY-TOOLS-RG --name iso27001-security-nsg --query id -o tsv)

az monitor diagnostic-settings create \
    --name iso27001-nsg-diag \
    --resource $NSG_ID \
    --logs '[{"category":"NetworkSecurityGroupEvent","enabled":true},{"category":"NetworkSecurityGroupRuleCounter","enabled":true}]' \
    --workspace $WORKSPACE_ID

# For Azure Firewall (if you've created one)
FIREWALL_ID=$(az network firewall show --resource-group SECURITY-TOOLS-RG --name ngfw1 --query id -o tsv)

az monitor diagnostic-settings create \
    --name iso27001-fw-diag \
    --resource $FIREWALL_ID \
    --logs '[{"category":"AzureFirewallApplicationRule","enabled":true},{"category":"AzureFirewallNetworkRule","enabled":true},{"category":"AzureFirewallDnsProxy","enabled":true}]' \
    --metrics '[{"category":"AllMetrics","enabled":true}]' \
    --workspace $WORKSPACE_ID

# 4. Configure diagnostic settings for Key Vault (if you've created one)
KV_ID=$(az keyvault show --name security-keyvault --resource-group SECURITY-TOOLS-RG --query id -o tsv 2>/dev/null)

if [ ! -z "$KV_ID" ]; then
    az monitor diagnostic-settings create \
        --name iso27001-kv-diag \
        --resource $KV_ID \
        --logs '[{"category":"AuditEvent","enabled":true},{"category":"AzurePolicyEvaluationDetails","enabled":true}]' \
        --metrics '[{"category":"AllMetrics","enabled":true}]' \
        --workspace $WORKSPACE_ID
fi

# 5. Create diagnostic setting for virtual machine
VM_ID=$(az vm show --resource-group SECURITY-TOOLS-RG --name security-monitor-vm --query id -o tsv 2>/dev/null)

if [ ! -z "$VM_ID" ]; then
    az monitor diagnostic-settings create \
        --name iso27001-vm-diag \
        --resource $VM_ID \
        --metrics '[{"category":"AllMetrics","enabled":true}]' \
        --workspace $WORKSPACE_ID
fi