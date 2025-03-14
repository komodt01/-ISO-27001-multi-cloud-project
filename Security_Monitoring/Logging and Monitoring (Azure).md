# Create Log Analytics workspace
az monitor log-analytics workspace create \
    --resource-group monitoring-rg \
    --workspace-name security-workspace \
    --location eastus

# Enable diagnostic settings for key resources
az monitor diagnostic-settings create \
    --name security-diag \
    --resource /subscriptions/SUBSCRIPTION-ID/resourceGroups/rg-name/providers/Microsoft.Storage/storageAccounts/storage-name \
    --workspace /subscriptions/SUBSCRIPTION-ID/resourceGroups/monitoring-rg/providers/Microsoft.OperationalInsights/workspaces/security-workspace \
    --logs '[{"category":"StorageRead","enabled":true},{"category":"StorageWrite","enabled":true}]'

# Create alert rule
az monitor alert create \
    --name "Sensitive-Data-Access" \
    --resource-group monitoring-rg \
    --target /subscriptions/SUBSCRIPTION-ID/resourceGroups/rg-name/providers/Microsoft.Storage/storageAccounts/storage-name \
    --condition "Total Requests > 100" \
    --description "Alert when sensitive data is accessed"